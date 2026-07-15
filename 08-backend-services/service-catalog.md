---
title: "Service Catalog"
folder: 08-backend-services
purpose: "Service registry"
pages_estimate: 3
prerequisites: "service-boundaries-domain-map"
dependencies: "none"
priority: P0
audience: "BE, DO"
status: draft-v1
generated: 2026-07-15
---

# Service Catalog

## Overview

A living registry of every module inside the ActivityMate FastAPI monolith: what it owns, who owns it, and what it depends on. This is the operational counterpart to [../07-architecture/service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md) — that document explains the domain reasoning, this one is the reference table engineers check when touching a module they don't own.

## How to Use This Catalog

Every module lives at `app/modules/<name>/` in the monolith repo. Before adding a new module, or a new cross-module dependency, this table must be updated in the same PR — CI checks that the import-linter contract (see [backend-coding-guidelines.md](backend-coding-guidelines.md)) matches the "Depends on" column below.

## Registry

| Module | Path | Owns | Public Interface | Depends On | Data Store(s) | Owner |
|---|---|---|---|---|---|---|
| `identity` | `app/modules/identity/` | Phone-OTP auth, sessions, account lifecycle, base profile | `IdentityService` | none (leaf module) | Postgres (`users`, `sessions`), Redis (session cache, OTP throttle) | BE Lead |
| `trust` | `app/modules/trust/` | Trust score computation, verification levels, rating aggregation | `TrustService` | `identity` (verification status) | Postgres (`trust_scores`, `trust_events`, `verifications`), MinIO (ID/selfie blobs) | BE / Trust & Safety |
| `activities` | `app/modules/activities/` | Activity CRUD, RSVP, capacity, lifecycle state machine | `ActivitiesService` | `identity`, `trust` | Postgres (`activities`, `rsvps`), PostGIS geometry columns | PM / BE |
| `chat` | `app/modules/chat/` | Messages, chat rooms, WebSocket delivery | `ChatService` | `identity`, `activities` (room creation) | MongoDB (message docs), Redis (presence) | BE / Infra |
| `discovery` | `app/modules/discovery/` | Feed, search, filters, ranking | `DiscoveryService` | `activities`, `trust` | Meilisearch, Redis (feed cache) | BE / ML |
| `moderation` | `app/modules/moderation/` | Reports, blocks, admin review queue, enforcement | `ModerationService` | `identity`, `chat`, `activities`, `trust` | Postgres (`reports`, `moderation_actions`), MongoDB (flagged content snapshots) | Trust & Safety Ops |
| `notifications` | `app/modules/notifications/` | Push composition/delivery, in-app feed, preferences | `NotificationsService` | all modules (event consumer only) | Redis (queue), Postgres (preferences, delivery log) | BE / Infra |
| `admin` | `app/modules/admin/` | Internal dashboard backend surface | `AdminService` | read access to all modules' public interfaces | none (aggregates others) | BE / Internal Tools |

## Cross-Module Call Rules

- A module may call another module's public `*Service` interface only. Direct ORM model imports across module boundaries fail CI (import-linter contract).
- `notifications` and `admin` are the only modules permitted to depend on "all modules" — every other dependency edge above must be justified by an actual synchronous call need; anything that can be event-driven (via the background job queue, see [background-jobs-queue-architecture.md](background-jobs-queue-architecture.md)) should be, to keep coupling loose ahead of any future extraction.
- New modules are added here at creation time, before the first line of module code is merged, with an initial "Depends On" column that becomes the enforced import-linter contract.

## Related Documents

- [../07-architecture/service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md)
- [backend-coding-guidelines.md](backend-coding-guidelines.md)
- [background-jobs-queue-architecture.md](background-jobs-queue-architecture.md)
- [../07-architecture/system-architecture-overview.md](../07-architecture/system-architecture-overview.md)
