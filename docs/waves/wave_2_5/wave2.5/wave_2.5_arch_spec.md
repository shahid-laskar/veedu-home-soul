# HOUSEHOLD IDENTITY & SHARING SPECIFICATION

## 1. Current Auth Architecture
- **Authentication**: Relies on Supabase Auth.
- **Session State**: Managed via Supabase client, hydrating local session on initialization.
- **Data Synchronization**: A generic `user_data` table (`user_id`, `key`, `value`, `updated_at`) acts as a cloud key-value store. The client reads/writes monolithic JSON strings for each key.
- **Isolation**: Currently, all data is rigidly scoped to `user_id`. There is no mechanism for cross-user sharing at the database level.

## 2. Current Family Architecture
- **Client-Side Model**: Household members are defined purely in client state (`veedu:family` in localStorage), containing an array of `FamilyMember` objects (roles: `admin`, `member`, `child`).
- **Entity Linking**: Tasks, Routines, and Events reference family members via `assigneeId` or `assignedTo` (which maps to `FamilyMember.id`).
- **Missing Link**: `FamilyMember` profiles are disconnected from real Supabase Auth accounts. A spouse added as a `FamilyMember` cannot log in on their own device and see the data.

## 3. Data Ownership Audit
Based on current prototype keys, data must be classified to determine synchronization boundaries:

| Domain | Current Key | Classification |
|--------|-------------|----------------|
| Profile Info | `profile` | Personal |
| Family Definitions | `family`, `kids` | Household |
| Tasks / Chores | `tasks` | Household (with member assignments) |
| Groceries & Recipes | `grocery`, `recipes` | Household |
| Meals | `meals`, `mealHistory` | Household |
| Calendar Events | `events` | Household |
| Budget & Limits | `expenses`, `limits` | Household |
| Notes | `notesList` | Personal (unless shared explicitly, default to Personal) |
| Health & Habits | `health`, `habits`, `workouts` | Personal |
| Deen / Ibadah | `salah`, `fasting`, `quran-*` | Personal |
| Journal & Reflections| `journal`, `checkins`, `deeds`| Personal |
| Routines | `routines` | Household |
| Notifications | `notifPrefs`, `reminders` | Personal |

## 4. Household Model
Firdaus requires a lightweight tenant record to group users and data.
- **Model**: `households` (Simple Tenant)
- **Fields**: `id`, `name`, `owner_id` (references `auth.users`), `created_at`.
- **Reasoning**: An "Organization" model is too heavy and B2B-focused. A `household` tenant accurately reflects the consumer domain. 

## 5. Membership Model
To bridge Auth Users and `FamilyMember` profiles:
- **Model**: `household_members`
- **Fields**: `id`, `household_id`, `user_id` (nullable), `family_member_id` (nullable), `role`, `status` (active, invited, pending), `created_at`.
- **Reasoning**: A junction table is strictly necessary. It links the authenticated `user_id` to the application-level `family_member_id` so that historical assignments (e.g., tasks assigned to "Dad") remain intact even if the Supabase Auth account is unlinked later.

## 6. FamilyMember ↔ Auth User Linking
- **Creation**: When a user adds a wife/child in the app, a `FamilyMember` is created (unlinked).
- **Invitation**: Inviting the member creates a `household_members` record in `pending` state.
- **Acceptance**: The user accepts the invite; their `auth.users.id` is populated in `household_members.user_id`.
- **Revocation**: If a member leaves, `user_id` is nulled, but the `FamilyMember` profile remains so historical tasks and routines don't break.

## 7. Existing User Migration
**Strictly Additive**:
1. **Migration 1**: For every existing `auth.users` with data in `user_data`, auto-generate a `households` record where they are the owner.
2. **Migration 2**: Create a `household_members` active link for them.
3. **Migration 3**: In the `user_data` table, add `household_id` and `scope` ('personal' | 'household') columns. Default existing household-domain keys to the new `household_id`, and personal-domain keys to `user_id` only.

## 8. Invitation / Join Flow
- **Initiation**: Owner clicks "Invite" -> enters email.
- **Backend Execution**: Due to Supabase security (admin ops require Service Role), the client calls a **Supabase Edge Function** (`invite_member`).
- **Processing**: The Edge Function safely uses `supabase.auth.admin.inviteUserByEmail` and creates the `pending` membership record.
- **Completion**: Invitee clicks the magic link, sets password, and is automatically routed to the household context.

## 9. Existing Account Invitation
If an invitee already has a Firdaus account:
- The Edge Function detects the existing `auth.users` record.
- Instead of a generic Auth invite, it creates a `pending` membership.
- Next time the user opens Firdaus, they see an in-app notification: "You have been invited to join [Owner]'s Household."
- Accepting links their account without duplicating identities.

## 10. Child Profile Model
- Children remain purely as `FamilyMember` records with `linked_user_id = null`.
- They interact through parental devices or shared family tablets using the `SELECTED_MEMBER_KEY` perspective switcher.
- **Implications**: Child data is fundamentally Household data. Privacy boundaries for kids do not exist against parents in this model.

## 11. Household Switching Decision
- **Recommendation**: Support **One Account → Multiple Households**, but optimize UI for a single primary household.
- **Reasoning**: Life happens (e.g., divorced co-parents managing two homes, or users managing an elderly parent's household). Allowing switching prevents users from needing multiple email addresses. The UI should hide the switcher if the user only belongs to one household.

## 12. Personal vs Household Data Matrix

| Domain | Owner | Access / Visibility |
|--------|-------|---------------------|
| Family Profiles | Household | All household members |
| Routines / Tasks| Household | All household members (filtered by assignment) |
| Groceries / Meals| Household | All household members |
| Calendar Events | Household | All household members |
| Child Profiles | Household | All household members (primarily parents) |
| Budget / Expenses| Household | All household members (consider adult-only role later) |
| Notes | Personal | Only Auth User |
| Quran / Hifz / Salah | Personal | Only Auth User |
| Habits / Health | Personal | Only Auth User |
| Journal / Reflections | Personal | Only Auth User |
| Personal Reminders| Personal | Only Auth User |

*(Note: Data models like `user_data` will need a `scope` column to differentiate).*

## 13. RLS Strategy
- **Household Data**:
  ```sql
  CREATE POLICY "Household access" ON user_data
  FOR ALL USING (
    scope = 'household' AND 
    household_id IN (SELECT household_id FROM household_members WHERE user_id = auth.uid() AND status = 'active')
  );
  ```
- **Personal Data**:
  ```sql
  CREATE POLICY "Personal access" ON user_data
  FOR ALL USING (
    scope = 'personal' AND user_id = auth.uid()
  );
  ```

## 14. Sync / Conflict Strategy
- **Current Vulnerability**: The app stores entire collections (e.g., all `tasks`) as monolithic JSON strings under a single key. A naive "last-write-wins" on the `tasks` key across two devices will result in data loss (e.g., Wife adds a task, Husband adds a task while offline, whoever syncs last overwrites the other).
- **Recommendation for MVP**: Do **NOT** rewrite the entire app to SQLite/RxDB. Instead, implement a **Smart Merge Sync** for known array keys (`tasks`, `grocery`, etc.) in `store.ts` OR migrate local storage to granular keys (`task:1`, `task:2`) so that last-write-wins happens at the row/item level rather than the collection level.

## 15. Offline Strategy
- **Prefixing**: LocalStorage keys must become scope-aware (e.g., `veedu:hh_123:tasks` vs `veedu:usr_456:habits`).
- **Pending Writes**: The app continues writing to local storage. 
- **Context Switching**: If a user switches households while offline, the app simply loads the other household's local prefix. If it's not cached, it shows an offline empty state.
- **Logout**: Clears all local storage to prevent leaking sensitive household data to subsequent users of the device.

## 16. Migration Plan
1. **DB Level**: Add `household_id` and `scope` to `user_data`.
2. **Data Level**: Auto-migrate existing single-user data to a new household.
3. **Client Level**: Update `store.ts` to append household prefixes to keys, migrating legacy `veedu:tasks` to `veedu:<household_id>:tasks`.

## 17. Privacy Model
- **Strict Isolation**: Personal data (Journal, Health, Quran, Habits, Notes) is fundamentally tied to `auth.users.id` and NEVER `household_id`.
- **Boundary Guarantee**: Even if two users join a household, their personal data queries will fail RLS against the other user. 

## 18. UX Flows
- **Existing User**: Signs in → Auto-migrated to Household Owner → Data looks exactly the same.
- **New User**: Signs up → Household automatically created → Proceeds to Onboarding.
- **Invite Adult**: Settings → Add Member → "Wife" → Enter Email → Invitee gets Magic Link → Clicks link → Logs in → Sees Shared Household.
- **Add Child**: Settings → Add Member → "Child" (No email prompt) → Profile appears in family list.

## 19. Domain/API Changes
- **New Files**:
  - `src/lib/household-model.ts`: Manages current active household context and membership state.
  - `supabase/functions/invite_member/index.ts`: Edge function for secure invites.
- **Modified Files**:
  - `src/lib/store.ts`: Must become aware of `household_id` to partition local storage keys and sync payloads.
  - `src/lib/supabase.ts`: Add RPC/Edge Function bindings for invitations.

## 20. Database Schema Proposal
```sql
CREATE TABLE households (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  name text,
  owner_id uuid REFERENCES auth.users(id),
  created_at timestamptz DEFAULT now()
);

CREATE TABLE household_members (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  household_id uuid REFERENCES households(id),
  user_id uuid REFERENCES auth.users(id),
  family_member_id text, -- Matches the local nano-id
  role text,
  status text CHECK (status IN ('active', 'pending', 'invited')),
  created_at timestamptz DEFAULT now()
);

-- Alter existing user_data
ALTER TABLE user_data 
ADD COLUMN household_id uuid REFERENCES households(id),
ADD COLUMN scope text CHECK (scope IN ('personal', 'household')) DEFAULT 'personal';
```

## 21. Security Risks
- **Monolithic Key Overwrites**: High risk of data loss on array-based keys if naive sync is used for multi-user.
- **Service Role Leaks**: Attempting to use `auth.admin` in the browser will leak the service role key. Edge Functions are mandatory.
- **Stale Cache**: Local storage must be thoroughly purged on logout, otherwise private household data remains accessible to anyone opening the browser.

## 22. Implementation Waves
**Minimal Sequence:**
- **H1**: Database Schema & RLS updates. Alter `user_data` to support `household_id`.
- **H2**: LocalStorage prefixing and `store.ts` sync adjustments for scopes.
- **H3**: Backend migrations for existing users.
- **H4**: Edge Function for invitations.
- **H5**: UI implementation (Membership management & Invites).
- **H6**: Multi-device conflict resolution (Smart Merge for arrays).

## 23. What NOT To Build
- Do NOT build an "Organizations" RBAC model. Keep roles simple (Admin / Member).
- Do NOT build granular per-task sharing (e.g., "share this specific task with user Y"). Everything is either Household-wide or Personal.
- Do NOT build custom real-time socket sync. Rely on Supabase standard sync pulling/pushing.

## 24. Testing Strategy
- **Offline Integrity**: Kill network, write data as User A, kill network, write data as User B. Bring both online and verify merge behavior.
- **RLS Verification**: Query `user_data` as User A and assert 0 rows returned for User B's personal data.
- **Logout Isolation**: Assert `localStorage` is empty post-logout.

## 25. Rollback Strategy
- Keep legacy `user_id` as the primary key constraint alongside `key` during H1/H2 so that reverting the UI doesn't break legacy sync.
- Non-destructive DB migrations: add columns instead of replacing them.

## 26. Final Architecture Diagram
```mermaid
graph TD
    Auth[Supabase Auth User] -->|owner| HH[Household]
    Auth -->|personal| PD[Personal Data (scope: personal)]
    
    HH -->|household| HD[Household Data (scope: household)]
    
    Auth -->|active| HM[Household Member]
    HM --> HH
    
    FM[FamilyMember Profile] --> HM
    FM -->|assignedTo| HD
```

## 27. Recommendation
**READY AFTER ARCHITECTURE DECISION**

**Key Decision Required**: The current monolithic JSON key syncing in `store.ts` will critically fail in a multi-user household (last-write-wins on arrays causes data loss). Before implementing H1, a decision must be made whether to (A) implement custom JSON merging in `store.ts` for arrays, or (B) refactor local storage to use granular row-based keys (e.g. `veedu:task:123` instead of `veedu:tasks`).
