# copilot-agent-framework

A governance framework for orchestrating multi-agent AI workflows using GitHub Copilot inside VS Code.

## Structure

- `.github/` — Copilot global instructions + PR template
- `/agents/` — Core delivery, platform, observability, and security agent definitions + stack configuration
- `/docs/` — Feature tracker, change log, classification guide

## Quick Start

1. Use this template → create your repo
2. Fill in `/agents/stack.md` with your project stack
3. Create or update `/docs/PRD.md`, `/docs/architecture.md`, and `/docs/agent-state.md`
4. Open VS Code with GitHub Copilot — governance loads automatically

## Agent Pipeline

Core delivery:
PM → Architect → Backend → Frontend → QA

Cross-cutting agents:
DevOps → Observability → Security

Use the cross-cutting agents whenever a change affects deployment, runtime operations, monitoring, alerting, authentication, authorization, secrets, or production hardening.

---

## Prompt-Driven Development Pipeline

The framework defines a structured, reproducible pipeline executed via versioned prompts. Each phase produces artifacts that feed the next. Prompts are technology-agnostic and adaptable to any stack, language, or environment.

### Pipeline Overview

```
CORE PHASE (Discovery → Product)
  initial-spec-to-prd → prd-review → prd-hardening-review

ARCHITECTURE PHASE (Product → Implementation Gate)
  architecture-handoff-review
    → vertical-slice-decomposition
    → implementation-plan-validation
    → architecture-design
    → stack-definition
    → stack-validation ←──┐
    → stack-remediation ──┤ (cycle until pass)
    → adr-resolution ─────┘
    → (re-run validation to confirm pass)

IMPLEMENTATION PHASE (Code Execution)
  slice-implementation (execute per slice, in dependency order)

HARDENING PHASE (Implementation → Production Gate)
  deployment-validation (Docker build + local compose up + health check)
  prd-compliance-audit (PRD vs. actual implementation gap analysis)
  operational-readiness (logs, observability, secrets rotation)

VALIDATION (Cross-cutting, any phase)
  code-audit, quick-review
```

### Prompt Folder Structure

Each project maintains versioned prompts organized by phase:

```
/ai/prompts/
├── core/                    # Discovery → Product
│   ├── initial-spec-to-prd.md
│   ├── prd-review.md
│   └── prd-hardening-review.md
├── architecture/            # Product → Implementation Gate
│   ├── 00a-architecture-handoff-review.md
│   ├── 00b-vertical-slice-decomposition.md
│   ├── 00c-implementation-plan-validation.md
│   ├── 00d-architecture-design.md
│   ├── 01-stack-definition.md
│   ├── 02-stack-validation.md
│   ├── 03-stack-remediation.md
│   └── 04-adr-resolution.md
├── implementation/          # Code Execution
│   └── 01-slice-implementation.md
├── hardening/               # Implementation → Production Gate
│   ├── 01-deployment-validation.md
│   ├── 02-prd-compliance-audit.md
│   └── 03-operational-readiness.md
└── validation/              # Cross-cutting
    └── code-audit.md
```

**Naming convention:** Numeric prefix indicates execution order within a phase. Prompts without prefix (00a-00d) are pre-stack steps already executed before the stack cycle begins.

### Prompt Registry

Each project maintains a `/ai/registry/prompt-registry.md` that lists all prompts with:
- ID, file path, purpose, phase, version, status
- Pipeline execution order diagram

---

## Architecture Phase Gates

### Gate Sequence

1. **Stack Definition** — Translate architecture into concrete technology choices (`/agents/stack.md`)
2. **Stack Validation** — Verify alignment between stack.md and source documents (PRD, architecture, implementation plan). Produces a pass/fail report.
3. **Stack Remediation** — Apply human-approved patches from a failed validation. Only modifies stack.md.
4. **ADR Resolution** — Resolve pending Architecture Decision Records that block implementation slices.
5. **Re-Validation** — Re-run stack-validation after changes to confirm pass status.
6. **Implementation Gate** — Only proceed to slice implementation if validation passes and all blocking ADRs are resolved.

The cycle `validation → remediation → adr-resolution → re-validation` may repeat until all discrepancies are resolved.

### ADR (Architecture Decision Records)

ADRs are formal documents for decisions with meaningful tradeoffs. Each ADR must include:
- Context, options considered (minimum 2), decision, rationale, consequences, alternatives rejected
- Impact on blocked slices
- Validation checklist

ADRs live in `/docs/adrs/ADR-XXX-short-title.md`.

---

## Artifact Policy

### What goes in git (decisions and code)

| Artifact | Location | Purpose |
|----------|----------|---------|
| Stack definition | `/agents/stack.md` | Single source of truth for technology |
| Agent state | `/docs/agent-state.md` | Feature tracker, decision log, pipeline state |
| ADRs | `/docs/adrs/` | Formal architectural decisions |
| Prompts | `/ai/prompts/` | Versioned, reproducible execution guides |
| Prompt registry | `/ai/registry/prompt-registry.md` | Index of all prompts |
| Source code | `/src/` | Implementation |
| Tests | `/tests/` | Verification |

### What stays out of git (process artifacts)

| Artifact | Location | Reason |
|----------|----------|--------|
| Execution run logs | `/agents/runs/` | Noisy; trazability via agent-state.md + git history |
| Validation reports | `/agents/validation-report.md` | Intermediate; last state is what matters |
| Suggested ADR lists | `/agents/suggested-adr-list.json` | Consumed during adr-resolution; decisions live in /docs/adrs/ |
| Remediation logs | `/agents/remediation-log.md` | Process artifact; changes tracked in stack.md CHANGE LOG |

Add these to `.gitignore`:
```
/agents/runs/
/agents/validation-report.md
/agents/suggested-adr-list.json
/agents/remediation-log.md
```

---

## Design Principles

### Local-First

All projects built with this framework must be runnable locally without external subscriptions:
- `docker-compose up` starts the full development environment
- Auth, storage, queue, cache, and observability run as local containers
- External integrations (cloud providers, SSO, APM) activate via environment variables only
- No internet connection required after initial image pull

### Stack as Source of Truth

- `/agents/stack.md` is the single source of truth for all technology decisions
- Agents must not introduce technologies not declared in this file
- New dependencies must be declared in stack.md FIRST, then used in code
- Stack changes require a validation cycle (modify → validate → pass)
- **stack.md MUST include a REPOSITORY STRUCTURE section** that defines folder layout and separation of concerns (app code vs. infrastructure vs. documentation vs. AI governance)
- Implementation agents MUST NOT place application code at repo root — the structure section defines where code lives

### Classification-Driven Workflow

The classification framework (4 levels) determines:
- Which agents are involved
- What model tier to use (base vs. premium)
- What output format is expected
- Whether an ADR is required

| Level | Scope | ADR Required |
|-------|-------|:---:|
| NO-CODE | Documentation/config only | No |
| SIMPLE | Single file/function, isolated | No |
| CROSS-SERVICE | Multi-layer/multi-module | No |
| ARCHITECTURAL | Schema, API contract, platform | Yes |

### Traceability

Every decision is traceable:
- **Decision log** in agent-state.md — lightweight, per-session
- **ADRs** in /docs/adrs/ — formal, for architectural decisions
- **Git history** — who changed what and when
- **Prompt versioning** — which prompt version produced which artifact
- **Stack CHANGE LOG** — every stack.md modification logged with reason

---

## Vertical Slice Implementation

Implementation follows vertical slices that cut end-to-end (DB → Service → API → UI → Tests). Each slice:
- Delivers business value independently
- Has explicit acceptance criteria
- Has declared dependencies on prior slices
- Is classified (Simple / Cross-Service / Architectural)
- Is tracked in agent-state.md

### Slice Execution Order

1. Foundation slices first (auth, config, data model)
2. Core business logic (primary value driver)
3. Supporting features (admin, reports, observability)
4. Phase 2+ deferred to future sprints

### Implementation Prompt

The `slice-implementation` prompt guides each slice through 5 phases:
1. **Database** — Prisma schema, migration, seed data
2. **Service** — Business logic, validation, state machines
3. **API** — Route handlers, auth middleware, error responses
4. **UI** — Pages, forms, components (if applicable)
5. **Verification** — Tests, acceptance criteria check, agent-state update

**Cross-cutting concerns are mandatory per slice** (not deferred to "hardening"):
- Pagination on all list endpoints (default 50, max 200)
- Rate limiting on public and write endpoints
- Secure error handling (no stack trace leaks)
- Input validation on all params (body, path, query)
- Security headers configured from scaffold
- Session integrity (disabled users blocked immediately)
- Observability (correlation IDs, structured logs)

---

## Hardening Phase

The Hardening Phase bridges the gap between "all slices implemented" and "production ready." This phase was identified through practice: implementation completion does not equal deployment readiness.

### Why This Phase Exists

In practice, the following problems consistently appear after implementation:
- Docker builds fail due to runtime dependencies (OpenSSL, fonts, binary targets)
- CI pipelines hang or fail due to environment differences (auth modes, reporter blocking)
- Containers start the wrong process (multi-stage target not specified)
- Services crash due to empty env vars or missing configuration
- The application works in dev but not in production mode

### Hardening Gate Sequence

1. **Deployment Validation** — Docker build + local compose up + health check pass
2. **PRD Compliance Audit** — Gap analysis between PRD requirements and actual implementation
3. **Operational Readiness** — Log levels, observability, secrets rotation, monitoring

### Critical Rule: Local Validation Before Production

**NEVER push deployment changes to production without local container validation.**

The validation sequence is:
```bash
docker compose build                              # Image builds without errors
docker compose up -d postgres redis [infra...]    # Infrastructure starts
docker compose --profile setup run --rm migrate   # Migrations apply
docker compose up -d app                          # App starts (not migrate loop)
curl http://localhost:<port>/api/health            # Health check responds
docker compose down -v                            # Cleanup
```

If any step fails locally, it will fail in production. Fix locally first.

### Common Docker/Alpine Pitfalls (Lessons Learned)

| Problem | Root Cause | Fix |
|---------|-----------|-----|
| Prisma engines fail with `libssl.so.1.1 not found` | Alpine has OpenSSL 3.x, Prisma auto-detects 1.1.x | Install `openssl` + set `binaryTargets = ["native", "linux-musl-openssl-3.0.x"]` in schema |
| `npx` downloads wrong package version | Package not in `node_modules`, npx fetches latest (breaking) | Use dedicated stage with full `node_modules` or pin version |
| App container runs wrong command | Multi-stage Dockerfile without explicit `target` in compose | Always specify `target: <stage>` in compose build config |
| Build fails fetching external resources | Docker build has no internet for Google Fonts, CDNs | Bundle all assets locally (`next/font/local`, vendored files) |
| Redis crashes on empty `--requirepass` | Empty env var passed as argument | Don't use password for internal-only services, or use conditional command |
| Container not recreated after image rebuild | `docker compose up -d` reuses existing container | Use `--force-recreate` in deploy workflows |
| CI hangs after test failure | Playwright HTML reporter opens HTTP server waiting for input | Set `open: "never"` in reporter config |

### Hardening Prompt Execution Order

1. Run `01-deployment-validation.md` — validates Docker + compose + health
2. Run `02-prd-compliance-audit.md` — identifies gaps between spec and reality
3. Run `03-operational-readiness.md` — validates logs, observability, production config
4. Update `agent-state.md` with hardening results

---

## Getting Started with a New Project

1. **Fork this template** → create your project repo
2. **Core phase:** Run `initial-spec-to-prd` → `prd-review` → `prd-hardening-review`
3. **Architecture phase:** Run prompts 00a through 04 in order
4. **Validate:** Run `stack-validation` until pass
5. **Implement:** Run `slice-implementation` per slice in dependency order
6. **Harden:** Run `deployment-validation` → `prd-compliance-audit` → `operational-readiness`
7. **Track:** Update `agent-state.md` after every session

---

## Prompt Creation Strategy

Prompts are created **on demand**, not upfront. This is deliberate:

### When to create a new prompt

1. **A step needs to be repeated** across projects or slices and lacks formal guidance
2. **An error pattern repeats** and needs a process to prevent it
3. **A new team member** needs to execute without prior context

### When NOT to create a new prompt

1. The existing generic prompt already covers the case (e.g., `slice-implementation` works for all slices)
2. The step only happens once in the project lifecycle
3. The step hasn't been validated in practice yet (prompts written without execution have errors)

### Key principle

**One well-tested generic prompt > many untested specific prompts.**

The `slice-implementation` prompt handles all slices. The `adr-resolution` prompt handles all ADRs. Creating a prompt per slice or per ADR would be over-engineering.

### Prompt validation rule

A prompt is only considered stable after it has been **executed at least once with real verification**. Until then, it is `status: draft` and may contain errors discovered during first execution.

---

## Topics

`github-copilot` `ai-agents` `vscode` `developer-tools` `prompt-engineering` `ai-governance` `multi-agent` `vertical-slices` `adr` `local-first`
