---
title: "Openapi Spec Index"
folder: 11-api
purpose: "Spec registry"
pages_estimate: 2
prerequisites: "api-design-guidelines"
dependencies: "none"
priority: P0
audience: "BE, FE, QA"
status: draft-v1
generated: 2026-07-15
---

# OpenAPI Spec Index

## Overview

This document is the index into ActivityMate's machine-readable OpenAPI 3.x specifications, which FastAPI generates automatically from route definitions and Pydantic models per module. It exists so backend, frontend, and QA engineers have one place to find the current contract for any module without hunting through source code.

## Context

Because the backend is a modular monolith (see [../07-architecture/system-architecture-overview.md](../07-architecture/system-architecture-overview.md) and [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md)), each internal module (auth, activities, communities, trust, chat, admin) exposes its own router with its own tag in a single unified OpenAPI document served at `/openapi.json`, with an interactive Swagger UI at `/docs` and ReDoc at `/redoc` in non-production environments. There is one authoritative spec per API version (see [api-versioning-strategy.md](api-versioning-strategy.md)), not one per module — module boundaries appear as OpenAPI tags, not separate documents, so client code generation and cross-module contract review both work against a single source of truth.

## Spec Registry

| Module (OpenAPI tag) | Primary resources | Owning folder |
|---|---|---|
| `auth` | `/v1/auth/otp/request`, `/v1/auth/otp/verify`, `/v1/auth/refresh`, `/v1/auth/social` | [../12-auth/](../12-auth/README.md) |
| `users` | `/v1/users/{id}`, `/v1/users/{id}/trust-score`, `/v1/users/{id}/verification` | [../12-auth/](../12-auth/README.md), [../10-database/](../10-database/README.md) |
| `activities` | `/v1/activities`, `/v1/activities/{id}`, `/v1/activities/{id}/participants` | [../15-activities-communities/](../15-activities-communities/README.md) |
| `communities` | `/v1/communities`, `/v1/communities/{id}/members` | [../15-activities-communities/](../15-activities-communities/README.md) |
| `trust-safety` | `/v1/reports`, `/v1/blocks`, `/v1/trust-events` | [../13-trust-safety-fraud/](../13-trust-safety-fraud/README.md) |
| `chat` | `/v1/conversations`, `/v1/conversations/{id}/messages` | [../16-realtime-chat-notifications/](../16-realtime-chat-notifications/README.md) |
| `notifications` | `/v1/notifications`, `/v1/notifications/preferences` | [../16-realtime-chat-notifications/](../16-realtime-chat-notifications/README.md) |
| `media` | `/v1/media/upload-url`, `/v1/media/{id}` | [../18-media-storage/](../18-media-storage/README.md) |
| `admin` | `/v1/admin/*` (internal-only, separate auth scope) | [../24-admin-panel/](../24-admin-panel/README.md) |

## Publishing and Consumption

The FastAPI-generated spec is exported as a static `openapi.json` artifact on every merge to `main` via the CI pipeline (see [../27-cicd-release/cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md)), and diffed against the previous version to flag breaking changes automatically — a breaking diff on a non-deprecated version blocks merge per [api-versioning-strategy.md](api-versioning-strategy.md). The Flutter team consumes this artifact to generate typed Dart API clients rather than hand-writing HTTP calls, keeping the mobile client's request/response models mechanically in sync with the backend contract. QA uses the same artifact to generate contract tests that run in CI ahead of manual test-plan execution, per [../29-testing-qa/README.md](../29-testing-qa/README.md).

## Related Documents

- [api-design-guidelines.md](api-design-guidelines.md)
- [api-versioning-strategy.md](api-versioning-strategy.md)
- [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md)
- [../27-cicd-release/cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md)
