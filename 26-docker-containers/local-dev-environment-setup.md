---
title: "Local Dev Environment Setup"
folder: 26-docker-containers
purpose: "Local dev stack"
pages_estimate: 3
prerequisites: "containerization-standards"
dependencies: "none"
priority: P0
audience: "BE, FE"
status: draft-v1
generated: 2026-07-15
---

# Local Dev Environment Setup

## Overview

Every engineer — backend or mobile — brings up the full ActivityMate backend stack locally with a single `docker compose up`. This document describes that stack: the services it contains, how it's configured, and how it stays honest with what actually runs in AWS.

## Why Docker Compose for Local Dev

PRD-001 commits to Docker Compose as the initial architecture, and that choice extends naturally to local development: the same service definitions (adjusted for local-only concerns like bind mounts and exposed ports) that describe the dev/staging topology also describe a laptop's stack. This avoids maintaining two parallel descriptions of "what services exist and how they connect," which is a common source of "works on my machine" bugs. Compose stays the local-dev tool even after production migrates to ECS/EKS-class orchestration post-PMF — orchestrator choice is a deployment-target concern, not a local-loop concern.

## Stack Composition

```yaml
# docker-compose.yml (abridged)
services:
  api:
    build: { context: ./services/api, dockerfile: Dockerfile }
    ports: ["8000:8000"]
    env_file: .env.local
    volumes: ["./services/api:/app"]     # bind mount for hot reload
    depends_on: [postgres, redis, mongo, minio, meilisearch]
    command: uvicorn app.main:app --host 0.0.0.0 --reload

  postgres:
    image: postgres:16.3-bookworm
    environment: { POSTGRES_DB: activitymate, POSTGRES_USER: dev, POSTGRES_PASSWORD: dev }
    ports: ["5432:5432"]
    volumes: ["pgdata:/var/lib/postgresql/data"]

  redis:
    image: redis:7.2-bookworm
    ports: ["6379:6379"]

  mongo:
    image: mongo:7.0
    ports: ["27017:27017"]
    volumes: ["mongodata:/data/db"]

  minio:
    image: minio/minio:RELEASE.2024-06-13T22-53-53Z
    command: server /data --console-address ":9001"
    environment: { MINIO_ROOT_USER: devadmin, MINIO_ROOT_PASSWORD: devpassword }
    ports: ["9000:9000", "9001:9001"]
    volumes: ["miniodata:/data"]

  meilisearch:
    image: getmeili/meilisearch:v1.9
    environment: { MEILI_MASTER_KEY: devkey, MEILI_ENV: development }
    ports: ["7700:7700"]
    volumes: ["meilidata:/meili_data"]

volumes:
  pgdata:
  mongodata:
  miniodata:
  meilidata:
```

Every non-`api` image is pinned to a specific version matching (or closely tracking) the managed/self-hosted version running in AWS, so a version-specific bug (a Postgres extension behavior, a Meilisearch ranking-rule change) shows up locally before staging.

## Onboarding Flow (new engineer, day one)

1. Install Docker Desktop (or Docker Engine + Compose plugin on Linux) and clone the monorepo.
2. Copy `.env.local.example` to `.env.local` — contains dev-only, non-secret defaults (dummy DB passwords, dummy MinIO keys). No real secret ever belongs in this file or its example.
3. Run `docker compose up -d`, then `docker compose exec api alembic upgrade head` to apply Postgres migrations (see `../10-database/migration-strategy.md`).
4. Run the seed script (`docker compose exec api python -m app.scripts.seed_dev_data`) to populate fixture users, activities, and communities — synthetic data only, matching the categories in PRD-001 section 8 (Coffee, Cycling, Trekking, etc.).
5. Hit `http://localhost:8000/healthz` and `http://localhost:8000/docs` (FastAPI's auto-generated OpenAPI UI) to confirm the stack is live.

Target: a new engineer reaches a running, seeded local stack in under 15 minutes on a clean laptop.

## Hot Reload & Iteration Loop

The `api` service bind-mounts the source tree and runs `uvicorn --reload`, so Python code changes reflect within ~1-2 seconds without a rebuild. A full `docker compose build api` is only needed when `requirements.txt` changes (new dependency) or the Dockerfile itself changes.

## Parity Boundaries (what's intentionally different from prod)

- Local Postgres/Mongo/Redis/MinIO run single-instance with no replication, no Multi-AZ, no backups — this is fine for local dev but means a local-only bug in failover behavior will never surface here (staging exists for that, see `../25-infrastructure-cloud/environment-strategy.md`).
- Local `.env.local` values are never real credentials, real SMS/OTP provider keys, or real third-party API keys — those are stubbed or point at sandbox/test-mode endpoints.
- TLS is not terminated locally; the stack runs plain HTTP on localhost.

## Common Issues

- **Port conflicts** with a locally-installed Postgres/Redis: Compose service ports are the documented ones above; engineers running a native Postgres for other projects should stop it or remap the host port in a local override file (`docker-compose.override.yml`, gitignored).
- **Stale volumes after a schema change:** `docker compose down -v && docker compose up -d` resets all local data — safe, since it's all synthetic.

## Related Documents

- [containerization-standards.md](containerization-standards.md)
- [dockerfile-guidelines.md](dockerfile-guidelines.md)
- [../10-database/database-selection-rationale.md](../10-database/database-selection-rationale.md)
- [../10-database/migration-strategy.md](../10-database/migration-strategy.md)
- [../37-engineering-standards/documentation-standards.md](../37-engineering-standards/documentation-standards.md)
