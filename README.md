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

## Topics

`github-copilot` `ai-agents` `vscode` `developer-tools` `prompt-engineering` `ai-governance` `multi-agent`