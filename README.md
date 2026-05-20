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

## Architecture Phase Gates

The architecture phase follows a structured validation sequence before implementation begins:

1. **Stack Definition** — Translate architecture into concrete technology choices (`/agents/stack.md`)
2. **Stack Validation** — Verify alignment between stack.md and source documents (PRD, architecture, implementation plan). Produces a validation report with pass/fail status.
3. **Stack Remediation** — Apply human-approved patches from a failed validation report. Only modifies stack.md; never touches source documents.
4. **Re-Validation** — Re-run stack-validation after remediation to confirm pass status.
5. **Implementation Gate** — Only proceed to slice implementation if stack validation passes. Failures require resolution (patches, ADRs, or document updates) before continuing.

The cycle `validation → remediation → re-validation` may repeat until all discrepancies are resolved or explicitly deferred via ADRs.

Each project should maintain versioned prompts for these gates in its `/ai/prompts/architecture/` directory. The validation report and remediation log serve as auditable artifacts for governance and traceability.

## Topics

`github-copilot` `ai-agents` `vscode` `developer-tools` `prompt-engineering` `ai-governance` `multi-agent`