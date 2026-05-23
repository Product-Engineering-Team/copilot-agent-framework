# Operational Readiness
## Phase: Hardening
## Purpose: Validate production configuration — logs, observability, secrets, auth mode
## Status: draft
## Version: 1.0.0

---

## Context

You are validating that the application is configured correctly for production operation.
This covers the gap between "app runs" and "app runs well in production":
- Log levels appropriate for production (minimal noise)
- Observability signals working (traces, metrics, health)
- Secrets rotated from bootstrap values
- Auth mode appropriate for environment
- Error handling doesn't leak internals

---

## Checklist

### 1. Log Levels

| Check | Expected | How to Verify |
|-------|----------|---------------|
| `LOG_LEVEL` in production | `info` or `warn` (NOT `debug`) | Check compose env / .env |
| `LOG_FORMAT` in production | `json` (structured, parseable) | Check compose env |
| No `console.log` in production code | Only structured logger calls | Grep for `console.log` in src/ |
| Error logs don't include stack traces to clients | 500 responses are generic | Test with invalid request |
| Debug logs disabled in production | No verbose output in container logs | Check running container logs |

### 2. Observability

| Check | Expected | How to Verify |
|-------|----------|---------------|
| Health endpoint responds | `/api/health` returns 200 | curl |
| Readiness probe works | `/api/health/ready` checks DB | curl |
| Liveness probe works | `/api/health/live` lightweight | curl |
| Metrics endpoint | `/api/metrics` returns Prometheus text | curl |
| Correlation IDs | `x-request-id` header on responses | curl -v |
| Traces configured | `OTEL_EXPORTER_TYPE` set | Check env |

### 3. Secrets & Auth

| Check | Expected | How to Verify |
|-------|----------|---------------|
| `AUTH_SECRET` is not bootstrap value | Rotated, min 32 chars | Check GH secrets (not the value, just that it was rotated) |
| `ALLOW_DEV_AUTH_SECRET` NOT set in prod | Absent from compose env | Check docker-compose.yml |
| `AUTH_PROVIDER` matches environment | `credentials` for dev, Azure AD for prod | Check compose env |
| Database password is not `testpass` | Rotated | Check GH secrets |
| MinIO credentials rotated | Not `minioadmin` | Check GH secrets |

### 4. Error Handling

| Check | Expected | How to Verify |
|-------|----------|---------------|
| Invalid routes return 404 (not stack trace) | Generic error | curl /api/nonexistent |
| Invalid body returns 400 with safe message | Zod error, no internals | POST with bad body |
| Auth failure returns 401/403 (not 500) | Clean error code | Request without token |
| Server errors return 500 with generic message | No stack trace in response | Force an error |

### 5. Production Auth Mode

| Check | Expected | How to Verify |
|-------|----------|---------------|
| Login works with configured provider | Credentials or OAuth | Test login flow |
| Disabled users cannot login | 403 ACCOUNT_DISABLED | Disable user, try login |
| Session expires correctly | After MAX_AGE | Check cookie/token expiry |
| Rate limiting active | 429 after threshold | Rapid requests |

---

## Output Format

```markdown
# Operational Readiness Report

**Date:** YYYY-MM-DD
**Environment:** [local-docker / staging / production]

## Summary

| Area | Status | Issues |
|------|--------|--------|
| Log Levels | PASS/FAIL | [count] |
| Observability | PASS/FAIL | [count] |
| Secrets & Auth | PASS/FAIL | [count] |
| Error Handling | PASS/FAIL | [count] |
| Production Auth | PASS/FAIL | [count] |

## Issues Found

| # | Area | Issue | Severity | Fix |
|---|------|-------|----------|-----|
| 1 | [area] | [description] | High/Medium/Low | [action] |

## Recommendations

1. [Most critical action]
2. [Second priority]
3. [Third priority]
```

---

## Rules

- This audit runs AFTER deployment-validation passes (app must be running)
- Test against the actual running container, not source code assumptions
- If auth mode is `credentials` in production, flag it as a gap (should be SSO/OAuth for enterprise)
- Log level `debug` in production is always a failure
- Any secret that matches a known default (`testpass`, `minioadmin`, bootstrap values) is a failure
