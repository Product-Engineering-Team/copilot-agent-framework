# PRD Compliance Audit
## Phase: Hardening
## Purpose: Validate that the implemented system fulfills the PRD requirements
## Status: draft
## Version: 1.0.0

---

## Context

You are auditing a completed MVP implementation against its Product Requirements Document (PRD).
The goal is to produce a gap analysis that identifies:
- Requirements that are fully implemented
- Requirements that are partially implemented (with specifics on what's missing)
- Requirements that are not implemented at all
- Implementation that exists but was not specified in the PRD (scope creep or emergent needs)

---

## Inputs Required

Before starting, read these files in order:
1. `/docs/PRD.md` — The source of truth for what should exist
2. `/docs/architecture.md` — The technical design
3. `/docs/agent-state.md` — Current implementation status
4. `/docs/implementation-plan.md` — Planned slices and their scope
5. Source code (routes, services, pages) — What actually exists

---

## Audit Process

### Step 1: Extract PRD Requirements

Parse the PRD and extract every discrete requirement, user story, or acceptance criterion.
Assign each a unique ID (PRD-001, PRD-002, etc.) if not already numbered.

### Step 2: Map Requirements to Implementation

For each requirement, determine:
- **IMPLEMENTED** — Code exists, tests pass, feature is accessible
- **PARTIAL** — Some aspects work, others are missing or incomplete
- **NOT IMPLEMENTED** — No code exists for this requirement
- **DEFERRED** — Explicitly marked as Phase 2+ in implementation plan

### Step 3: Identify Unspecified Implementation

Review the codebase for features or behaviors that exist but are NOT in the PRD:
- Were they emergent needs discovered during implementation?
- Are they technical necessities (rate limiting, pagination) that should be in the PRD?
- Are they scope creep that should be removed or formally added?

### Step 4: Assess Operational Gaps

Check for operational requirements that may be implicit in the PRD:
- Authentication mode (dev credentials vs. production SSO)
- Log levels and noise in production
- Error handling and user-facing messages
- Performance requirements (response times, concurrent users)
- Data retention and cleanup policies

---

## Output Format

```markdown
# PRD Compliance Audit Report

**Date:** YYYY-MM-DD
**PRD Version:** [version or last modified date]
**Implementation Commit:** [SHA]

## Summary

| Status | Count | % |
|--------|-------|---|
| Implemented | X | X% |
| Partial | X | X% |
| Not Implemented | X | X% |
| Deferred (Phase 2+) | X | X% |

## Fully Implemented

| ID | Requirement | Evidence |
|----|-------------|----------|
| PRD-001 | [requirement text] | [file/route/test that proves it] |

## Partially Implemented

| ID | Requirement | What Works | What's Missing |
|----|-------------|------------|----------------|
| PRD-XXX | [requirement] | [working part] | [missing part] |

## Not Implemented

| ID | Requirement | Reason | Priority |
|----|-------------|--------|----------|
| PRD-XXX | [requirement] | [why missing] | [High/Medium/Low] |

## Deferred to Phase 2+

| ID | Requirement | Deferred In | Rationale |
|----|-------------|-------------|-----------|
| PRD-XXX | [requirement] | [implementation-plan ref] | [why deferred] |

## Unspecified Implementation (not in PRD)

| Feature | Location | Should Be In PRD? | Action |
|---------|----------|-------------------|--------|
| [feature] | [file/route] | Yes/No | Add to PRD / Remove / Keep as-is |

## Operational Gaps

| Area | PRD Expectation | Current State | Gap |
|------|-----------------|---------------|-----|
| Auth | [what PRD says] | [what exists] | [delta] |
| Logging | [what PRD says] | [what exists] | [delta] |
| Performance | [what PRD says] | [what exists] | [delta] |

## Recommended Next Steps

1. [Highest priority gap to close]
2. [Second priority]
3. [Third priority]
```

---

## Rules

- Be factual, not aspirational. If code doesn't exist, it's NOT IMPLEMENTED regardless of intent.
- Check actual routes, not just documentation. A documented API that returns 404 is NOT IMPLEMENTED.
- Distinguish between "works in dev mode" and "works in production mode" (e.g., credentials auth vs. SSO).
- If the PRD is ambiguous, note it as an open question rather than marking it implemented.
- Cross-reference with E2E tests — if a feature has no test coverage, note it even if code exists.
