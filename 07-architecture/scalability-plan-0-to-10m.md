---
title: "Scalability Plan 0 To 10M"
folder: 07-architecture
purpose: "Growth-stage plan"
pages_estimate: 6
prerequisites: "tech-stack-decision-record"
dependencies: "infrastructure-cloud"
priority: P1
audience: "BE, DO, CTO"
status: draft-v1
generated: 2026-07-15
---

# Scalability Plan 0 To 10M

## Overview

The modular monolith decision in [tech-stack-decision-record.md](tech-stack-decision-record.md) is correct for launch, not forever. This document lays out, stage by stage, what breaks first as ActivityMate grows from zero to roughly 10 million registered users in Mumbai and beyond, and what the concrete, measured trigger is for each infrastructure change — never a calendar date, always a threshold.

## Context

ActivityMate's load profile is unusual for a "social" app: writes are bursty around evenings and weekends (activity creation, RSVPs), reads are geo-heavy (PostGIS proximity queries), and a meaningful fraction of traffic is long-lived WebSocket connections (chat, live activity status) rather than short REST calls. Each stage below is sized against that profile, not generic SaaS assumptions.

## Stage 0: Launch (0 – 50K users, single Mumbai city)

- **Infra:** Single Docker Compose host (or a small managed VM cluster) running the FastAPI monolith (2-4 uvicorn workers), Postgres+PostGIS, Redis, MongoDB, MinIO, Meilisearch, all co-located.
- **Capacity assumption:** Peak concurrent WebSocket connections in the low thousands; Postgres handles both OLTP writes and PostGIS geo queries directly with no read replica.
- **Known ceiling:** A single Postgres primary and a single monolith process group. This stage is intentionally over-simple — the entire point is to not build for scale that doesn't exist yet.

## Stage 1: Read Replica + Cache Warm-up (Trigger: >70% sustained CPU on Postgres primary, or read query p95 latency >150ms)

- Add a Postgres **read replica**; route read-only discovery/search-adjacent queries to it, keep all writes and anything trust/safety-critical (fraud checks, RSVP capacity) on the primary to avoid replication-lag correctness bugs.
- Expand Redis caching per [../08-backend-services/caching-strategy.md](../08-backend-services/caching-strategy.md): discovery feed pages and geo bucket lookups get explicit TTLs.
- **Trigger, not calendar:** measured via [../28-monitoring-observability/README.md](../28-monitoring-observability/README.md) dashboards, not a "month 6" plan.

## Stage 2: WebSocket Gateway Extraction (Trigger: concurrent WS connections >20K, or WS process memory footprint starves REST worker capacity on the same host)

- Extract the WebSocket gateway (chat, live notifications, presence) into its own deployable service, per the seam already drawn in [service-boundaries-domain-map.md](service-boundaries-domain-map.md) and [system-architecture-overview.md](system-architecture-overview.md).
- It continues to call into the monolith's identity module for auth (network call replacing what was a function call) — this is the first live proof that the modular-monolith boundaries hold under real extraction.
- Chat message writes move fully behind the queue (background workers) to decouple message persistence from live delivery.

## Stage 3: Discovery / Search Module Extraction (Trigger: Meilisearch index rebuild frequency or query volume degrades monolith request latency, or matching/discovery logic needs a deploy cadence independent of the rest of the app)

- Extract discovery + matching-adjacent code into its own service. This is expected to be the second extraction, not the first, because the WebSocket load pattern (long-lived connections, different resource shape) diverges from CRUD load faster than discovery does.
- Meilisearch and its reindexing workers move with it.

## Stage 4: Postgres Write Sharding / Partitioning (Trigger: single-primary write throughput approaches provider ceiling, or table sizes on `activities`, `messages`-adjacent, or `trust_events` degrade index performance despite tuning)

- Partition high-growth tables (activity history, trust score events, ratings) by time range or by city/region first — city-based partitioning is a natural fit since ActivityMate expands city by city, and most queries are already scoped to one city.
- True multi-primary sharding is deferred unless a single logical partition itself exceeds capacity; this is a 10M-scale concern, not a Stage 1-3 one.

## Stage 5: Full Domain Service Split (Trigger: team size makes single-codebase deploy contention a measured velocity cost — see [tech-stack-decision-record.md](tech-stack-decision-record.md) migration triggers)

- Remaining modules (identity, trust, activities, moderation, admin) are extracted individually, each behind the interface boundary already enforced since Stage 0.
- Each extraction is logged as its own ADR in [../39-decision-records-rfcs/adr-index.md](../39-decision-records-rfcs/adr-index.md), justified by its own specific trigger — this stage is explicitly not "do it all at once."

## Summary Table

| Stage | Trigger | Change | Approx. Scale |
|---|---|---|---|
| 0 | Launch | Single Compose host, one of everything | 0-50K users |
| 1 | Postgres CPU >70% sustained / read p95 >150ms | Read replica + cache expansion | ~50K-300K |
| 2 | Concurrent WS >20K or memory contention | WebSocket gateway extraction | ~300K-1M |
| 3 | Search/discovery load degrades monolith latency | Discovery module extraction | ~1M-3M |
| 4 | Write throughput near provider ceiling | Table partitioning (time/city) | ~3M-8M |
| 5 | Team-size deploy contention | Full domain service split | ~8M-10M+ |

## Related Documents

- [tech-stack-decision-record.md](tech-stack-decision-record.md)
- [system-architecture-overview.md](system-architecture-overview.md)
- [multi-region-strategy.md](multi-region-strategy.md)
- [../08-backend-services/caching-strategy.md](../08-backend-services/caching-strategy.md)
- [../25-infrastructure-cloud/README.md](../25-infrastructure-cloud/README.md)
- [../28-monitoring-observability/README.md](../28-monitoring-observability/README.md)
