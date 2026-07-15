# 26-docker-containers/

**Phase:** Phase 6 — Platform Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** DevOps  
**Depends on:** infrastructure-cloud  
**Document count:** 4

## Purpose

Containerization standards for consistent local, CI, and production parity — the Docker Compose stack PRD-001 already commits to.

## Why this folder exists

Kept separate from infrastructure-cloud because container standards are a daily developer-facing concern, distinct from cloud-account-level infrastructure decisions.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [containerization-standards.md](containerization-standards.md) | Base image policy | Base image policy and multi-stage build conventions. | 3 | infrastructure-cloud | none | P0 | BE, DO |
| [dockerfile-guidelines.md](dockerfile-guidelines.md) | Dockerfile conventions | Per-service Dockerfile conventions. | 2 | containerization-standards | none | P0 | BE |
| [local-dev-environment-setup.md](local-dev-environment-setup.md) | Local dev stack | The docker-compose-based local development stack. | 3 | containerization-standards | none | P0 | BE, FE |
| [container-registry-strategy.md](container-registry-strategy.md) | Registry strategy | Registry choice and image tagging/retention policy. | 2 | containerization-standards | infrastructure-cloud | P1 | DO |
