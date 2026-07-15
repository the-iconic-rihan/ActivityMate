---
title: "Service Boundaries Domain Map"
folder: 07-architecture
purpose: "DDD bounded contexts"
pages_estimate: 6
prerequisites: "tech-stack-decision-record"
dependencies: "backend-services"
priority: P0
audience: "BE, CTO"
status: draft-v1
generated: 2026-07-15
---

# Service Boundaries Domain Map

## Overview

This document is the domain-driven design map for ActivityMate: the bounded contexts the modular monolith is split into internally today, and the future microservice boundaries if the platform ever splits per [tech-stack-decision-record.md](tech-stack-decision-record.md). Every module referenced in [system-architecture-overview.md](system-architecture-overview.md) and [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md) traces back to a bounded context defined here.

## Context

A modular monolith is only as good as its module boundaries. Drawing them along the wrong seams (e.g., splitting by technical layer — "all controllers," "all database code" — instead of by business domain) produces a monolith that is *harder* to extract from later, not easier, because a business feature ends up scattered across every "layer module." ActivityMate draws boundaries by business capability instead, following classic DDD bounded-context practice: each context owns its own data, its own vocabulary, and exposes a narrow interface to the others.

## Bounded Contexts

### 1. Identity
**Owns:** phone-OTP authentication, session/token management, account lifecycle (creation, deactivation), basic profile fields (name, DOB, gender, avatar).
**Does not own:** trust score, verification level (owned by Trust — Identity only stores *that* a phone number is verified, Trust owns *what that means*).
**Talks to:** Trust (on account creation, to seed an initial trust record), Notifications (on OTP send).
**Data store:** Postgres `users`, `sessions` tables; Redis for active session cache and OTP throttling.

### 2. Trust
**Owns:** trust score computation and history, verification levels (phone/email/selfie/gov-ID/LinkedIn), rating aggregation, cancellation/no-show/response-rate tracking that feeds the score.
**Does not own:** the report/dispute workflow itself (owned by Moderation) — Trust consumes moderation *outcomes* as one input signal.
**Talks to:** Identity (reads verification status), Activities (reads completion/cancellation events), Moderation (reads report outcomes), Notifications (trust score change alerts).
**Data store:** Postgres `trust_scores`, `trust_events`, `verifications`; MinIO for selfie/ID document blobs; background workers for score recalculation.

### 3. Activities
**Owns:** activity CRUD, RSVP/join/waitlist logic, capacity management, host/guest roles, activity lifecycle state machine (draft -> open -> full -> in-progress -> completed/cancelled).
**Does not own:** chat content for an activity's group chat (owned by Chat, linked by activity ID), discovery ranking (owned by Discovery).
**Talks to:** Trust (emits completion/cancellation events), Chat (creates a chat room on activity creation), Discovery (emits activity documents for indexing), Notifications (RSVP/status change alerts).
**Data store:** Postgres `activities`, `rsvps`, `activity_categories`, PostGIS geometry columns for location.

### 4. Chat
**Owns:** message send/receive, per-activity group chat rooms, delivery/read receipts, WebSocket connection handling for live message delivery.
**Does not own:** message *moderation* decisions (owned by Moderation, which subscribes to chat content for safety scanning).
**Talks to:** Activities (room creation on activity join), Moderation (forwards flagged content), Notifications (new-message push).
**Data store:** MongoDB for message documents (high write volume, flexible schema); Redis for presence/typing state.

### 5. Discovery
**Owns:** the activity feed, search, filters/facets, ranking/recommendation surfacing (early heuristic-based, see [../14-matching-recommendation/README.md](../14-matching-recommendation/README.md) for the matching logic Discovery surfaces).
**Does not own:** the underlying activity or trust data — Discovery is a read-optimized projection over data owned elsewhere.
**Talks to:** Activities (consumes activity documents), Trust (factors trust score into ranking), Meilisearch (index writes/reads).
**Data store:** Meilisearch index; Redis cache for hot feed pages.

### 6. Moderation
**Owns:** the report/block workflow, content moderation queue, admin review actions, ban/suspension enforcement.
**Talks to:** Trust (writes report outcomes as trust signals), Chat (receives flagged content), Activities (can force-cancel/hide an activity), Identity (can suspend an account).
**Data store:** Postgres `reports`, `moderation_actions`; MongoDB for flagged content snapshots.

### 7. Notifications
**Owns:** push notification composition and delivery (FCM), in-app notification feed, notification preferences.
**Talks to:** every other context (it is a pure downstream consumer — Activities, Trust, Chat, Moderation all emit events it delivers).
**Data store:** Redis/queue for fan-out; Postgres for notification preferences and delivery log.

### 8. Admin
**Owns:** the internal admin dashboard's backend surface — aggregated views over Trust, Moderation, Activities for ops/support use, per [../24-admin-panel/README.md](../24-admin-panel/README.md).
**Talks to:** read-mostly access into every other context through their public interfaces; write access limited to explicit admin actions (ban, override, manual verification).

## Context Map (Dependency Direction)

```
Identity  ──────►  Trust  ◄──────  Moderation
   │                 ▲                 ▲
   │                 │                 │
   ▼                 │                 │
Activities ──────────┴──────►  Chat ───┘
   │                                │
   ▼                                ▼
Discovery                     Notifications
   ▲                                ▲
   └────────────────────────────────┘
              (all contexts emit
               events consumed by
               Notifications)

Admin: read-mostly across all contexts (dashed lines, not shown)
```

Arrows indicate the direction of an interface call or event, not data flow direction — e.g., Activities calls Trust's public interface to report a completion event; Trust never reaches back into Activities' tables directly.

## Interface Discipline

Each context exposes a Python service class (e.g., `TrustService.record_activity_completion(user_id, activity_id)`) as its only entry point. No context imports another context's SQLAlchemy models directly. This is enforced via import-linter contracts in CI, per [tech-stack-decision-record.md](tech-stack-decision-record.md), and detailed in [../08-backend-services/backend-coding-guidelines.md](../08-backend-services/backend-coding-guidelines.md).

## Evolution Triggers

Each context above is the exact unit of extraction when a [scalability-plan-0-to-10m.md](scalability-plan-0-to-10m.md) trigger fires for it. Because the interface (not the implementation) is what other contexts depend on, extracting Chat into its own service means replacing an in-process function call with an HTTP/gRPC call behind the same `ChatService` interface — call sites elsewhere do not change.

## Related Documents

- [tech-stack-decision-record.md](tech-stack-decision-record.md)
- [system-architecture-overview.md](system-architecture-overview.md)
- [data-flow-diagrams.md](data-flow-diagrams.md)
- [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md)
- [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md)
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)
