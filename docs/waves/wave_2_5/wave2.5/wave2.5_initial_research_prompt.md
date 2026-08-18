# FIRDAUS WAVE 2.X — HOUSEHOLD IDENTITY & SHARING
# RESEARCH / ARCHITECTURE SPECIFICATION ONLY

Repository:
~/firdous/veedu-home-soul

DO NOT MODIFY CODE.

Wave 2 core is complete:
- Family Responsibility Model
- Per-Member Daily Surface
- Conflict Detection
- Workload Intelligence
- Family Weekly Planning
- Calm/Vibrant
- local-first architecture
- Supabase sync
- personal-account onboarding

We now need to design the missing HOUSEHOLD IDENTITY & SHARING layer.

==================================================
CURRENT PRODUCT REALITY
==================================================

Today:

Supabase Auth Account
        ↓
personal user/session
        ↓
existing Firdaus local/synced data

And separately:

FamilyMember
        ↓
profile within that user's household
        ↓
may or may not represent a real authenticated person

This means a spouse/another adult can currently exist as a FamilyMember
without having a separate login.

We now need to support:

1. household-only profiles
2. invited household adults with their own accounts
3. linked account ↔ FamilyMember
4. shared household data
5. personal/private data
6. multi-device access
7. secure household-scoped authorization

==================================================
OBJECTIVE
==================================================

Design the architecture for:

ACCOUNT
    ↓
HOUSEHOLD
    ↓
HOUSEHOLD MEMBERSHIP
    ↓
FAMILY MEMBER PROFILE

while preserving:

- existing users
- localStorage
- offline mode
- existing Phase 0–4
- Wave 1
- Wave 2
- Calm/Vibrant
- current Supabase sync

Do NOT implement yet.

==================================================
1. AUDIT CURRENT AUTH
==================================================

Inspect:

- Supabase client
- auth/session handling
- onboarding
- user profile model
- login
- signup
- signout
- session hydration
- `user_data` schema/usage
- current Supabase synchronization
- RLS policies if present
- migrations
- any existing user/profile tables

Determine exactly how current authenticated user identity is stored.

==================================================
2. AUDIT CURRENT FAMILY MODEL
==================================================

Inspect:

- family-model.ts
- FamilyMember
- roles
- member IDs
- selected member
- assignment model
- routines
- tasks
- calendar
- weekly planning

Determine which information belongs to:

PERSON
vs.
HOUSEHOLD
vs.
FAMILY MEMBER PROFILE

Do not assume they are the same.

==================================================
3. AUDIT CURRENT DATA OWNERSHIP
==================================================

Enumerate all major data domains:

- profile
- tasks
- routines
- calendar
- meals
- grocery
- kids
- family
- budget
- notes
- reminders
- habits
- health
- salah
- fasting
- Quran
- Hifz
- Ramadan
- goals if present
- insights
- reflections if present
- weekly planning

For each classify:

PERSONAL
HOUSEHOLD
FAMILY-MEMBER PROFILE
DERIVED

For example:

Private reflection → personal
Family routine → household
Child profile → family-member profile
Household grocery → household
Personal Quran progress → personal/member
Shared calendar event → household
Personal habit → personal/member

Do not invent classifications without inspecting current behavior.

==================================================
4. EXISTING USER MIGRATION
==================================================

This is critical.

Determine how an existing single-account user becomes:

Household Owner
    +
their existing FamilyMember profile
    +
their existing household data

without breaking:

- local data
- Supabase data
- offline data
- existing IDs
- exports/imports

Propose an additive migration.

No destructive migration.

==================================================
5. HOUSEHOLD MODEL
==================================================

Research whether Firdaus needs:

households
or
organizations
or
a lightweight tenant record

Recommend the simplest appropriate model.

Define:

- household id
- name
- owner
- created_at
- status

Explain why.

==================================================
6. MEMBERSHIP MODEL
==================================================

Design the relationship:

User
   ↕
Household Membership
   ↕
FamilyMember

Support:

ACTIVE
INVITED
REMOVED
PENDING

Determine whether a membership should reference both:

user_id
family_member_id

and why.

Ensure one real adult account can map to one FamilyMember within a household.

==================================================
7. CHILD MODEL
==================================================

A child should NOT need a Supabase Auth account unless explicitly invited/allowed later.

Support:

FamilyMember
linked_user_id = null

while adult member can have:

linked_user_id = auth.users.id

Determine implications for:

- login
- Daily Surface
- assignments
- Quran/Hifz
- child privacy
- workload
- weekly planning

==================================================
8. INVITATION FLOW
==================================================

Design:

Owner
  ↓
Add Family Member
  ↓
"Invite to Firdaus"
  ↓
email/OTP/link
  ↓
new or existing Auth user
  ↓
accept invitation
  ↓
link account to FamilyMember
  ↓
join household

Research the safest Supabase implementation.

Supabase Auth invitation/admin operations are server-side.

Do NOT expose service-role credentials in the browser.

Determine whether an Edge Function or equivalent trusted server action is appropriate.

==================================================
9. EXISTING USER ALREADY HAS ACCOUNT
==================================================

Design the case:

Wife already has a separate Firdaus account.

Owner invites email.

System should:

- not create duplicate identity
- not create duplicate FamilyMember
- allow linking the existing account to the household after acceptance

Determine the safest flow.

==================================================
10. HOUSEHOLD SWITCHING
==================================================

Determine whether Firdaus should support:

- one account → one household
or
- one account → multiple households

Recommend the simplest model for Firdaus.

Do not introduce multi-household complexity unless justified.

==================================================
11. AUTHORIZATION
==================================================

Design authorization around:

authenticated user
        ↓
household membership
        ↓
allowed household data

Use Supabase RLS as the database authorization boundary.

Do not rely only on React/UI filtering.

Determine the required RLS policy strategy.

Do NOT write migrations yet.

==================================================
12. PERSONAL VS HOUSEHOLD DATA
==================================================

Create a proposed ownership matrix.

Example:

| Domain | Owner |
|--------|-------|
| Family members | Household |
| Household routines | Household |
| Shared tasks | Household |
| Personal task | Person/member |
| Household calendar | Household |
| Personal notes | Person |
| Grocery | Household |
| Meals | Household |
| Budget | TBD |
| Quran progress | Person/member |
| Hifz | Person/member |
| Reflections | Person |
| Reminders | Person/member |
| Child profile | Household |
| Weekly family plan | Household |

But BASE THE FINAL MATRIX ON ACTUAL CODE AUDIT.

==================================================
13. LOCAL-FIRST / OFFLINE
==================================================

This is critical.

Determine how household-scoped data can continue to work when offline.

Questions:

- What is locally cached?
- How is household identity cached?
- How are pending writes scoped?
- What happens when membership changes?
- What happens after logout?
- How are stale household records cleared?
- How do two devices converge?

Do not recommend a design that breaks the existing local-first architecture.

==================================================
14. SYNC / CONFLICTS
==================================================

The current sync is simple.

Determine how household collaboration changes this.

Need to consider:

Device A
  ↓
change

Device B
  ↓
change

both sync

Determine whether:

- last-write-wins
- per-record timestamps
- version numbers
- optimistic concurrency

is appropriate for the MVP.

Do NOT overengineer.

==================================================
15. ACCOUNT / FAMILY MEMBER LINKING
==================================================

Define lifecycle:

FamilyMember created
    ↓
unlinked profile

Invitation sent
    ↓
pending

Invitation accepted
    ↓
linked account

Membership revoked
    ↓
account removed from household

Determine what happens to historical assignments.

Never delete historical ownership simply because membership ends.

==================================================
16. SECURITY
==================================================

Explicitly audit:

- RLS
- user_id
- household_id
- family_member_id
- child data
- budget
- private notes
- reflections
- Quran/Hifz
- personal health data

Determine which information must NEVER become automatically shared merely because two users join a household.

==================================================
17. DATA MIGRATION
==================================================

Propose:

Migration 1
Existing user → household owner

Migration 2
Existing FamilyMember → linked profile where appropriate

Migration 3
Existing `user_data` → household/personal ownership

Migration 4
RLS

All migrations must be backward-compatible.

==================================================
18. PRODUCT / UX
==================================================

Design the user-facing conceptual flows:

### Existing user
Sign in
→ Household automatically exists
→ See existing data

### New user
Sign up
→ Create household
→ Add members

### Add household profile
Add wife/child
→ profile only

### Invite adult
Add wife
→ Invite
→ accept
→ link existing/new account

### Switch perspective
Household
↔
Member

### Leave household
Adult member leaves
→ historical data preserved
→ access removed

Do NOT design final visuals.

==================================================
19. PRIVACY
==================================================

Define clear boundaries for:

Personal
Household
Child

Especially:

- budget
- private notes
- health
- reflections
- Quran progress
- Hifz
- personal goals

==================================================
20. ARCHITECTURE
==================================================

Recommend exact domain additions.

Possible candidates:

household-model.ts
membership-model.ts
household-sync.ts

But do not invent files unless justified.

Explain how this integrates with:

- family-model.ts
- store.ts
- supabase.ts
- Daily Surface
- weekly planning
- auth
- backup/export

==================================================
21. SUPABASE RESEARCH
==================================================

Use current official Supabase documentation where necessary.

Confirm current recommended patterns for:

- auth.users references
- RLS
- invitation flow
- server-side admin auth operations
- Edge Functions where appropriate

Clearly distinguish:
SOURCE
RECOMMENDATION
INFERENCE

Never expose service-role credentials client-side.

==================================================
22. FINAL DELIVERABLE
==================================================

Return:

# HOUSEHOLD IDENTITY & SHARING SPECIFICATION

## 1. Current Auth Architecture

## 2. Current Family Architecture

## 3. Data Ownership Audit

## 4. Household Model

## 5. Membership Model

## 6. FamilyMember ↔ Auth User Linking

## 7. Existing User Migration

## 8. Invitation / Join Flow

## 9. Existing Account Invitation

## 10. Child Profile Model

## 11. Household Switching Decision

## 12. Personal vs Household Data Matrix

## 13. RLS Strategy

## 14. Sync / Conflict Strategy

## 15. Offline Strategy

## 16. Migration Plan

## 17. Privacy Model

## 18. UX Flows

## 19. Domain/API Changes

## 20. Database Schema Proposal

## 21. Security Risks

## 22. Implementation Waves

Recommend a minimal sequence such as:

H1 Identity / Household foundation
H2 Membership / linking
H3 Invitations
H4 Household-scoped sync/RLS
H5 Migration
H6 Multi-device validation

Do not over-split unnecessarily.

## 23. What NOT To Build

## 24. Testing Strategy

## 25. Rollback Strategy

## 26. Final Architecture Diagram

## 27. Recommendation

Choose:

READY FOR IMPLEMENTATION
READY AFTER ARCHITECTURE DECISION
NOT READY

==================================================
IMPORTANT
==================================================

DO NOT MODIFY CODE.
DO NOT create migrations.
DO NOT change Supabase schema.
DO NOT implement invitation flow.

This task is specification/research only.