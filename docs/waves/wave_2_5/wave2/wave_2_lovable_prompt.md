# FIRDAUS WAVE 2 — FAMILY OPERATING SYSTEM UX

The Wave 2 engineering is COMPLETE and has passed an independent architecture gate
and final engineering polish.

The production repository already contains:

- Family Responsibility Model
- Member Perspective / filtering
- Prayer Rhythm Engine
- Prayer-aware Tasks
- Family Routines
- Daily Surface
- Conflict Detection
- Household Workload Intelligence
- Family Weekly Planning
- Proposal / draft model
- Approval / commit
- Calm experience
- Vibrant experience

DO NOT implement business logic.

DO NOT rebuild any domain engine.

DO NOT create new stores.

DO NOT create mock data.

DO NOT create a second planning system.

Your responsibility is to turn the existing Wave 2 functionality into a coherent,
premium, low-cognitive-load Family Operating System experience.

==================================================
PRODUCT IDEA
==================================================

Firdaus should help a household answer:

"How is our family doing today, and how should we prepare for the week?"

The experience should feel:

- calm
- capable
- private
- cooperative
- practical
- prayer-aware

It should NOT feel like:

- project management software
- a family leaderboard
- a surveillance dashboard
- a social network
- a spreadsheet
- a productivity scorecard

==================================================
1. FAMILY PERSPECTIVE SWITCHER
==================================================

Create/refine the member perspective selector.

Options:

Household
Family member 1
Family member 2
Child

The selector represents perspective only.

It is NOT:
- authentication
- account switching
- permission administration

The selected perspective must consume the existing member-filtered Daily Surface.

Use existing FamilyMember data.

==================================================
2. FAMILY DAILY SURFACE
==================================================

Create a clear household/member view of the day.

Preserve the existing Rhythm Engine structure.

Potential hierarchy:

NOW
────────────────
what needs attention now

NEXT
────────────────
what is approaching

TODAY
────────────────
remaining responsibilities

LATER
────────────────
later in the day

Use the existing:
- tasks
- routines
- calendar
- prayer context
- reminders
- Hifz
- Ramadan
- family signals

Do not duplicate calculations.

==================================================
3. SHARED VS PERSONAL RESPONSIBILITY
==================================================

Visually distinguish:

Household
Assigned to me
Assigned to another member
Shared routine

without making the interface noisy.

Example:

Household
"Buy groceries"

Amina
"School pickup"

Yusuf
"Pack school bag"

Shared
"After Maghrib family routine"

==================================================
4. ROUTINE PRESENTATION
==================================================

Make routines feel like household workflows.

Example:

School Morning
2 / 4 complete

✓ Get dressed
✓ Breakfast
○ Pack school bag
○ Leave

Include:

- assignee
- timing
- recurrence
- progress
- next step

Avoid task-dashboard aesthetics.

==================================================
5. WEEKLY FAMILY PLANNING
==================================================

THIS IS THE FLAGSHIP EXPERIENCE.

The current domain engine already supports:

- week generation
- proposal/draft state
- tasks
- routines
- family members
- conflicts
- workload
- meals
- approval
- idempotent commit

Design a guided weekly planning experience.

Suggested flow:

STEP 1
Review last week

STEP 2
Review upcoming fixed events

STEP 3
Review routines

STEP 4
Assign responsibilities

STEP 5
Resolve conflicts

STEP 6
Review workload

STEP 7
Review meals/grocery

STEP 8
Approve week

Do NOT force the user through all steps when there is nothing to review.

Allow:
- skip
- edit
- go back
- save draft
- approve

==================================================
6. WEEKLY RHYTHM
==================================================

Do NOT design a conventional hourly calendar clone.

Use:

Day
  ↓
Prayer rhythm
  ↓
Responsibilities

Potential visual:

MONDAY

After Fajr
Quran / morning routine

After Dhuhr
School / work

After Asr
Errands

After Maghrib
Family routine

The exact data must come from the real DayRhythm.

==================================================
7. CONFLICT EXPERIENCE
==================================================

Conflict signals should be immediately understandable.

Examples:

"Ahmed has two overlapping commitments."

"This After Asr block has 4 responsibilities."

"School pickup conflicts with the 4:30 PM appointment."

Use existing conflict severity/types.

Provide clear actions:

Reschedule
Reassign
Split
Review

DO NOT automatically apply changes.

==================================================
8. WORKLOAD EXPERIENCE
==================================================

Present workload as a fairness signal.

Good:

"Amina is carrying more of this week's responsibilities."

"Responsibilities are broadly balanced."

"This week is light for Yusuf."

Avoid:

- points
- scores
- rankings
- leaderboards
- "winner"
- percentages intended to compete

Where duration is unknown, do not present false precision.

==================================================
9. CHILD EXPERIENCE
==================================================

Child perspective should remain simple and encouraging.

Show:

- their responsibilities
- their routines
- appropriate Quran/Hifz context
- positive progress

Do NOT show:

- adult workload
- budget
- adult financial information
- parent-only responsibilities
- family fairness analysis

Use existing sanitized domain data.

==================================================
10. CALM EXPERIENCE
==================================================

Calm should feel:

- editorial
- quiet
- spacious
- reflective
- low-noise

Use:
- typography
- whitespace
- subtle separators
- restrained status treatment

Do not over-card everything.

==================================================
11. VIBRANT EXPERIENCE
==================================================

Vibrant should feel:

- vivid
- energetic
- modern
- warm

Use existing Vibrant design tokens and primitives.

Do not create new Vibrant business logic.

The same Wave 2 data should render in both experiences.

==================================================
12. FAMILY WORKLOAD VISUALIZATION
==================================================

Prefer:

member rows
subtle indicators
qualitative states
responsibility counts

over complex charts.

Possible:

Amina
More responsibility

Ahmed
Balanced

Yusuf
Light

Household
3 shared responsibilities

Keep the tone compassionate.

==================================================
13. RESPONSIBILITY ASSIGNMENT
==================================================

Make assignment interactions easy.

When planning:

Assign to:
[Household]
[Amina]
[Ahmed]
[Yusuf]

For routines:

Step:
"Pack school bag"
Assigned to:
[Yusuf]

Use existing member IDs and APIs.

==================================================
14. APPROVAL
==================================================

The user should understand:

WHAT WILL CHANGE

before approving.

Use a concise summary:

"This week you are changing 6 assignments,
adding 2 responsibilities,
and resolving 1 conflict."

Then:

Cancel
Save Draft
Approve Week

Do not expose implementation details.

==================================================
15. MOBILE FIRST
==================================================

Primary experience is mobile.

Verify:
- small phone
- normal phone
- tablet
- desktop

Important:
- member switcher
- weekly planner
- conflict cards
- assignments
- routine editor
- approval summary

Avoid horizontal scrolling.

==================================================
16. ACCESSIBILITY
==================================================

Verify:
- keyboard navigation
- focus states
- semantic buttons
- accessible member selector
- accessible conflict actions
- readable status indicators
- reduced motion
- touch targets

Do not make drag-and-drop the only way to assign/reorder.

==================================================
17. MOTION
==================================================

Use subtle, meaningful motion:

- step completion
- assignment change
- conflict appearing/resolving
- weekly step transition
- approval confirmation

Avoid:
- gamification
- looping animation
- excessive particles
- noisy transitions

==================================================
18. DATA BOUNDARY
==================================================

Consume existing:

- FamilyMember
- Task
- Routine
- RoutineStep
- DayRhythm
- DailySurfaceItem
- ConflictSignal
- HouseholdWorkloadSummary
- WeeklyPlanProposal

DO NOT calculate:

- prayer times
- recurrence
- conflict detection
- workload
- routine completion
- task filtering

inside UI components.

==================================================
19. PRESERVE EXISTING PRODUCT
==================================================

Do not redesign unrelated:

- Quran
- Hifz
- Ramadan
- Budget
- Insights
- Calendar
- Deen
- PWA
- Tasks
- existing Wave 1 Rhythm UX

The Family Operating System should feel like an extension of the current Firdaus product.

==================================================
20. VALIDATION
==================================================

After implementation:

- verify Household view
- verify member view
- verify child view
- verify assignments
- verify routines
- verify conflicts
- verify workload
- verify weekly planning
- verify approval
- verify Calm
- verify Vibrant
- verify mobile
- verify desktop

Run:

npx tsx --test src/lib/*.test.ts
npx tsc --noEmit
npm run build

The business/domain tests must remain green.

==================================================
21. SCOPE
==================================================

Do not:
- implement new domain logic
- add dependencies unnecessarily
- modify database schema
- redesign PWA
- begin Wave 3
- create an AI family planner

This is a Wave 2 UX/productization pass only.

==================================================
FINAL REPORT
==================================================

Return:

### Family Switcher
### Household Daily Surface
### Member Daily Surface
### Child View
### Routines
### Responsibilities
### Conflict UX
### Workload UX
### Weekly Planning
### Approval Flow
### Calm Experience
### Vibrant Experience
### Mobile
### Accessibility
### Components Changed
### Domain APIs Consumed
### Business Logic Changes
### Dependencies
### Tests
### Typecheck
### Build
### Remaining Issues