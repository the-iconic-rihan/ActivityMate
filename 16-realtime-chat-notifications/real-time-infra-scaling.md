---
title: "Real Time Infra Scaling"
folder: 16-realtime-chat-notifications
purpose: "Connection scaling"
pages_estimate: 3
prerequisites: "chat-architecture"
dependencies: "infrastructure-cloud"
priority: P2
audience: "BE, DO"
status: draft-v1
generated: 2026-07-15
---

# Real-Time Infra Scaling

## Overview

chat-architecture.md commits ActivityMate to a managed chat provider at MVP specifically to avoid owning connection-layer scaling during the pre-PMF phase. This document is the deferred P2 plan for what changes if and when that migration trigger fires and ActivityMate brings real-time messaging in-house — the connection-scaling design for a self-hosted WebSocket layer.

## Context / Problem

A managed provider abstracts away a genuinely hard distributed-systems problem: persistent WebSocket connections are stateful, and a stateful connection cannot be load-balanced the way a stateless HTTP request can — the server holding a given user's socket is the only server that can push to them, which breaks the usual scale-out-behind-a-load-balancer pattern FastAPI's other endpoints rely on. If the migration trigger in chat-architecture.md fires (cost threshold or a data-residency requirement the vendor cannot meet), ActivityMate needs this problem already solved on paper before committing engineering time to it under pressure.

## The Actual Policy (Scaling Design)

**Connection layer:** FastAPI's native WebSocket support runs behind a Layer 4 load balancer with sticky sessions (client-IP or cookie-based affinity), so a reconnect from the same client lands back on a server that can resume its session state cheaply, though session state itself is not tied to that server — see below.

**Fan-out via Redis Pub/Sub:** message delivery does not depend on the sending and receiving user being connected to the same backend replica. When user A sends a message, the API writes it to the message store (PostgreSQL/MongoDB per the schema already in use) and publishes an event to a Redis Pub/Sub channel keyed by conversation ID. Every backend replica subscribes to channels for the connections it currently holds; whichever replica holds user B's socket receives the published event and pushes it down that socket. This decouples "which server processes the send" from "which server delivers to the recipient," which is the core requirement for horizontal scaling of the connection layer.

**Presence tracking:** online/offline/typing state is held in Redis (already in the stack) with short TTLs (e.g., a few seconds for typing, tens of seconds for online presence with heartbeat renewal), never in a database table, since presence is inherently ephemeral and would otherwise generate write load disproportionate to its value.

**Horizontal scaling triggers:** each backend replica has a practical concurrent-connection ceiling driven by OS file-descriptor limits and per-connection memory overhead; the scaling plan adds replicas behind the load balancer as concurrent WebSocket connections approach a defined per-replica threshold (validated under load test before launch of the self-hosted path, not assumed), with the Redis Pub/Sub layer itself scaled to a clustered configuration once single-node Redis throughput becomes the bottleneck rather than connection count.

**Reconnect and offline delivery:** clients reconnect with exponential backoff; on reconnect, the client requests any messages since its last-seen message ID (a per-conversation cursor), so no message is lost to a dropped connection — this mirrors the offline-sync guarantee ActivityMate already gets for free from the managed provider at MVP, and must be validated with equivalent reliability before cutover.

## Enforcement

This document is not activated engineering work at MVP — it is the pre-approved design so that if the migration trigger in chat-architecture.md fires, the team is executing a reviewed plan, not designing under deadline pressure. Before any self-hosted rollout, the design here must pass a load test simulating projected peak concurrent Mumbai users with realistic message rates, owned jointly by BE and DevOps (infrastructure-cloud).

## Related Documents

- [chat-architecture.md](chat-architecture.md) — the build-vs-buy decision and migration trigger this plan activates under
- [../25-infrastructure-cloud/cloud-provider-evaluation.md](../25-infrastructure-cloud/cloud-provider-evaluation.md) — hosting environment this scaling plan runs on
- [../25-infrastructure-cloud/networking-vpc-architecture.md](../25-infrastructure-cloud/networking-vpc-architecture.md) — load balancer and networking layer referenced above
- [../07-architecture/scalability-plan-0-to-10m.md](../07-architecture/scalability-plan-0-to-10m.md) — company-wide scaling context this fits into
