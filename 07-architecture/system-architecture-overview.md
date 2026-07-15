---
title: "System Architecture Overview"
folder: 07-architecture
purpose: "Top-level diagram"
pages_estimate: 4
prerequisites: "product-strategy-doc"
dependencies: "none"
priority: P0
audience: "BE, FE, DO, SEC"
status: draft-v1
generated: 2026-07-15
---

# System Architecture Overview

## Overview

This is the one diagram every ActivityMate engineer should be able to redraw from memory: client apps, gateway, the modular monolith, data stores, background workers, and third parties. Every other document in `07-architecture/` and `08-backend-services/` zooms into one box on this diagram.

## Context

ActivityMate is a modular monolith (see [tech-stack-decision-record.md](tech-stack-decision-record.md)) deployed via Docker Compose in a single India cloud region ([multi-region-strategy.md](multi-region-strategy.md)). At MVP scale the goal is one deployable backend unit, a small set of managed data stores, and a thin layer of India-specific third parties (SMS/OTP, maps; payments deferred to P2). This document freezes that shape so every subsequent spec shares one mental model.

## System Diagram

```
                              +---------------------------+
                              |   Flutter Mobile App       |
                              | (Android-first, iOS P1)    |
                              +--------------+--------------+
                                             | HTTPS/TLS (REST + WebSocket)
                                             v
                              +---------------------------+
                              |  nginx: TLS term, coarse   |
                              |  rate limit, WS upgrade    |
                              +--------------+--------------+
                                             |
                    +------------------------+------------------------+
                    v                                                 v
       +---------------------------+                     +---------------------------+
       |  FastAPI Modular Monolith  |                     |  WebSocket Gateway         |
       |  (uvicorn/gunicorn)        |<------------------->|  (chat, presence, live     |
       |                             |    shared auth      |   notifications)           |
       |  Modules (in-process):      |                     +---------------------------+
       |  - identity (OTP, sessions) |
       |  - trust (score, verify)    |
       |  - activities (CRUD, RSVP)  |
       |  - chat (message API)       |
       |  - discovery (feed/search)  |
       |  - moderation/reports       |
       |  - notifications            |
       |  - admin                    |
       +--------------+---------------+
                       |
   +-------------------+-------+-------------+-------------+---------------+
   v                   v       v             v             v               v
+---------+     +-----------+ +-----------+ +-----------+ +-------------+
|PostgreSQL|     | MongoDB   | |  Redis    | |  MinIO    | | Meilisearch |
|+ PostGIS |     |(chat msgs,| |(sessions, | |(selfies,  | | (activity & |
|(users,   |     | feed docs,| | cache,    | | ID docs,  | |  profile    |
|activities,|    | audit log)| | rate-limit| | activity  | |  search     |
|trust,    |     |           | | counters, | | photos)   | |  index)     |
|ratings)  |     |           | | queues)   | |           | |             |
+---------+     +-----------+ +-----------+ +-----------+ +-------------+
                       |
                       v
             +---------------------------+
             |  Background Workers        |
             |  (RQ/Celery on Redis)      |
             |  - trust score recalc      |
             |  - notification fan-out    |
             |  - moderation pipeline     |
             |  - Meilisearch reindex     |
             +---------------------------+

  Third parties (outbound only):
  +-------------+   +--------------+   +----------------+
  | SMS/OTP      |   | Push (FCM)   |   | Maps provider   |
  | (DLT-        |   |              |   | (see maps-      |
  | compliant)   |   |              |   | location docs)  |
  +-------------+   +--------------+   +----------------+
```

## Component Notes

- **Client apps.** Single Flutter codebase, Android-first given device mix in Mumbai, iOS as P1. See [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md).
- **Gateway.** nginx terminates TLS and applies coarse rate limiting; per-endpoint, signal-aware limits live in the app layer — [../08-backend-services/rate-limiting-standards.md](../08-backend-services/rate-limiting-standards.md).
- **FastAPI monolith.** One deployable unit, internally organized by domain module with enforced import boundaries mapping 1:1 to [service-boundaries-domain-map.md](service-boundaries-domain-map.md) and [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md).
- **WebSocket gateway.** Runs alongside the monolith at MVP scale (not yet a separate service) to share auth/session code with REST; first extraction candidate once connection counts stress the event loop — [scalability-plan-0-to-10m.md](scalability-plan-0-to-10m.md).
- **PostgreSQL + PostGIS.** System of record for relational and geospatial data: users, activities, RSVPs, trust scores, ratings, reports.
- **MongoDB.** High-write, document-shaped data only: chat bodies, denormalized feed documents, audit logs — never identity or money-adjacent data.
- **Redis.** Session/token cache, OTP throttling, discovery feed cache, and the background-job broker.
- **MinIO.** S3-compatible object storage for selfies, ID docs, and activity photos — self-hosted for cost and data-residency control over sensitive verification media.
- **Meilisearch.** Typo-tolerant search over activities and public profile fields, fed asynchronously off the write path.
- **Background workers.** Redis-backed consumers for anything that must not block a request: trust recalculation, notification fan-out, moderation, reindexing. See [../08-backend-services/background-jobs-queue-architecture.md](../08-backend-services/background-jobs-queue-architecture.md).
- **Third parties.** Deliberately minimal at MVP: DLT-compliant SMS/OTP, FCM push, one maps provider — each scored in [third-party-vendor-evaluation.md](third-party-vendor-evaluation.md).

## Request Path Examples

1. **OTP login.** App -> gateway -> identity module -> Redis (throttle check) -> SMS provider -> Postgres (user record) -> Redis (session token). No Mongo, no Meilisearch.
2. **Browse nearby activities.** App -> gateway -> discovery module -> Meilisearch (text/filter) enriched with a PostGIS distance filter -> Redis cache of hot results.
3. **Join activity.** App -> gateway -> activities module -> Postgres (RSVP write, capacity check) -> background worker (notify host, recalc trust exposure) -> Mongo (feed/audit doc) -> WebSocket gateway (live push to host).

## Related Documents

- [tech-stack-decision-record.md](tech-stack-decision-record.md)
- [service-boundaries-domain-map.md](service-boundaries-domain-map.md)
- [data-flow-diagrams.md](data-flow-diagrams.md)
- [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md)
- [../10-database/database-selection-rationale.md](../10-database/database-selection-rationale.md)
- [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md)
