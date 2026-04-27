# Classification Framework
## Location: /docs/classification-framework.md
##
## This guide defines the 4 classification levels used across the governance system.
## All team members and agents must apply these definitions consistently.
## When in doubt, always escalate to the higher classification.

---

## 🎯 Purpose

Classification is the entry condition for the entire agent pipeline.
A wrong classification means the wrong agent, the wrong model, and the wrong output.
This document provides definitions, decision criteria, and real examples
for every classification level.

---

## 🔵 The 4 Classification Levels

NO-CODE ──► SIMPLE ──► CROSS-SERVICE ──► ARCHITECTURAL  
  │              │              │                  │  
  │         1 file/function     │        Schema, contract, platform, or security baseline change  
  │                        Multi-layer         Full system design  
No code needed              change  

---

## ⬜ Level 1 — NO-CODE

### Definition

The issue can be resolved without writing or modifying any code.

### Triggers

- Updating documentation or comments only
- Changing a configuration value in an existing config file
- Fixing a typo in a UI string or error message
- Updating environment variable values (not adding new ones)
- Clarifying an existing ADR without changing the decision

### Agent Required

None. Resolve directly and log the change.

### Copilot Model

Not applicable.

### Change Log Entries

- Generation Type: `No Generation`
- Risk Level: `Low`

### ✅ Real Examples

| Issue | Why No-Code |
|---|---|
| Update the 404 error message text in the UI | Only a string constant changes |
| Fix a broken link in README.md | Documentation only |
| Change the pagination default from 10 to 20 in a config file | Config value, no logic change |
| Update an ADR status from Proposed to Accepted | Documentation only |

### ❌ Misclassification Traps

- Adding a new environment variable → **Simple minimum** (requires code to consume it)
- Changing a hardcoded value inside a function → **Simple** (code change required)

---

## 🟢 Level 2 — SIMPLE

### Definition

A logic or configuration change isolated to a single file, function, or narrow component
that does not affect any shared layer, API contract, database schema, runtime topology,
or security model.

### Triggers

- Adding or fixing logic inside one existing function
- Styling or layout change in a single component
- Adding a utility function used only within one module
- Fixing a bug that is contained to one service or component
- Adding a new field to a form that maps to an existing API field
- Adjusting a single container health check without changing deployment topology
- Updating one logging field or one alert threshold in an isolated service
- Tightening one secure header in reverse proxy config without changing auth flow

### Agent Required

Domain agent only — Backend, Frontend, DevOps, Observability, or Security Agent depending on the layer.
No Architect Agent needed.

### Copilot Model

Base model acceptable. Use Premium (Sonnet) only if the logic is complex.

### Change Log Entries

- Generation Type: `Delta`
- Risk Level: `Low`

### ✅ Real Examples

| Issue | Why Simple |
|---|---|
| Add email format validation to an existing login form | Single component, existing API field |
| Fix a bug where a date is displayed in the wrong timezone | Single utility function change |
| Add a loading spinner to an existing button during API call | Single component, no new API call |
| Refactor a helper function to handle a null input | Single file, no interface change |
| Add a missing index to a query inside one service function | Single service, no schema change |
| Adjust one Docker health check command for the app container | Single runtime config change |
| Add one structured log field to a background worker | Single observability change |
| Add one missing security header in Nginx | Single security hardening change |

### ❌ Misclassification Traps

- The fix requires changing a shared utility used by 3+ modules → **Cross-Service**
- The form field requires a new API field not yet in the contract → **Architectural**
- The bug is caused by a schema design issue → **Architectural**
- The infra change affects multiple services or routing rules → **Cross-Service**
- The security change alters session, auth, or access-control behavior → **Architectural**

---

## 🟡 Level 3 — CROSS-SERVICE

### Definition

A change that touches more than one service, module, or shared layer
but does not require modifying an existing DB schema, API contract,
runtime platform baseline, or authentication model.

### Triggers

- Adding a new feature that involves both Backend and Frontend
- Modifying a shared utility, type, or interface used by multiple modules
- Implementing a new API endpoint defined in an existing contract
- Adding a new page or view that requires a new API call (contract already defined)
- Connecting two existing services that were previously independent
- Adding observability to multiple services using an already approved telemetry pattern
- Updating deployment behavior across multiple containers without changing architecture
- Applying security hardening across several routes or services using existing rules

### Agent Required

Relevant domain agents with Architect Agent review.
Architect Agent must confirm no contract, schema, platform, or auth model change is needed before implementation begins.

### Copilot Model

Premium (Sonnet) required for all agents in this classification.

### Change Log Entries

- Generation Type: `Delta` (if implementing a defined contract) or `Full` (if new)
- Risk Level: `Medium`

### ✅ Real Examples

| Issue | Why Cross-Service |
|---|---|
| Implement the user profile page — API contract already defined | Multi-layer (BE + FE), contract exists |
| Add real-time notification badge to navbar using existing WebSocket service | Frontend + shared service layer |
| Implement CSV export — endpoint defined, needs service + UI button | Backend service + frontend trigger |
| Add audit logging to 3 existing endpoints | Multiple backend modules affected |
| Refactor auth middleware to support a new role type under existing auth model | Shared middleware + multiple routes |
| Add request tracing to API + worker using existing telemetry stack | Multiple services, no new platform decision |
| Update Docker Compose for app + Redis + worker under an approved topology | Multi-service runtime change |
| Apply rate limiting to several protected routes using existing security rules | Shared security behavior, no auth model change |

### ❌ Misclassification Traps

- The implementation reveals the API contract is missing fields → **Architectural**
- The shared utility change alters its return type or signature → **Architectural**
- More than 2 teams or services need coordination for a structural change → **Architectural**
- The deployment change introduces a new infrastructure component → **Architectural**
- The observability change introduces a new telemetry platform or baseline → **Architectural**
- The security change modifies authentication or authorization strategy → **Architectural**

---

## 🔴 Level 4 — ARCHITECTURAL

### Definition

A change that modifies or introduces a DB schema, API contract, shared interface,
runtime platform baseline, observability baseline, security model, or requires a
fundamental design decision that affects the long-term structure of the system.

### Triggers

- Adding or modifying a database table, column, or relationship
- Adding, changing, or deprecating an API endpoint contract
- Introducing a new service, module, or infrastructure component
- Changing authentication or authorization strategy
- Introducing a new third-party integration or external dependency
- Introducing a new observability platform, tracing baseline, or alerting baseline
- Changing reverse proxy, deployment topology, or runtime boundaries
- Any change that requires a new or updated ADR
- Any decision that cannot be reversed without a migration or platform redesign

### Agent Required

Architect Agent MUST go first and produce a complete spec.
No Backend, Frontend, DevOps, Observability, or Security agent starts work
until `architecture.md` is updated if the change affects architecture-level decisions.

### Copilot Model

Premium (Opus or best available) required for Architect Agent.
Premium (Sonnet) for domain agents implementing the approved spec.

### Change Log Entries

- Generation Type: `Full`
- Risk Level: `Medium` or `High`
- ADR Required: `Yes`

### ✅ Real Examples

| Issue | Why Architectural |
|---|---|
| Add a `tags` system to posts — requires new table and junction table | DB schema change |
| Change user authentication from JWT to session-based | Auth strategy change — ADR required |
| Add a new `/api/v2/users` endpoint with different response shape | New API contract |
| Introduce Redis for caching across multiple services | New infrastructure component |
| Add soft-delete to all existing tables | DB schema change across multiple tables |
| Split a monolith service into two independent services | Structural redesign — ADR required |
| Introduce OpenTelemetry and centralized tracing for the platform | New observability baseline |
| Replace direct app exposure with Nginx reverse proxy as required edge layer | New runtime topology |
| Adopt Azure AD as the enterprise identity provider | Authentication model change |

### ❌ Misclassification Traps

- "It's just a small schema change" → Schema changes are ALWAYS Architectural
- "The API change is backward compatible" → Any contract change is Architectural
- "We're just adding one column" → One column = migration = Architectural
- "It's only one new container" → A new infrastructure component is Architectural
- "It's only logging" → A new observability platform or baseline is Architectural
- "It's only auth config" → Authentication model changes are Architectural

---

## 🔁 Quick Classification Decision Tree

START  
  │  
  ▼  
Does it require writing or changing any code?  
  │  
  ├── NO ──────────────────────────────────► NO-CODE  
  │  
  └── YES  
        │  
        ▼  
      Does it modify a DB schema, API contract, runtime platform baseline,
      observability baseline, or authentication / authorization model?  
        │  
        ├── YES ─────────────────────────► ARCHITECTURAL  
        │  
        └── NO  
              │  
              ▼  
            Does it touch more than one service,
            module, or shared layer?  
              │  
              ├── YES ──────────────────► CROSS-SERVICE  
              │  
              └── NO ───────────────────► SIMPLE  

---

## ⚠️ Escalation Rule

**When in doubt, always escalate to the higher classification.**

The cost of over-classifying a Simple issue as Cross-Service is a slightly
longer review process. The cost of under-classifying an Architectural issue
as Simple is a broken system, a missing migration, and untracked technical debt.

---

## 📋 Classification Log — Monthly Review

Use this table during monthly retrospectives to identify patterns
in misclassification and hallucination rates.

| Month | No-Code | Simple | Cross-Service | Architectural | Misclassified | Hallucinations |
|---|---|---|---|---|---|---|
| YYYY-MM | 0 | 0 | 0 | 0 | 0 | 0 |