---
title: "Chat Architecture"
folder: 16-realtime-chat-notifications
purpose: "Build vs buy"
pages_estimate: 6
prerequisites: "service-catalog"
dependencies: "none"
priority: P1
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Chat Architecture

## Overview

ActivityMate's core loop — a host creates an activity, guests join, the group coordinates logistics, they meet in person — depends on in-app chat working reliably from the first release. Chat is not a peripheral feature; it is the coordination layer that turns an RSVP into an actual meetup. This document decides how ActivityMate builds that layer: self-hosted WebSocket infrastructure versus a managed real-time messaging provider, and lays out the migration path between the two.

## Context / Problem

The modular monolith (FastAPI + PostgreSQL + Redis + MongoDB + MinIO + Meilisearch, per PRD-001 Section 12) already gives the backend team a lot to build for MVP: auth, verification, activity lifecycle, trust scoring, and moderation all land in the same release window. Real-time chat has a different engineering profile than the rest of the stack — it requires persistent connections, presence tracking, delivery guarantees, offline queuing, and horizontal scaling of stateful connections, none of which the request/response FastAPI services need. Building this correctly (not just a WebSocket echo server, but ordered delivery, multi-device sync, typing indicators, read receipts, and reconnect handling) is a multi-month effort that competes directly with the trust and safety engineering the launch depends on.

At the same time, a managed provider introduces a recurring per-MAU cost, a dependency on a third party for a feature central to the product experience, and — because ActivityMate handles conversations between strangers meeting in person in India — a data-residency and law-enforcement-access question that has to be resolved before any user data touches a non-Indian jurisdiction.

## Options Considered

**Option A — Build in-house on FastAPI + Redis Pub/Sub + WebSockets.** FastAPI supports WebSocket endpoints natively; Redis Pub/Sub (already in the stack for caching and session state) can fan out messages across multiple backend replicas. Pros: full control over data residency (all messages stay in the same PostgreSQL/MongoDB instances already in use), no recurring per-message vendor cost, tight integration with the moderation pipeline (message-moderation-safety.md) without a webhook round-trip, and no vendor lock-in. Cons: the team has to build and operate presence, multi-device delivery, offline message queuing, horizontal scaling of stateful WebSocket connections (which Redis Pub/Sub does not solve by itself — a connection-affinity or dedicated pub/sub-to-socket layer is still needed), and reconnect/backoff logic, all before the first activity can be safely coordinated in-app. This is 6-10 weeks of dedicated backend effort the team does not have at MVP.

**Option B — Managed provider (e.g., Stream Chat, Sendbird, or PubNub-class product).** Pros: production-grade delivery guarantees, presence, typing indicators, offline sync, and moderation webhooks ship in days, not months; SDKs exist for Flutter, matching the frontend stack directly; scaling of the connection layer is the vendor's problem, not ActivityMate's, during the highest-uncertainty phase of the company's life. Cons: recurring cost that scales with MAU and message volume, a data-residency question (most of these vendors default to US/EU regions; an India-region or India-compliant data processing addendum must be contractually confirmed before any user PII or message content is sent to them), and structural dependency — if the vendor changes pricing or is acquired, ActivityMate's core loop is exposed.

**Option C — Self-hosted open-source chat server (e.g., Matrix/Synapse or a Socket.IO-based OSS project) on ActivityMate's own infrastructure.** Pros: no recurring per-MAU vendor fee, full data residency control. Cons: this is Option A's engineering burden plus the operational burden of running and patching a third-party server ActivityMate did not write — worst of both worlds for a team this size at this stage. Rejected outright.

## Recommendation & Rationale

**Recommend Option B — a managed real-time messaging provider — at MVP**, selected via a vendor evaluation that makes India data residency (or a signed Data Processing Addendum guaranteeing India-compliant handling under the DPDP Act) a hard requirement, not a nice-to-have. This conserves scarce backend engineering time for the trust, safety, and verification systems that differentiate ActivityMate, while still shipping chat that feels reliable from day one.

The vendor contract must expose: (1) a webhook or server-side callback on every message send, so message-moderation-safety.md's auto-flagging can run before or immediately after delivery; (2) message export/deletion APIs sufficient to implement chat-data-retention-privacy.md's retention window and DPDP-Act deletion-on-request obligations; (3) a data processing location commitment.

**Migration trigger to self-hosted:** ActivityMate commits to re-evaluating build-vs-buy at two thresholds — (a) when projected annual messaging spend exceeds a materially significant fraction of the BE headcount cost of a 2-engineer chat team for one quarter, or (b) if the vendor cannot contractually guarantee India-compliant data handling as DPDP Act enforcement matures. At that point Option A becomes the default path, using the message schema and moderation hooks already built against the vendor's webhook contract, minimizing rework.

## Related Documents

- [notification-system-architecture.md](notification-system-architecture.md) — push/SMS/email orchestration layered on top of chat events
- [message-moderation-safety.md](message-moderation-safety.md) — moderation hooks this architecture must expose
- [chat-data-retention-privacy.md](chat-data-retention-privacy.md) — retention window enforced against the vendor's storage
- [real-time-infra-scaling.md](real-time-infra-scaling.md) — scaling plan once/if migrated to Option A
- [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md) — prerequisite service inventory
- [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md) — data residency obligations
- [../07-architecture/third-party-vendor-evaluation.md](../07-architecture/third-party-vendor-evaluation.md) — general vendor evaluation framework this decision follows
