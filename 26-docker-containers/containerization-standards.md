---
title: "Containerization Standards"
folder: 26-docker-containers
purpose: "Base image policy"
pages_estimate: 3
prerequisites: "infrastructure-cloud"
dependencies: "none"
priority: P0
audience: "BE, DO"
status: draft-v1
generated: 2026-07-15
---

# Containerization Standards

## Overview

Every ActivityMate backend service ships as a container, from a developer's laptop through CI to the AWS app tier defined in `../25-infrastructure-cloud/networking-vpc-architecture.md`. This document sets the base-image policy and multi-stage build conventions that all Dockerfiles must follow, so that "works in Docker Compose locally" reliably means "works in prod."

## Base Image Policy

- **Python services (FastAPI backend):** `python:3.12-slim-bookworm` as the base for build stages, and for the final runtime stage where possible. Slim over full (`python:3.12`) to cut image size roughly 5x; slim over Alpine because `musl`-based Alpine has repeatedly caused subtle incompatibilities with compiled Python wheels (notably `psycopg`, `cryptography`, and `pandas`-family packages), and the CPU-time debugging those incompatibilities cost is not worth Alpine's smaller footprint for a small team.
- **Static/build tooling images** (Flutter web build, if ever needed for an admin-panel web build step): official `dart`/`flutter` SDK images pinned to a specific version tag, never `latest`.
- **No `latest` tags, ever**, in any Dockerfile or Compose file, in any environment. Every `FROM` line pins an explicit version (`python:3.12.4-slim-bookworm`, not `python:3.12-slim` even) so that a base-image update never silently changes behavior on the next unrelated build.
- Base images are re-pinned deliberately on a monthly cadence (or immediately on a published CVE affecting the current pin), tracked as a routine PR, not left to drift.

## Multi-Stage Build Convention

Every service Dockerfile uses at least two stages: a **builder** stage that installs build-time dependencies and compiles/installs Python packages, and a **runtime** stage that copies only the installed environment and application code, with no compilers, no package-manager caches, and no dev dependencies.

```dockerfile
# ---- builder ----
FROM python:3.12.4-slim-bookworm AS builder
WORKDIR /build
RUN apt-get update && apt-get install -y --no-install-recommends build-essential libpq-dev \
    && rm -rf /var/lib/apt/lists/*
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

# ---- runtime ----
FROM python:3.12.4-slim-bookworm AS runtime
RUN apt-get update && apt-get install -y --no-install-recommends libpq5 \
    && rm -rf /var/lib/apt/lists/* \
    && useradd --create-home --uid 1000 appuser
WORKDIR /app
COPY --from=builder /root/.local /home/appuser/.local
COPY . .
USER appuser
ENV PATH=/home/appuser/.local/bin:$PATH
EXPOSE 8000
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

This keeps `libpq-dev` and `build-essential` (needed to compile `psycopg`) out of the final image while `libpq5` (the runtime shared library) stays in.

## Non-Root Execution

Every runtime stage creates and switches to a non-root user (`appuser`, UID 1000, matched across services for consistent volume permissions in Compose). Containers never run as root in any environment, including local dev — catching root-dependent behavior early is cheaper than discovering it in prod.

## Image Size & Layer Discipline

- `.dockerignore` is mandatory per service, excluding `.git`, `__pycache__`, `.venv`, test fixtures, and local `.env` files — keeps build context small and prevents secrets from ever entering an image layer.
- Dependency installation is layered before application code copy, so that changing application code doesn't invalidate the dependency-install cache layer.
- Target: backend service images stay under 250MB compressed. Anything larger triggers a review of what's being pulled in.

## Health & Signal Handling

Every service image exposes an HTTP health endpoint (`/healthz`) used by both Docker Compose's `healthcheck` directive locally and by the AWS target-group health check in prod. Containers run the application as PID 1 via `uvicorn` directly (no shell wrapper), ensuring `SIGTERM` on deploy/scale-down reaches the app process for graceful shutdown rather than being swallowed by an intermediate shell.

## Related Documents

- [dockerfile-guidelines.md](dockerfile-guidelines.md)
- [local-dev-environment-setup.md](local-dev-environment-setup.md)
- [container-registry-strategy.md](container-registry-strategy.md)
- [../25-infrastructure-cloud/cloud-provider-evaluation.md](../25-infrastructure-cloud/cloud-provider-evaluation.md)
- [../08-backend-services/backend-coding-guidelines.md](../08-backend-services/backend-coding-guidelines.md)
