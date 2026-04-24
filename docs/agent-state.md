# Agent State — Feature Tracker & Change Log
# Location: /docs/agent-state.md
#
# This file is the shared memory of the agent pipeline.
# All agents read this file to understand current project state.
# Update this file at the end of every agent session.
# The Change Log section feeds directly into monthly KPI reporting.

---

## 📦 Project

**Name:**
**Description:**
**Stack Reference:** `/agents/stack.md`
**PRD Reference:** `/docs/PRD.md`
**Architecture Reference:** `/docs/architecture.md`
**Started:** YYYY-MM-DD
**Current Phase:** [ Foundation / Active Development / Stabilization / Maintenance ]

---

## 🎯 Active Sprint

**Sprint goal:**
**Sprint period:** YYYY-MM-DD → YYYY-MM-DD

---

## 📋 Feature Tracker

> Update status after every agent session.
> One row per vertical slice. Add slices as the PM Agent defines them.

### Status Legend
⏳ Pending     — Not started, waiting for dependency
🔵 Classified  — PM Agent complete, ready for Architect Agent
📐 Specced     — Architect Agent complete, ready for implementation
🔄 In Progress — Implementation underway
🧪 In Review   — QA Agent reviewing
✅ Done        — QA passed, PR merged
🚫 Blocked     — Waiting on external dependency

---

### Feature: [Feature Name]
> Replace this block with actual features. Add one block per feature.

| Slice | Description | Classification | Risk | PM | Architect | Backend | Frontend | QA | Status |
|---|---|---|---|---|---|---|---|---|---|
| [Feature]-01 | [Slice description] | Simple | Low | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ Pending |
| [Feature]-02 | [Slice description] | Cross-Service | Medium | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ | ⏳ Pending |

**Dependencies:**
- [Feature]-02 is blocked by: [Feature]-01

**Notes:**

---

## 🔁 Pipeline State

> Current active work across all agents. One row per agent.
> Update this section at the start and end of every Copilot session.

| Agent | Current Task | Slice | Status | Last Updated |
|---|---|---|---|---|
| PM Agent | — | — | Idle | YYYY-MM-DD |
| Architect Agent | — | — | Idle | YYYY-MM-DD |
| Backend Agent | — | — | Idle | YYYY-MM-DD |
| Frontend Agent | — | — | Idle | YYYY-MM-DD |
| QA Agent | — | — | Idle | YYYY-MM-DD |

---

## 🚧 Blocked Items

> List all currently blocked slices with the reason and expected unblock date.

| Slice | Blocked By | Reason | Expected Unblock |
|---|---|---|---|
| — | — | — | — |

---

## 📊 Monthly Change Log

> One entry per merged PR. This data feeds the KPI dashboard.
> Copy values directly from the PR Change Log fields.

### [YYYY-MM] — [Month Name Year]

| Change ID | Slice | Classification | Risk | Agent | Model | Gen Type | Avoided Redesign | Hallucination | Stack OK |
|---|---|---|---|---|---|---|---|---|---|
| ENH-001 | — | — | — | — | — | — | — | — | — |

**Monthly Summary:**

| KPI | Count | Target | Status |
|---|---|---|---|
| Total PRs merged | 0 | — | — |
| No-Code | 0 | — | — |
| Simple | 0 | — | — |
| Cross-Service | 0 | — | — |
| Architectural | 0 | — | — |
| Misclassified issues | 0 | 0 | — |
| Hallucinations detected | 0 | 0 | — |
| Avoided full redesign | 0 | — | — |
| New undeclared dependencies | 0 | 0 | — |

---

## 📐 Architecture Snapshot

> Quick reference to active contracts and schemas.
> Full details live in `/docs/architecture.md`.

### Active API Contracts
| Endpoint | Method | Status | ADR |
|---|---|---|---|
| — | — | — | — |

### Active DB Tables
| Table | Status | Migration | Last Modified |
|---|---|---|---|
| — | — | — | — |

---

## 🗒️ Decision Log

> Lightweight log for decisions made during agent sessions that do not
> require a full ADR but should be traceable.

| Date | Decision | Made By | Rationale |
|---|---|---|---|
| YYYY-MM-DD | [Decision description] | [Agent / Human] | [Why] |

---

## 🔍 Open Questions

> Questions that arose during agent sessions and need a decision
> before work can continue. Assign and resolve promptly.

| # | Question | Raised By | Assigned To | Status |
|---|---|---|---|---|
| 1 | — | — | — | Open |

---

## 📅 Retrospective Notes

> Brief notes from each sprint or monthly review.
> Focus on: what worked, what broke, classification accuracy, agent performance.

### YYYY-MM-DD — [Sprint or Month]

**What worked well:**
-

**What needs improvement:**
-

**Classification accuracy:**
-

**Agent performance notes:**
-

**Action items:**
- [ ]
