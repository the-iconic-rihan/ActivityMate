---
title: "Dockerfile Guidelines"
folder: 26-docker-containers
purpose: "Dockerfile conventions"
pages_estimate: 2
prerequisites: "containerization-standards"
dependencies: "none"
priority: P0
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Dockerfile Guidelines

## Overview

`containerization-standards.md` sets the policy (base images, multi-stage builds, non-root execution). This document is the concrete, per-service checklist engineers follow when writing or reviewing an actual Dockerfile for an ActivityMate backend service — currently the modular monolith itself, plus any standalone worker processes (background jobs per `../08-backend-services/background-jobs-queue-architecture.md`).

## Checklist for Every Service Dockerfile

1. **Pin the base image tag exactly**, including the patch version (`python:3.12.4-slim-bookworm`, never `python:3.12-slim`).
2. **Order instructions from least to most frequently changing** so Docker's layer cache is useful: system packages → Python dependencies → application code, in that order.
3. **Copy `requirements.txt` (or `pyproject.toml`/lockfile) before copying the rest of the source tree.** This is the single highest-value line-ordering rule — it means editing application code doesn't force a dependency reinstall on every build.
4. **Use `--no-cache-dir` with `pip install`** to avoid bloating the image with pip's download cache.
5. **Never `COPY . .` before installing dependencies.** Always dependencies first, code second.
6. **Run as a non-root `appuser` (UID 1000)**, created in the runtime stage, per `containerization-standards.md`.
7. **Set `PYTHONDONTWRITEBYTECODE=1` and `PYTHONUNBUFFERED=1`** as environment variables — the former avoids stray `.pyc` files in the image, the latter ensures logs stream immediately rather than buffering, which matters for the structured logging pipeline in `../28-monitoring-observability/logging-standards.md`.
8. **Declare `EXPOSE`** for documentation purposes, even though Compose and ECS both set the actual port mapping explicitly.
9. **Define a `HEALTHCHECK`** (or rely on the equivalent Compose/ECS-level health check — pick one, don't duplicate both inconsistently) pointing at `/healthz`.
10. **No secrets, ever, via `ARG` or `ENV` baked into the image.** Secrets are injected at container start (Compose `env_file` locally, ECS task-definition secrets in AWS) — never `ARG DATABASE_PASSWORD`, which would persist in image history even if unset later.
11. **Every Dockerfile lives at the root of its service directory** (`services/api/Dockerfile` for the FastAPI monolith, `services/worker/Dockerfile` for background workers), not in a shared top-level location — keeps build context scoped and avoids accidentally leaking one service's files into another's image.

## Standard Template (FastAPI service)

```dockerfile
FROM python:3.12.4-slim-bookworm AS builder
WORKDIR /build
RUN apt-get update && apt-get install -y --no-install-recommends build-essential libpq-dev \
    && rm -rf /var/lib/apt/lists/*
COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt

FROM python:3.12.4-slim-bookworm AS runtime
ENV PYTHONDONTWRITEBYTECODE=1 PYTHONUNBUFFERED=1
RUN apt-get update && apt-get install -y --no-install-recommends libpq5 curl \
    && rm -rf /var/lib/apt/lists/* \
    && useradd --create-home --uid 1000 appuser
WORKDIR /app
COPY --from=builder /root/.local /home/appuser/.local
COPY --chown=appuser:appuser . .
USER appuser
ENV PATH=/home/appuser/.local/bin:$PATH
EXPOSE 8000
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s \
  CMD curl -f http://localhost:8000/healthz || exit 1
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

`curl` is the one exception to "no extra packages in runtime" — it's needed for the `HEALTHCHECK` directive and is small enough (~1MB) not to matter against the size targets in `containerization-standards.md`.

## Worker Process Variant

Background-job workers (Celery/RQ-style, per `../08-backend-services/background-jobs-queue-architecture.md`) reuse the same builder stage but swap the final `CMD` — no `EXPOSE`, no `HEALTHCHECK` via HTTP (workers report liveness through the queue's own heartbeat instead).

## Review Bar

A Dockerfile change is reviewed with the same rigor as application code (`../37-engineering-standards/code-review-standards.md`). The most common rejection reasons in practice: unpinned base images, `COPY . .` placed before dependency installation, and running as root.

## Related Documents

- [containerization-standards.md](containerization-standards.md)
- [../08-backend-services/backend-coding-guidelines.md](../08-backend-services/backend-coding-guidelines.md)
- [../37-engineering-standards/code-review-standards.md](../37-engineering-standards/code-review-standards.md)
