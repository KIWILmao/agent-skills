# Execute Template

---

# execute.md — [Project Name]

**Last Updated:** [Date] 
**Overall Status:** `in-progress` | `completed` | `blocked`

---

## Plans Overview

| # | Plan | Module | Status | Depends On | Parallel Safe |
|---|------|--------|--------|------------|---------------|
| 01 | plan-01-auth.md | Authentication | `done` | none | no |
| 02 | plan-02-users.md | User Profiles | `in-progress` | plan-01 | no |
| 03 | plan-03-dashboard.md | Dashboard | `pending` | plan-01, plan-02 | no |
| 04 | plan-04-notifications.md | Notifications | `pending` | plan-01 | yes (with plan-05) |
| 05 | plan-05-settings.md | Settings | `pending` | plan-01 | yes (with plan-04) |

---

## Current Focus

**Active Plan:** `plan-02-users` 
**Active Phase:** `Phase 3 — Backend: Service Logic` 
**Phase Status:** `in-progress`

---

## Phase Tracker

### plan-01-auth Done

| Phase | Description | Status |
|-------|-------------|--------|
| Phase 1 | DB Schema & Migrations | done |
| Phase 2 | Backend: API Routes | done |
| Phase 3 | Backend: Service Logic | done |
| Phase 4 | Frontend: Screens & Components | done |
| Phase 5 | Integration | done |
| Phase 6 | Testing & QA | done |

### plan-02-users In Progress

| Phase | Description | Status |
|-------|-------------|--------|
| Phase 1 | DB Schema & Migrations | done |
| Phase 2 | Backend: API Routes | done |
| Phase 3 | Backend: Service Logic | in-progress |
| Phase 4 | Frontend: Screens & Components | pending |
| Phase 5 | Integration | pending |
| Phase 6 | Testing & QA | pending |

### plan-03-dashboard Pending

| Phase | Description | Status |
|-------|-------------|--------|
| Phase 1 | DB Schema & Migrations | pending |
| Phase 2 | Backend: API Routes | pending |
| Phase 3 | Backend: Service Logic | pending |
| Phase 4 | Frontend: Screens & Components | pending |
| Phase 5 | Integration | pending |
| Phase 6 | Testing & QA | pending |

---

## Blocked Plans

| Plan | Reason | Blocked Since | Unblocked By |
|------|--------|---------------|--------------|
| _(none)_ | | | |

---

## Completed Log

| Plan | Completed On | Notes |
|------|-------------|-------|
| plan-01-auth | [Date] | All phases done, all tests passing |

---

## Notes / Decisions Log

[Running log of any important decisions made during implementation]

- [Date] — Decided to use httpOnly cookies instead of localStorage for JWT storage
- [Date] — Skipped email verification for MVP, added to plan-07-email as post-MVP

---

## How Claude Uses This File

1. On session start → read this file first
2. Find the `Current Focus` section → resume from the active plan + phase
3. After completing a phase → update the phase table above + update the plan file
4. After completing a plan → move it to Completed Log, update Plans Overview status to `done`
5. Before starting a new plan → check its `Depends On` field in Plans Overview
6. If a plan is blocked → add it to Blocked Plans table with reason, move to next available plan
