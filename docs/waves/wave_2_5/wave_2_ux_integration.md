# FIRDAUS — WAVE 2 LOVABLE UX AUDIT
# AUDIT ONLY — DO NOT MODIFY PRIMARY REPOSITORY

We have completed Wave 2 engineering and independently validated the domain architecture.

We now have a NEW Lovable UX donor repository containing the Wave 2 Family Operating System UX.

PRIMARY / SOURCE OF TRUTH
~/firdous/veedu-home-soul

LOVABLE DONOR
~/firdous/firdaus-daily-joy

The donor repository has just been synchronized from its latest Lovable `main`.

IMPORTANT:

DO NOT MODIFY `veedu-home-soul` during this audit.

Do NOT port code yet.

Do NOT create commits.

The purpose of this task is to compare the CURRENT Lovable donor against the CURRENT production repository and produce an exact selective integration plan.

==================================================
CURRENT FIRDAUS STATE
==================================================

The production repository already contains:

PHASE 0–4
- Tasks
- Notes
- Calendar
- Meals
- Grocery
- Kids
- Budget
- Deen
- Quran
- Hifz
- Ramadan
- Insights
- Reminders
- PWA
- Intelligent Daily Surface
- Calm/Vibrant experience system

WAVE 1
- Prayer Rhythm Engine
- Prayer-aware task scheduling
- Family Routines
- Rhythm → DayRhythm → Daily Surface
- Routine authoring
- Prayer-relative task scheduling
- Kids and Routines as separate destinations
- Calm + Vibrant presentation

WAVE 2
- Family responsibility model
- member roles
- assignedTo
- per-member Daily Surface
- household/member perspectives
- child privacy filtering
- schedule conflict detection
- workload/fairness intelligence
- family weekly planning
- proposal/draft model
- conflict preview
- workload preview
- meal planning context
- approval/commit
- idempotent planning commit

Wave 2 currently has:
- 176 passing tests
- zero TypeScript errors
- successful production build
- independent architecture approval after fixes

==================================================
PRIMARY ARCHITECTURE
==================================================

The canonical domain flow is:

Family Model
    ↓
Member Perspective
    ↓
Assigned Tasks / Routines / Events
    ↓
Rhythm Engine
    ↓
Daily Surface
    ↓
Conflict Detection
    ↓
Workload Intelligence
    ↓
Weekly Planning
    ↓
User Approval
    ↓
Existing Domain State

The domain layer is source of truth.

The Lovable donor is UI/presentation only.

==================================================
AUDIT BOTH REPOSITORIES
==================================================

PRIMARY:

~/firdous/veedu-home-soul

Inspect:

- package.json
- src/routes
- src/components/home
- src/components/veedu
- src/components/family
- src/lib
- experiences.ts
- theme-provider.tsx
- styles.css
- Daily Surface
- member selector
- routines
- tasks
- calendar
- weekly review
- existing Calm/Vibrant components

DONOR:

~/firdous/firdaus-daily-joy

Inspect:

- package.json
- routes
- components
- styles
- theme tokens
- member/family UX
- weekly planning UX
- workload presentation
- conflict presentation
- routines
- tasks
- navigation
- modal/sheet components
- wizard/stepper components
- animations
- assets
- providers
- state
- mock data
- APIs
- dependencies

==================================================
BUILD A DIFFERENCE MATRIX
==================================================

For every meaningful donor artifact classify:

A. ALREADY IN PRIMARY
B. MISSING — SAFE TO PORT
C. PRESENT — NEEDS ADAPTATION
D. INTENTIONALLY DIFFERENT
E. MUST NOT PORT

For each:

Donor file/component
→ Primary equivalent
→ Classification
→ Action
→ Reason

==================================================
FOCUS AREA 1 — FAMILY MEMBER SELECTOR
==================================================

Compare donor vs primary.

The primary already supports:

- Household perspective
- member perspective
- child perspective
- persisted selected member
- family-model filtering
- Calm/Vibrant presentation

Determine exactly what donor UX adds.

Possible donor improvements:
- selector visuals
- avatars
- member chips
- role indicators
- responsive behavior

DO NOT import donor state management.

==================================================
FOCUS AREA 2 — FAMILY DAILY SURFACE
==================================================

Compare the donor Family/Today experience with the primary Daily Surface.

PRIMARY IS AUTHORITATIVE.

The donor must NOT replace:

buildDailyThread()
buildDayRhythm()
Rhythm Engine
conflict detector
workload engine

Determine which donor components are merely presentation improvements.

==================================================
FOCUS AREA 3 — WEEKLY FAMILY PLANNING
==================================================

This is the highest-priority audit.

The primary already has:

weekly-planning.ts
WeeklyPlanProposal
conflict preview
workload preview
meal context
approval/commit
idempotent commit

Determine whether the donor contains UI for:

1. Previous-week review
2. Upcoming fixed events
3. Routines
4. Responsibility assignment
5. Conflict resolution
6. Workload review
7. Meal planning
8. Approval

Map each donor component to the existing real domain API.

Do NOT recommend importing donor planning logic.

==================================================
FOCUS AREA 4 — PROPOSAL / DRAFT UX
==================================================

The primary planning engine uses an in-memory proposal/draft.

The donor may have its own local state.

Determine:

- what UI state can be reused
- what donor state must be removed
- how donor components should consume WeeklyPlanProposal
- whether the donor assumes immediate persistence

Any donor flow that writes directly to localStorage/state during planning must be adapted to the primary draft/approval architecture.

==================================================
FOCUS AREA 5 — CONFLICT UX
==================================================

Determine how donor presents:

- hard conflicts
- soft conflicts
- overload

Compare against the primary ConflictSignal model.

Identify whether the donor:
- duplicates calculations
- has its own severity model
- assumes arbitrary conflict types
- auto-resolves conflicts

Only presentation should be ported.

==================================================
FOCUS AREA 6 — WORKLOAD UX
==================================================

The primary workload engine deliberately avoids:

- scores
- rankings
- leaderboards
- gamification
- false precision

Inspect donor UI for any such patterns.

If present:
- flag them
- do not port those patterns

Preferred language:

- light
- balanced
- carrying more
- shared
- unclear

Compare donor UX with:
HouseholdWorkloadSummary
MemberWorkload
qualitativeLoad

==================================================
FOCUS AREA 7 — ROUTINES
==================================================

The primary already has:

- Routine
- RoutineStep
- recurrence
- step assignments
- prayer-relative schedule
- routine progress
- completion/skip

Determine whether the donor adds:
- better routine authoring
- assignment UX
- progress visuals
- weekly planning presentation

Do not create another routine model.

==================================================
FOCUS AREA 8 — MEALS / GROCERY
==================================================

The primary weekly planner already consumes existing meal state.

Determine whether donor only presents:
- meal slots
- meal gaps
- grocery context

or whether it introduces a duplicate meal-planning system.

Only presentation improvements should be ported.

==================================================
FOCUS AREA 9 — CHILD UX
==================================================

The primary enforces child privacy.

Verify donor does not expose:
- adult workloads
- budget
- parent-only tasks
- private family information

Determine whether donor has useful child-specific presentation that can safely consume the sanitized primary data.

==================================================
FOCUS AREA 10 — CALM / VIBRANT
==================================================

The primary already has:

Calm
Vibrant

as a shared domain / dual presentation system.

Determine whether donor:
- extends existing experience primitives
- duplicates theme logic
- introduces another theme mechanism

Any donor theme implementation must be adapted to the existing experience architecture.

==================================================
FOCUS AREA 11 — ROUTING / NAVIGATION
==================================================

Compare:
- Home tabs
- Family navigation
- Weekly Planning entry point
- member selector
- Routines

Do NOT import donor routing.

Primary routing remains authoritative.

==================================================
FOCUS AREA 12 — DEPENDENCIES
==================================================

Compare package.json.

For every donor dependency:

- already present?
- genuinely needed?
- can existing dependency be reused?

Prefer zero new dependencies.

==================================================
FOCUS AREA 13 — MOCK / DONOR CONTAMINATION
==================================================

Search donor for:

- mock
- demo
- sample
- fake
- placeholder
- fake family members
- fake tasks
- fake workload
- fake conflicts
- fake weekly plans
- demo state
- donor-only providers
- donor API calls
- donor localStorage keys

Anything in this category must be rejected unless it is purely static presentation data and can be removed.

==================================================
FOCUS AREA 14 — RESPONSIVE / ACCESSIBILITY
==================================================

Audit the donor UX for:

- mobile
- tablet
- desktop
- keyboard
- touch targets
- focus
- reduced motion
- screen readers

Especially:
- weekly planning flow
- member selector
- conflict actions
- workload display
- approval screen

==================================================
CRITICAL RULE — NO WHOLE-FILE REPLACEMENTS
==================================================

Even during the eventual implementation:

DO NOT replace production files wholesale with donor files.

The final integration must be selective and surgical.

==================================================
OUTPUT
==================================================

DO NOT MODIFY ANY FILE.

Return:

# 1. Primary Repository State

# 2. Donor Repository State

# 3. Difference Matrix

# 4. Already Integrated

# 5. Missing UX

# 6. Partial / Needs Adaptation

# 7. Must NOT Be Ported

# 8. Family Selector UX

# 9. Family Daily Surface UX

# 10. Weekly Planning UX

# 11. Proposal/Draft UX

# 12. Conflict UX

# 13. Workload UX

# 14. Routine UX

# 15. Meal/Grocery UX

# 16. Child UX

# 17. Calm/Vibrant Compatibility

# 18. Navigation Differences

# 19. Dependency Differences

# 20. Mock / Foreign Architecture Rejection List

# 21. Data Contract Mapping

For each donor UI component specify which REAL primary data/API it must consume.

# 22. Exact Files to Port

# 23. Exact Files to Adapt

# 24. Files That Must Remain Untouched

# 25. Recommended Integration Order

# 26. Risks

# 27. Final Recommendation

Choose exactly one:

READY FOR SELECTIVE WAVE 2 UX INTEGRATION

NEEDS ARCHITECTURE DECISION

DO NOT INTEGRATE YET

STOP AFTER THE AUDIT.

DO NOT MODIFY CODE.