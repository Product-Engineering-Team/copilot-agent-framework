# Deployment Validation
## Phase: Hardening
## Purpose: Validate that the application builds, deploys, and responds correctly in containers
## Status: draft
## Version: 1.0.0

---

## Context

You are validating that the application can be built, deployed, and accessed
as a containerized service. This prompt is executed BEFORE pushing any
Docker/compose changes to production.

This validation catches the class of bugs that only appear in containers:
- Missing system libraries (OpenSSL, libc variants)
- Wrong binary targets (Prisma on Alpine)
- Multi-stage build target confusion
- Missing environment variables
- Network/port configuration issues
- Service dependency ordering

---

## Inputs Required

1. `Dockerfile` — Multi-stage build definition
2. `docker-compose.yml` — Service orchestration
3. `.env.example` — Required environment variables
4. Application health endpoint path

---

## Validation Sequence

Execute these steps IN ORDER. Stop at the first failure and fix before continuing.

### Step 1: Build All Images

```bash
export AUTH_SECRET=test-validation-secret-32chars
export POSTGRES_PASSWORD=testpass
export MINIO_ACCESS_KEY=minioadmin
export MINIO_SECRET_KEY=minioadmin
export APP_PORT=8085

docker compose -f docker-compose.yml --profile setup build
```

**Pass criteria:**
- All stages complete without error
- No warnings about missing libraries or unsupported platforms
- Final image size is reasonable (< 500MB for Node.js apps)

**Common failures:**
- `next/font/google` fails → bundle fonts locally
- Prisma `libssl` not found → add `openssl` to Alpine + set `binaryTargets`
- `npm ci` fails → check `.dockerignore` isn't excluding `package-lock.json`

### Step 2: Start Infrastructure

```bash
docker compose up -d postgres redis [other-infra]
```

**Pass criteria:**
- All infra containers reach `healthy` status
- No crash loops (check `docker compose ps`)

**Common failures:**
- Redis `--requirepass` with empty value → remove or use conditional
- Postgres volume permissions → check user/group in compose

### Step 3: Run Migrations

```bash
docker compose --profile setup run --rm migrate
```

**Pass criteria:**
- Migrations apply successfully OR report "no pending migrations"
- Container exits with code 0
- Container does NOT start the application server

**Common failures:**
- Wrong Dockerfile target → container runs app CMD instead of migrate
- `npx` downloads wrong version → use installed CLI from node_modules
- Missing OpenSSL → Prisma engines can't load

### Step 4: Start Application

```bash
docker compose up -d app
```

**Pass criteria:**
- Container starts and stays running (no restart loop)
- Logs show application server ready (e.g., "Ready in Xms")
- Container is NOT running migrations

**Common failures:**
- No `target: runner` in compose → uses last Dockerfile stage (migrator)
- Missing env vars → app crashes on startup
- Prisma client can't connect → wrong DATABASE_URL or missing OpenSSL

### Step 5: Health Check

```bash
curl -s http://localhost:${APP_PORT}/api/health
```

**Pass criteria:**
- Returns HTTP 200
- Response includes `status: "healthy"` or `status: "degraded"` (degraded is OK if optional services like MinIO are down)
- Database check passes

**Common failures:**
- Port not mapped → check compose ports config
- App not listening → check HOSTNAME=0.0.0.0 in Dockerfile ENV

### Step 6: Cleanup

```bash
docker compose down -v
```

---

## Output Format

```markdown
# Deployment Validation Report

**Date:** YYYY-MM-DD
**Commit:** [SHA]

| Step | Status | Duration | Notes |
|------|--------|----------|-------|
| Build images | PASS/FAIL | Xs | [notes] |
| Start infra | PASS/FAIL | Xs | [notes] |
| Run migrations | PASS/FAIL | Xs | [notes] |
| Start app | PASS/FAIL | Xs | [notes] |
| Health check | PASS/FAIL | Xs | [response] |
| Cleanup | PASS/FAIL | Xs | [notes] |

**Overall:** PASS / FAIL

**Issues found:**
- [issue 1]
- [issue 2]

**Fixes applied:**
- [fix 1]
- [fix 2]
```

---

## Rules

- NEVER skip local validation and push directly to production
- If ANY step fails, fix it locally before proceeding
- Document every fix — these become lessons learned for the framework
- After fixing, re-run the ENTIRE sequence from Step 1 (not just the failed step)
- The validation must pass with a CLEAN build (`--no-cache`) at least once
