# KSP Crime Analytics Platform — Member 1 Deliverable

**Scope:** Project Lead & DevOps — Zoho Catalyst setup, API Gateway, security layer,
CI/CD, production deployment, and documentation, per the *AI-Driven Crime Analytics
& Visualization Platform* project plan for Karnataka State Police (SCRB Division).

This repository contains **only Member 1's responsibilities**. It does not include the
DataStore schema (Member 2), ML pipelines (Member 3), business-logic API handlers
(Member 4), or dashboard/frontend code (Member 5) — those are separate workstreams
that plug into the security and deployment scaffolding provided here.

## What's in this repository

| Path | Owns |
|---|---|
| `catalyst.json` | Catalyst project/environment/function declarations |
| `auth/` | JWT issuance & validation, role-based access control |
| `gateway/` | Rate limiting, CORS policy, IP whitelisting, gateway config |
| `middleware/` | Auth middleware, structured request logging, error handling |
| `security/` | Secrets management, input validators |
| `deployment/` | Deploy scripts, preflight checks, integration checks, health check |
| `monitoring/` | Structured logging and in-memory metrics |
| `scripts/` | Environment bootstrap, Catalyst init, secret generation, cron entry point |
| `docs/` | Installation, deployment, API security, and final deployment checklist |
| `tests/` | Unit tests for the modules above |
| `.github/workflows/ci.yml` | Lint, test matrix, dependency/SAST scan, deploy preflight — runs on every push/PR to `main`/`dev` |

## Quick start

```bash
git clone <this-repo>
cd ksp-crime-analytics
./scripts/setup_env.sh          # creates venv, installs deps, copies .env.example -> .env
source .venv/bin/activate
pytest tests/ -q                # run the test suite
```

See `docs/INSTALLATION.md` for full setup instructions and `docs/DEPLOYMENT.md` for
deploying to Zoho Catalyst.

## Security model (summary)

- **Auth:** JWT bearer tokens (HS256) for officer-facing endpoints; a separate
  read-only API key for the public `/api/districts` and `/api/health` endpoints.
- **Authorization:** Role-based (`viewer`, `analyst`, `admin`, `service`) — see `auth/rbac.py`.
- **Transport:** HTTPS enforced in production via the Catalyst API Gateway.
- **Rate limiting:** 100 req/min per client in production (token-bucket, `gateway/rate_limiter.py`),
  with the Catalyst API Gateway's own limiter as the primary enforcement point.
- **IP whitelisting:** Applied to admin/ingestion endpoints only.
- **Secrets:** Loaded exclusively from environment variables (`security/secrets_manager.py`);
  never hardcoded, never logged.

Full detail in `docs/API_SECURITY.md`.

## Branching strategy

- `main` — always deployable, protected, requires PR review.
- `dev` — integration branch for the team.
- `feature/<member>-<short-description>` — individual work, merged into `dev` via PR.

## Team

Project plan defines 5 members; this deliverable covers **Member 1 (Project Lead & DevOps)**
only. See `docs/ARCHITECTURE.md` for how this scaffolding integrates with Members 2–5.
