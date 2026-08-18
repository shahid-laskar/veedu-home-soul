# FIRDAUS WAVE 2 — SELECTIVE LOVABLE UX INTEGRATION

PRIMARY:
~/firdous/veedu-home-soul

DONOR:
~/firdous/firdaus-daily-joy

The audit is complete.

FINAL AUDIT VERDICT:
READY FOR SELECTIVE WAVE 2 UX INTEGRATION

DO NOT perform a broad repository integration.

DO NOT copy the donor repository wholesale.

DO NOT modify any domain engine.

The donor contains only the missing Wave 2 presentation layer.

==================================================
APPROVED CHANGES
==================================================

CREATE:

1. src/components/family/signals.tsx
2. src/routes/plan.tsx

MODIFY:

3. src/components/veedu/bento.tsx
4. src/routes/index.tsx

AUTO-GENERATE:

5. src/routeTree.gen.ts

==================================================
1. PORT signals.tsx
==================================================

Copy/adapt the donor:

src/components/family/signals.tsx

into:

src/components/family/signals.tsx

It contains presentation components such as:

- StatusPill
- WorkloadRow
- FairnessBanner
- WorkloadPanel
- ConflictCard
- Note

These must consume the existing production domain types:

- HouseholdWorkloadSummary
- MemberWorkload
- FairnessSignal
- ConflictSignal
- WeeklyPlanningStepSummary

DO NOT recreate any calculations.

DO NOT create any new workload/conflict logic.

Preserve Calm and Vibrant compatibility.

==================================================
2. PORT plan.tsx
==================================================

Copy/adapt the donor:

src/routes/plan.tsx

into:

src/routes/plan.tsx

The page must use the EXISTING production:

- buildWeeklyPlan()
- WeeklyPlanProposal
- stageTaskAssignment()
- stageRoutineOwner()
- stageMeal()
- commitWeeklyPlanProposal()
- discard proposal
- FamilyMember
- TaskRecord
- Routine
- CalEventRecord
- meals

Do NOT replace or duplicate weekly-planning.ts.

==================================================
3. VERIFY ALL 8 PLANNING STEPS
==================================================

The UI should expose:

1. Review Previous Week
2. Upcoming Fixed Events
3. Routines
4. Responsibility Assignment
5. Conflict Resolution
6. Workload Review
7. Meal Planning
8. Approval

The UI must use the existing step summaries from weekly-planning.ts.

==================================================
4. PROPOSAL / DRAFT
==================================================

The UI may keep an in-memory / existing store representation of the proposal as implemented by the donor, but ensure:

- proposal changes remain staged
- real tasks/routines/meals are NOT mutated before approval
- discard removes the draft
- approval commits through the existing production function
- repeated approval is idempotent

Do not introduce a second proposal model.

If donor state implementation duplicates a type already exported by weekly-planning.ts, use the production type.

==================================================
5. MEMBER PERSPECTIVE
==================================================

Use the existing:

useSelectedMember()

and FamilyMember data.

The planner must support:

- Household
- Individual member
- Child

Pass memberId into:

buildWeeklyPlan()

and use the existing child sanitization.

Do not create another member selector implementation if the existing selector can be reused.

==================================================
6. CONFLICT PRESENTATION
==================================================

ConflictCard must consume:

ConflictSignal

directly.

Use:

- hard_conflict
- soft_conflict
- overload
- severity
- affectedItems
- explanation
- suggestedAction

Do not create a second conflict taxonomy.

Do not auto-resolve conflicts.

==================================================
7. WORKLOAD PRESENTATION
==================================================

WorkloadPanel and WorkloadRow must consume:

HouseholdWorkloadSummary
MemberWorkload

Use the existing qualitative model:

light
balanced
heavier
unclear

DO NOT display:

- scores
- rankings
- leaderboards
- points
- competitive comparisons

Do not fabricate duration when duration is unknown.

Respect child workload sanitization.

==================================================
8. ROUTINES
==================================================

Use the real Routine and RoutineStep models.

Allow the UI to stage:

- routine ownership
- member assignment

without mutating the real routine until approval.

Preserve:

- prayer-relative scheduling
- step-level attribution
- recurrence
- completion state

==================================================
9. MEALS
==================================================

Use the existing meals store and stageMeal() mechanism.

Do not create a new meal planner.

Do not create duplicate meal records.

==================================================
10. HOME QUICK ACTION
==================================================

Modify:

src/routes/index.tsx

Add a Quick Action:

"Plan the week"

linking to:

/plan

Preserve the existing Home navigation and Daily Surface.

Do not redesign the Home screen.

==================================================
11. BENTO LINK TYPE
==================================================

Modify:

src/components/veedu/bento.tsx

Extend the existing LinkTo union to include:

"/plan"

Do not change unrelated link behavior.

==================================================
12. ROUTER
==================================================

Do not manually construct route-tree architecture.

Add:

src/routes/plan.tsx

then regenerate the existing TanStack route tree through the normal build/tooling.

Do not hand-edit generated route metadata unless the project requires it.

==================================================
13. CALM / VIBRANT
==================================================

The new `/plan` experience must work with both:

Calm
Vibrant

Use the existing:

- shell
- page hero
- primitives
- tokens
- experience system

Do NOT create a third theme.

Do NOT add theme-specific domain logic.

==================================================
14. DO NOT TOUCH
==================================================

DO NOT modify:

src/lib/*
especially:

- weekly-planning.ts
- conflict-detector.ts
- workload-intelligence.ts
- rhythm-engine.ts
- routine-engine.ts
- daily-surface.ts
- family-model.ts
- recurrence.ts
- store.ts

Also do not modify:

- styles.css
- package.json
- vite.config.ts
- tsconfig.json
- existing Deen/Budget/Insights components
- existing Wave 1 components

unless required only to resolve a TypeScript import issue.

==================================================
15. NO WHOLE-FILE REPLACEMENTS
==================================================

For existing files:

- index.tsx
- bento.tsx

make surgical changes.

Do not replace the production files with donor files.

==================================================
16. VALIDATION
==================================================

Run:

npx tsx --test src/lib/*.test.ts
npx tsc --noEmit
npm run build

Expected:

- all existing 176 tests pass
- no TypeScript errors
- production build passes

Then manually verify:

### Weekly Planning
- open /plan
- week navigation
- member perspective
- previous week
- events
- routines
- assignments
- conflicts
- workload
- meals
- approval
- discard
- repeated approval

### Family
- household
- adult member
- child

### Experience
- Calm
- Vibrant

### Home
- "Plan the week" quick action
- existing navigation still works

==================================================
17. GIT
==================================================

Work on:

feature/wave2-lovable-integration

Commit:

feat: integrate wave 2 family planning ux

Do NOT merge to main automatically.

==================================================
FINAL REPORT
==================================================

Return:

### Files Added
### Files Modified
### Weekly Planning UX
### Proposal/Draft Integration
### Member Perspective
### Conflict UX
### Workload UX
### Routine UX
### Meal UX
### Home Quick Action
### Calm
### Vibrant
### Tests
### Typecheck
### Build
### Manual Validation
### Domain Files Confirmed Untouched
### Commit
### Remaining Issues

STOP.