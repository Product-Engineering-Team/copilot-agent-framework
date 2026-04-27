# Pull Request

## 📋 Summary

> One or two sentences describing what this PR does and why.

_Replace this line with your summary._

---

## 🔗 Linked Issue

> Reference the issue this PR resolves.

Closes #___

---

## 🏷️ Change Log Entry

> Complete ALL fields. PRs with incomplete Change Log will not be merged.
> This log feeds the monthly KPI report and architectural health metrics.

| Field | Value |
|---|---|
| **Change ID** | ENH-___ |
| **Classification** | `[ No-Code / Simple / Cross-Service / Architectural ]` |
| **Risk Level** | `[ Low / Medium / High ]` |
| **Agent Role Used** | `[ PM / Architect / Backend / Frontend / QA / DevOps / Observability / Security / None ]` |
| **Copilot Model Used** | `[ Base / Premium Sonnet / Premium Opus / Premium GPT-4o / None ]` |
| **Generation Type** | `[ Delta / Full / No Generation ]` |
| **Avoided Full Redesign** | `[ Yes / No / N/A ]` |
| **Hallucination Detected** | `[ Yes / No ]` |
| **Stack Compliance Verified** | `[ Yes / No ]` |
| **Security Review Completed** | `[ Yes / No / N/A ]` |
| **Observability Review Completed** | `[ Yes / No / N/A ]` |
| **Deployment Review Completed** | `[ Yes / No / N/A ]` |
| **New Dependency Introduced** | `[ Yes — declared in stack.md / No ]` |

> If **Hallucination Detected = Yes**, describe what was detected and how it was corrected in the Notes section below.

---

## 📐 Scope of Changes

**Files modified:**
- [ ] `/src/frontend/` — UI components or pages
- [ ] `/src/backend/` — API routes or business logic
- [ ] `/src/` — Shared utilities or types
- [ ] `/docs/` — Architecture docs, PRD, or ADRs
- [ ] `/agents/` — Agent role files or stack definition
- [ ] `/.github/` — Governance or workflow files
- [ ] `Tests` — Unit or integration test files
- [ ] `Infrastructure / deployment` — Containers, runtime, reverse proxy, CI/CD, backups
- [ ] `Security / auth / access control` — Auth flows, permissions, secrets, hardening
- [ ] `Observability / logging / metrics / tracing` — Runtime visibility, telemetry, alerting
- [ ] `Configuration` — Environment, build, or deployment config

---

## ✅ Level Silver Checkpoint

> Every item must be checked before requesting review.

- [ ] Issue classification matches the actual complexity of this PR
- [ ] PR modifies only files within the defined issue scope
- [ ] All automated tests pass locally
- [ ] No undeclared dependencies were introduced
- [ ] No secrets, credentials, or API keys are present in the code
- [ ] All API inputs are validated using the library declared in `/agents/stack.md`
- [ ] Database queries use parameterized statements — no string interpolation
- [ ] No debug logs or development artifacts left in the code
- [ ] Code follows conventions declared in `/agents/stack.md`
- [ ] `/docs/architecture.md` updated if this PR changes an API contract or DB schema
- [ ] `/docs/agent-state.md` updated with the current feature status
- [ ] Security review completed if auth, secrets, access control, or privileged operations changed
- [ ] Observability review completed if critical runtime behavior changed
- [ ] Deployment review completed if infrastructure, runtime packaging, or reverse proxy behavior changed

---

## 🧪 Testing Evidence

> Describe how the changes were verified. Attach screenshots if applicable.

**Test type:** `[ Unit / Integration / E2E / Manual / Security Review / Observability Review / Deployment Review / None ]`

**What was tested:**

_Describe the test scenarios covered._

**Edge cases considered:**

_List any edge cases explicitly tested or noted for future coverage._

---

## 🏗️ Architectural Impact

> Complete only if Classification = Cross-Service or Architectural.

**Does this PR modify a DB schema?**
- [ ] Yes — migration file included
- [ ] No

**Does this PR modify an API contract?**
- [ ] Yes — `/docs/architecture.md` updated
- [ ] No

**Does this PR introduce a new shared interface or type?**
- [ ] Yes — documented in `/docs/architecture.md`
- [ ] No

**Does this PR change runtime topology, containers, reverse proxy behavior, or deployment flow?**
- [ ] Yes — deployment review completed
- [ ] No

**Does this PR affect observability baseline, telemetry, or alerting?**
- [ ] Yes — observability review completed
- [ ] No

**Does this PR affect authentication, authorization, or privileged access?**
- [ ] Yes — security review completed
- [ ] No

**ADR required?**
- [ ] Yes — ADR added to `/docs/architecture.md`
- [ ] No — existing decision covers this change

---

## 📝 Notes

> Any additional context, decisions made, known limitations, or follow-up items.

_Add notes here or write "None"._

---

## 🔁 Follow-up Issues

> List any new issues discovered or deferred during this PR.

- [ ] _(Issue title — create and link after merge)_