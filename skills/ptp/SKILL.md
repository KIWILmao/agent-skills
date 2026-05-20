---
name: ptp
description: >
 Plan To Product (PTP) — Full project scaffolding and execution tracking skill. Trigger this 
 skill whenever the user uses /ptp <idea>, asks to "plan a project", "scaffold a project", 
 "generate a PRD", "create project plans", or says "I have an idea I want to build". Also 
 trigger if the user mentions wanting plans, screens, or phases for a new project. This skill 
 generates a PRD, a screens breakdown, modular plan files with phases, and an execute.md 
 tracker — and then drives the actual implementation using those plans, resuming automatically 
 across sessions.
---

# PTP — Plan To Product

Turns a raw idea into a fully structured, executable project — PRD, screens, phased plans, 
and an execution tracker — then drives implementation plan by plan, phase by phase.

---

## Step 0 — Gather Inputs

### 0a. Get the idea
If the user ran `/ptp <idea>`, extract the idea from the command.
If no idea was provided, ask:
> "What's the idea you want to build? Describe it in as much detail as you'd like."

### 0b. Ask about tech stack
Ask the user for their tech stack. Suggest options based on common patterns if they're unsure:
- Frontend (e.g. React, Next.js, Vue, Flutter, React Native)
- Backend (e.g. Node/Express, Django, FastAPI, Laravel, Rails)
- Database (e.g. PostgreSQL, MySQL, MongoDB, Supabase, Firebase)
- Auth (e.g. JWT, Clerk, Auth0, Supabase Auth, Firebase Auth)
- Any other services (e.g. S3, Stripe, Twilio, Redis)

### 0c. Ask about project folder structure
Inspect the current directory and offer smart options. For example:
- "I'll create everything at the **project root** `./`"
- "I see a `frontend/` folder — put plans in `frontend/plan/` and docs at root?"
- "I see `frontend/` and `backend/` — create a shared `./plan/` at root?"
- "Tell me where you'd like the `plan/` folder."

Always confirm before creating files.

### 0d. Ask about `claude.md`
Check if `claude.md` exists at the project root:
- If it **doesn't exist**: create it and add the workflow instructions (see Step 5).
- If it **exists and is not empty**: ask the user: "You already have a `claude.md`. Should I append the blueprint workflow instructions to it, or keep it as is and create a separate `claude-blueprint.md`?"
- If it **exists but is empty**: write the workflow instructions into it.

---

## Step 1 — Generate PRD

Create `prd.md` at the agreed root. See `references/prd-template.md` for the full structure.

Key sections to always include:
1. **Overview** — one-paragraph summary of the product
2. **Problem Statement** — what problem this solves and for whom
3. **Target Users** — personas with brief descriptions
4. **Goals & Success Metrics** — what does success look like
5. **MVP Scope** — features in the first version (be explicit and conservative)
6. **Full Scope** — everything beyond MVP, phase 2+
7. **Out of Scope** — explicitly state what's NOT being built
8. **Non-functional Requirements** — performance, security, scale expectations
9. **Tech Stack** — confirm what was provided in Step 0b
10. **Open Questions** — anything unclear that needs a decision

---

## Step 2 — Generate `screens.md`

Create `screens.md` at the agreed root. List every screen in the product — from onboarding to the last admin panel. Cover all flows end to end. See `references/screens-template.md` for format.

For each screen include:
- **Screen Name** — short identifier
- **Route / Path** — e.g. `/login`, `/dashboard`, `/profile/:id`
- **Purpose** — what is the user trying to accomplish here
- **Auth State** — `public` | `authenticated` | `role: admin` etc.
- **Key Actions** — buttons, forms, navigations the user can trigger
- **Data Displayed** — what data shows on this screen and where it comes from

Group screens by flow (e.g. Auth Flow, Onboarding Flow, Core App, Settings, Admin).

---

## Step 3 — Generate Plans

Plans live in the `plan/` folder (at the location confirmed in Step 0c).
Each plan = one module or feature of the product.
Naming: `plan-01-auth.md`, `plan-02-dashboard.md`, etc.

### How to divide into plans
- One plan per **logical module** (auth, user profile, notifications, payments, etc.)
- Keep plans small enough to be completable in a focused session
- If the project is large, plans can be flexible — not all plans need all phases
- For standard/small-medium projects, use the **fixed phase structure** below

### Fixed Phase Structure (default)
Each plan file must contain these phases in order:

```
Phase 1 — DB Schema & Migrations
Phase 2 — Backend: API Routes + Controllers
Phase 3 — Backend: Service Logic + Business Rules
Phase 4 — Frontend: Screens & Components
Phase 5 — Integration (wiring frontend ↔ backend)
Phase 6 — Testing & QA (unit, integration, edge cases)
```

For large/complex plans, phases can be split further or merged if it makes sense — document the reasoning.

### Plan file format
See `references/plan-template.md` for the full template.

Each plan file must include:
- Plan name and module description
- **Dependencies** — list of other plan numbers this plan requires to be done first
- **Can run in parallel** — yes/no, and with which plans if yes
- **Status** — `pending` | `in-progress` | `blocked` | `done`
- All phases with their tasks, code blocks, schema snippets, API contracts, component specs
- A **Completion Checklist** at the bottom

### Dependency rules
- If Plan B depends on Plan A, write: `depends_on: [plan-01-auth]` in Plan B's header
- Mention it clearly in the phase where the dependency matters
- Claude will refuse to start a dependent plan if its dependency isn't done

---

## Step 4 — Generate `execute.md`

Create `plan/execute.md`. This is the single source of truth for progress.
See `references/execute-template.md` for format.

Contents:
- A **plans table** with columns: Plan | Module | Status | Dependencies | Parallel-safe
- A **current focus** section: which plan and phase is active right now
- A **completed log**: plans + phases done, with dates
- A **blocked log**: what's blocked and why

Claude updates this file:
- When a phase is marked done
- When a plan is fully completed
- When a plan is blocked

---

## Step 5 — Write `claude.md` Instructions

Append (or create) these instructions in `claude.md`:

```markdown
## PTP Workflow

This project uses the PTP (Plan To Product) skill for structured execution.

### On every session start:
1. Read `plan/execute.md`
2. Find the current in-progress plan and phase
3. Say: "Resuming [Plan Name] — Phase [X]: [Phase Title]. Continuing from where we left off."
4. Do NOT re-read the PRD or screens.md unless you need to clarify something specific

### During execution:
- Work through one phase at a time
- After completing a phase, update its status in the plan file and in execute.md
- After completing ALL phases in a plan, mark the plan as done in execute.md and ask: 
 "Plan [X] is complete! Ready to move to [Next Plan], or do you want to review first?"

### Phase status values:
pending → in-progress → blocked → done

### If a plan is blocked:
- Mark it as `blocked` with the reason in execute.md
- Move to the next non-dependent, non-blocked plan
- Notify the user: "Plan [X] is blocked because [reason]. Moving to Plan [Y] instead."

### If the user asks to jump to a specific plan:
- Check if it has unresolved dependencies
- If yes: "This plan depends on [Plan X] which isn't done yet. Want to do that first?"
- If no dependencies: start it immediately and update execute.md

### Parallel plans:
- When plans are marked parallel-safe, spawn subagents to work on them simultaneously
- Each subagent reads its plan file and works through its phases
- Subagents update execute.md when done
- Report back to the user when all parallel plans finish
```

---

## Step 6 — Final Setup Check

After all files are generated:
1. List all created files with their paths
2. Show the plans table (from execute.md) so the user sees the full roadmap
3. Ask: **"Everything looks good — ready to start building? I'll kick off Plan 01 unless you want to start somewhere else."**

If user says yes → begin Plan 01, Phase 1. Update execute.md status to `in-progress`.

---

## Commands Reference

All commands below can be used at any point after project initialization.
Always read `plan/execute.md` before processing any command so state is current.

---

### `/ptp status`
Show a clean summary of the current project state.

**Output:**
- Current active plan + phase
- Plans table (all plans, their status, dependencies)
- Blocked plans and reasons
- Overall % complete (phases done / total phases)

**Edge cases:**
- If `execute.md` doesn't exist → "No PTP project found here. Run `/ptp <idea>` to initialize one."
- If all plans are done → show completion summary + congratulate 

---

### `/ptp resume`
Explicitly resume from the current position in execute.md.

**Behavior:** Read execute.md → find the in-progress plan + phase → continue from there.

**Edge cases:**
- No `execute.md` → "No PTP project found. Run `/ptp <idea>` to start."
- Nothing in-progress (all pending) → "No plan is currently in-progress. Want me to start Plan 01?"
- Nothing in-progress (all done) → "All plans are complete! Your project is done."
- Multiple plans in-progress (e.g. parallel plans were started) → list them and ask: "Which plan do you want to focus on right now?"

---

### `/ptp start <plan-id>`
Jump to and begin a specific plan (e.g. `/ptp start plan-03`).

**Behavior:** Validate → check deps → begin Phase 1 of that plan → update execute.md.

**Edge cases:**
- Plan doesn't exist → "plan-03 wasn't found. Run `/ptp roadmap` to see available plans."
- Plan already `done` → "plan-03 is already marked as done. Want to re-open it and redo a specific phase?"
- Plan already `in-progress` → "plan-03 is already in progress at Phase [X]. Want to resume from there, or restart from Phase 1?"
- Plan is `blocked` → "plan-03 is currently blocked: [reason]. Want to unblock it first or work on something else?"
- Plan has unmet dependencies → show exactly which plans need to finish first:
 > "plan-03 depends on: plan-01 (done ), plan-02 (pending ). Finish plan-02 first, or override?"
- If user chooses to override a dependency → warn clearly: "Skipping a dependency may cause broken integrations. Are you sure? Type `yes` to confirm."

---

### `/ptp next`
Move forward — complete the current phase and advance to the next one.

**Behavior:**
- If mid-plan: mark current phase `done` in plan file + execute.md → begin next phase
- If last phase of a plan: trigger plan completion flow (see `/ptp done`)

**Edge cases:**
- Nothing in-progress → "Nothing is currently in-progress. Use `/ptp start <plan-id>` or `/ptp resume`."
- Current phase has incomplete items in its checklist → warn: "There are unchecked items in this phase. Are you sure you want to move on?" List the unchecked items.
- Last phase of last plan → project complete flow.

---

### `/ptp done`
️ **Risky command — always confirm before executing.**

Marks the current phase OR the entire current plan as done.

**Step 1 — Clarify scope:**
Ask: "Do you want to mark just the current **phase** as done, or the entire **plan**?"

**Step 2 — Show impact before confirming:**

For marking a **phase** done:
- Check if the phase checklist has incomplete items → list them and ask: "These items are unchecked. Mark done anyway?"

For marking a **plan** done:
- Check which other plans depend on this one → show them:
 > "Marking plan-02 as done will unblock: plan-03, plan-04. These plans depend on plan-02 being complete."
- Check if any phases within the plan are still pending → list them:
 > "Phases 4, 5, 6 are still pending in this plan. Mark the whole plan done anyway?"
- Ask for explicit confirmation: "Are you sure you want to mark plan-02 as done? Type `yes` to confirm."

**After confirmation:**
- Update plan file status → update execute.md → show what got unblocked
- Ask: "Ready to move to [next recommended plan], or do you want to pick one yourself?"

**Edge cases:**
- Nothing in-progress → "Nothing is in-progress right now."
- Plan is already done → "This plan is already marked as done."
- Marking a plan done that has `blocked` dependents → note: "plan-05 depends on this but is currently blocked for another reason: [reason]. You'll need to resolve that separately."

---

### `/ptp block <reason>`
️ **Risky command — always show impact before confirming.**

Marks the current in-progress plan as blocked.

**Step 1 — Show transitive impact:**
Before blocking, check which other plans depend on this one (directly or transitively) and will also become effectively blocked:
> "Blocking plan-02 will also stall: plan-03 (depends on plan-02), plan-04 (depends on plan-03). That's 3 plans affected."

**Step 2 — Show alternatives:**
List plans that are NOT affected and can still be worked on:
> "You can still work on: plan-05 (notifications) and plan-06 (settings) — these are independent."

**Step 3 — Confirm:**
Ask: "Block plan-02 with reason '[reason]'? Type `yes` to confirm."

**After confirmation:**
- Update plan file status to `blocked` with reason + date
- Update execute.md blocked log
- Ask: "Want me to move to [best available unblocked plan] now?"

**Edge cases:**
- No plan in-progress → ask which plan to block: "Which plan do you want to mark as blocked?"
- All remaining plans would be blocked or stalled → warn: "Blocking this plan leaves no available plans to work on. Your project will be at a standstill until this is resolved. Still want to proceed?"
- Reason not provided → ask: "What's the reason for blocking this plan? (This gets saved in execute.md for later reference.)"

---

### `/ptp add <feature>`
Add a new plan mid-project for a feature that wasn't in the original scope.

**Behavior:**
1. Understand the feature from the description
2. Check if it overlaps with any existing plan → warn if so: "This sounds similar to plan-03 (dashboard). Should this be a new plan or should I add it to plan-03?"
3. Ask: "Does this new plan depend on any existing plans?"
4. Generate a new plan file using the standard template
5. Assign the next plan number (e.g. if plans go up to plan-06, new one is plan-07)
6. Update execute.md plans table with the new entry
7. Optionally ask: "Should I also update `prd.md` to include this feature in scope?"

**Edge cases:**
- Feature clearly already exists in a plan → don't create a duplicate, point to the existing plan
- Project already fully complete (all done) → "All existing plans are done. Adding plan-07 for this new feature. Want to start it now?"
- Dependency on a blocked plan → flag it immediately

---

### `/ptp plan <plan-id>`
Print the full details of a specific plan in chat.

**Edge cases:**
- Plan doesn't exist → "plan-05 not found. Run `/ptp roadmap` to see all plans."
- No plan-id provided → show current in-progress plan, or ask which one.

---

### `/ptp roadmap`
Show the full project roadmap — all plans, statuses, dependencies, and parallel groupings.

**Output format:**
```
 Project Roadmap — [Project Name]
Progress: X/Y plans done | X/Y phases done

[01] plan-01-auth done deps: none
[02] plan-02-users in-progress deps: plan-01
[03] plan-03-dashboard pending deps: plan-01, plan-02
[04] plan-04-notifications pending deps: plan-01 parallel with plan-05
[05] plan-05-settings pending deps: plan-01 parallel with plan-04
[06] plan-06-admin blocked deps: plan-02 reason: [reason]
```

**Edge cases:**
- No `execute.md` found → "No PTP project found. Run `/ptp <idea>` to initialize."

---

## Reference Files

- `references/prd-template.md` — Full PRD structure with example sections
- `references/screens-template.md` — Screen entry format and grouping guide 
- `references/plan-template.md` — Complete plan file template with all phases
- `references/execute-template.md` — execute.md format and status conventions
