---
title: "Storage Cdn Strategy"
folder: 18-media-storage
purpose: "Storage & CDN"
pages_estimate: 3
prerequisites: "media-upload-pipeline"
dependencies: "infrastructure-cloud"
priority: P1
audience: "BE, DO"
status: draft-v1
generated: 2026-07-15
---

# Storage & CDN Strategy

## Overview

MinIO is ActivityMate's already-adopted object store (PRD-001 Section 12). This document is not a build-vs-buy evaluation — MinIO is settled — it documents how MinIO is deployed and operated, how a CDN is layered in front of it for India edge latency, and the concrete scale threshold at which self-hosting stops being the right call.

## Context / Problem

MinIO gives ActivityMate a self-hosted, S3-API-compatible object store with no per-GB vendor markup and full control over where data physically lives — relevant both for cost discipline pre-revenue and for the India-data-residency posture the company has taken elsewhere (chat, DPDP Act compliance). The tradeoff is operational: MinIO does not manage itself. Someone has to run it reliably, back it up, monitor its disks, and — the part self-hosting genuinely gives up versus a managed object store — serve it fast to a mobile user in Mumbai without every media request round-tripping to wherever the MinIO cluster physically sits.

## The Actual Policy

**Deployment:** MinIO runs as a containerized service within the Docker Compose / infrastructure-cloud environment already hosting the modular monolith, configured in distributed mode (multiple MinIO nodes/drives) even at modest scale, since MinIO's erasure-coding-based redundancy requires a minimum node count to provide real durability — a single-node MinIO instance is a durability liability, not just a scaling one.

**Bucket structure:** at minimum, a `quarantine` bucket (private, moderation staging, per media-upload-pipeline.md), a `public-media` bucket (approved, CDN-fronted content — profile photos, activity covers), and a `verification` bucket (selfie/ID verification images, private, never CDN-fronted, access restricted to the trust engine and Trust & Safety Ops only, per 13-trust-safety-fraud access rules).

**CDN layering:** the `public-media` bucket is fronted by a CDN with points of presence covering India, so a user in Mumbai loading another user's profile photo or an activity's cover image is served from a nearby edge cache rather than round-tripping to the MinIO origin on every request. Cache headers on promoted objects are set for long-lived, immutable caching (object keys are content-addressed/versioned at promotion time, so a cache invalidation is never needed — a changed photo gets a new key, not an overwritten one). The `quarantine` and `verification` buckets are never CDN-fronted; they are reachable only via short-lived, backend-issued pre-signed URLs.

**Backup and durability:** MinIO's erasure coding provides resilience against individual drive failure, but this is not a substitute for backup — a separate scheduled replication job mirrors the `public-media` and `verification` buckets to a physically or logically separate storage target on a defined interval, consistent with the company-wide backup-retention-policy.

**Operational cost tracking:** self-hosting MinIO costs ActivityMate compute, storage, and DevOps attention rather than a per-GB vendor bill — that cost is real but often under-tracked because it does not show up as a single line-item invoice. DevOps tracks an internal estimated cost-per-GB-served figure (compute + storage + bandwidth + amortized ops time) quarterly.

**Migration threshold to a managed provider (e.g., S3 or a managed S3-compatible service):** self-hosting is reassessed once either (a) the internal cost-per-GB-served estimate exceeds a comparable managed-provider quote at then-current usage, or (b) media volume/traffic grows to a point where MinIO cluster operations meaningfully compete with DevOps bandwidth needed elsewhere. Because the pipeline already speaks the S3 API end-to-end (pre-signed URLs, bucket semantics), this migration is a re-pointing exercise, not a pipeline rewrite.

## Enforcement

Bucket access policies (IAM-equivalent MinIO policies) are reviewed alongside every change to media-upload-pipeline.md or image-video-moderation-pipeline.md, since those two documents define which service roles are allowed to write to which bucket. DevOps owns MinIO cluster health monitoring (28-monitoring-observability) with alerting on disk capacity and node health, not just application-level errors.

## Related Documents

- [media-upload-pipeline.md](media-upload-pipeline.md) — bucket flow (quarantine → public-media) this strategy supports
- [image-video-moderation-pipeline.md](image-video-moderation-pipeline.md) — moderation gate between quarantine and promotion
- [../25-infrastructure-cloud/cost-optimization-playbook.md](../25-infrastructure-cloud/cost-optimization-playbook.md) — cost-tracking framework referenced above
- [../10-database/backup-retention-policy.md](../10-database/backup-retention-policy.md) — company-wide backup policy this bucket replication follows
- [../13-trust-safety-fraud/user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md) — access rules for the verification bucket
