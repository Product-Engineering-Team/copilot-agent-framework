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

---

## Getting Started with a New Project

1. **Fork this template** → create your project repo
2. **Core phase:** Run `initial-spec-to-prd` → `prd-review` → `prd-hardening-review`
3. **Architecture phase:** Run prompts 00a through 04 in order
4. **Validate:** Run `stack-validation` until pass
5. **Implement:** Run `slice-implementation` per slice in dependency order
6. **Track:** Update `agent-state.md` after every session

---

## Topics

`github-copilot` `ai-agents` `vscode` `developer-tools` `prompt-engineering` `ai-governance` `multi-agent` `vertical-slices` `adr` `local-first`
