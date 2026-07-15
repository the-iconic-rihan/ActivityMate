---
title: "Database Selection Rationale"
folder: 10-database
purpose: "Polyglot persistence rationale"
pages_estimate: 7
prerequisites: "service-boundaries-domain-map"
dependencies: "none"
priority: P0
audience: "BE, DO, CTO"
status: draft-v1
generated: 2026-07-15
---

# Database Selection Rationale

## Overview

ActivityMate runs on a deliberately polyglot persistence layer: PostgreSQL with the PostGIS extension as the system of record, MongoDB for schema-flexible document data, Redis for cache/session/rate-limit state, and MinIO for object storage. This document explains why a single-database approach was rejected, what each store is responsible for, and the operational tradeoffs the team is accepting in exchange for correctness where it matters most — the trust graph.

This decision is downstream of [service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md) and upstream of every other document in this folder; the entity model in [schema-design-erd.md](schema-design-erd.md) assumes this hybrid as given.

## Context / Problem

ActivityMate is not a generic marketplace CRUD app. Its core product claim is that the platform can be trusted to introduce strangers to each other for real-world, often nighttime, activities. That claim is only as strong as the data model behind it: who verified whom, who actually showed up, who was reported, and how a trust score was computed. If any of that data can silently corrupt — a duplicated report row, an orphaned verification record, a trust-score update that partially applies — the safety story collapses along with it.

At the same time, the product is pre-PMF. Activity metadata (what fields describe a "trekking" activity vs. a "board games" night), moderation snapshots, and various evolving payloads change shape weekly during the first two years. Forcing every one of those into a rigid relational schema with a migration for each iteration would slow the team to a crawl exactly when speed matters most.

A single database cannot satisfy both constraints well. PostgreSQL alone forces schema rigidity onto genuinely volatile data. MongoDB alone gives up the relational integrity, foreign keys, and transactional guarantees the trust graph needs. The team evaluated four options.

## Options Considered

### Option A — PostgreSQL only (single relational store)

**Pros:** Single operational surface, one backup/restore story, strong ACID guarantees everywhere, simplest mental model for a small team, native PostGIS support for geo.

**Cons:** JSONB columns can approximate document flexibility but degrade query ergonomics and indexing behavior as documents diverge in shape; every change to activity metadata or moderation payload structure becomes a migration, which is a heavier process than the iteration speed pre-PMF product work requires; no first-class caching layer, forcing ad hoc caching patterns on top of Postgres itself.

### Option B — MongoDB only (single document store)

**Pros:** Schema flexibility everywhere, fast iteration on any entity shape, horizontal scaling story is simpler on paper.

**Cons:** No native multi-document ACID transactions across the trust graph in the way Postgres offers by default (Mongo's transaction support exists but is heavier and less idiomatic to the document model); no mature geospatial proximity engine comparable to PostGIS for the density of queries a "find activities near me" feature needs; weaker foreign-key enforcement means integrity bugs in verification/report/trust-score data are easier to introduce silently — unacceptable for a trust-critical dataset.

### Option C — PostgreSQL + MongoDB + Redis + MinIO (adopted)

**Pros:** Each store does the one thing it is best at — Postgres for integrity-critical relational and geospatial data, Mongo for volatile document data, Redis for sub-millisecond cache/session/rate-limit reads, MinIO for large binary media without bloating the primary database. Failure domains are somewhat isolated: a Mongo outage degrades activity-metadata richness but does not corrupt the trust graph.

**Cons:** Four operational surfaces instead of one — four backup strategies, four sets of credentials, four things that can page on-call. Cross-store consistency (e.g., an activity's core row in Postgres and its flexible metadata in Mongo) must be managed at the application layer since there is no cross-database transaction. Onboarding new engineers takes longer; the team must document store boundaries clearly (this document, plus [schema-design-erd.md](schema-design-erd.md)) or engineers will guess wrong about where a new field belongs.

### Option D — Managed multi-model cloud database (e.g., a single cloud-vendor database supporting relational + document + cache)

**Pros:** Single vendor relationship, potentially lower ops overhead, unified billing.

**Cons:** Vendor lock-in at a very early stage before the team has negotiating leverage or certainty about the winning cloud provider; multi-model databases tend to be mediocre at every model rather than excellent at any one, and PostGIS in particular has no real substitute for the geospatial query patterns this product depends on; self-hosted MinIO becomes harder to justify if everything else is already vendor-managed, removing the media-storage cost control the team wants pre-revenue. Rejected for both strategic and cost reasons — see also [tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md).

## Recommendation & Rationale

Option C is adopted, matching PRD-001's technical stack section. The store boundaries are:

- **PostgreSQL + PostGIS** — system of record for `users`, `verification_records`, `trust_scores`, `activities` (core fields), `communities`, `memberships`, `reports`, and all foreign-key relationships between them. PostGIS handles proximity queries ("activities within 5km"), venue geocoding, and location-fuzzing logic support (see [geospatial-data-strategy.md](geospatial-data-strategy.md)). Postgres is chosen here specifically because trust-graph writes must be transactional: a trust-score recalculation triggered by a completed activity and a new rating must apply atomically or not at all.
- **MongoDB** — flexible document data that changes shape faster than a migration cycle can track: per-category activity metadata (a "trekking" activity has a difficulty field and a meeting-point note; a "board games" activity has a game list), moderation-flagged content snapshots (a point-in-time capture of a chat thread or profile at the moment it was reported, retained for review), and any experimental feature payloads before they graduate into a stable Postgres schema.
- **Redis** — session tokens and refresh-token denylists (see [session-token-management.md](../12-auth/session-token-management.md)), OTP rate-limit counters, hot-path caching for activity feeds and trust-score reads, and short-lived idempotency keys for API writes.
- **MinIO** — self-hosted S3-compatible storage for profile photos, selfie-liveness capture artifacts, activity photos, and moderation evidence attachments. Chosen over a managed cloud object store pre-PMF specifically to control cost and avoid a second vendor relationship while the team is small; see [storage-cdn-strategy.md](../18-media-storage/storage-cdn-strategy.md) for the migration path to a CDN-backed managed store post-PMF.

The fixed operational cost — four systems to run, patch, back up, and monitor — is accepted because it keeps relational integrity guarantees exactly where trust depends on them (Postgres) while keeping iteration speed everywhere else (Mongo). The alternative, forcing everything into one store, either slows the team down when it can least afford it (Postgres-only) or introduces integrity risk into the one dataset that cannot tolerate it (Mongo-only).

Cross-store consistency risk is mitigated by a rule enforced in code review and documented in [data-modeling-standards.md](data-modeling-standards.md): any entity with a safety or trust implication must have its authoritative row in Postgres, with Mongo used only for auxiliary, non-authoritative fields referenced by that row's primary key. Mongo is never the sole source of truth for anything the trust engine reads.

## Related Documents

- [schema-design-erd.md](schema-design-erd.md) — the concrete entity model built on this hybrid
- [data-modeling-standards.md](data-modeling-standards.md) — conventions for deciding which store a new field belongs in
- [geospatial-data-strategy.md](geospatial-data-strategy.md) — PostGIS query patterns in detail
- [../07-architecture/service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md) — prerequisite service boundary decisions
- [../07-architecture/tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md) — broader stack rationale
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) — how the trust engine reads/writes these stores
- [../18-media-storage/storage-cdn-strategy.md](../18-media-storage/storage-cdn-strategy.md) — MinIO's role in the media pipeline
