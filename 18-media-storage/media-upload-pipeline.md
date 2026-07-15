---
title: "Media Upload Pipeline"
folder: 18-media-storage
purpose: "Upload flow"
pages_estimate: 4
prerequisites: "database-selection-rationale"
dependencies: "none"
priority: P1
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Media Upload Pipeline

## Overview

This document defines the concrete, ordered pipeline a photo or video takes from a user's phone camera roll to being safely visible on ActivityMate — profile photos, selfie verification images, and activity cover photos. It is the mechanical counterpart to image-video-moderation-pipeline.md, which defines what happens at the moderation gate this pipeline routes through.

## Context / Problem

Media upload touches four systems that must hand off cleanly: the Flutter client, the FastAPI backend, MinIO object storage, and the moderation gate. Getting the sequencing wrong in either direction is costly — publishing before moderation clears creates exactly the launch-blocking risk the parent README calls out (an upload pipeline with no moderation gate), while blocking the UI until a full moderation pass completes makes upload feel broken on the mid-range Android devices and inconsistent mobile networks common among the target user base in Mumbai. The pipeline below is designed to feel instant to the user while never allowing unmoderated content to become publicly visible.

## Pipeline (Ordered Stages)

**Stage 1 — Client-side pre-processing.** Before any network call, the Flutter client: validates file type (JPEG/PNG for images, MP4/MOV for video) and enforces a maximum raw file size; strips EXIF GPS metadata client-side (a photo's embedded GPS coordinates are a location-privacy leak independent of the app's own location-fuzzing policy in 17-maps-location/location-privacy-strategy.md, so this strip happens unconditionally before upload, not as an optional step); generates a client-side compressed variant per media-compression-standards.md before the upload request is even made, to keep upload time reasonable on constrained mobile data.

**Stage 2 — Request a pre-signed upload URL.** The client calls a backend endpoint requesting a direct-to-MinIO pre-signed PUT URL, scoped to a single object key and short expiry. The backend does not proxy the raw bytes through itself — this keeps the FastAPI service stateless and avoids it becoming an upload bottleneck, and is the standard pattern for S3-compatible object stores like MinIO (storage-cdn-strategy.md).

**Stage 3 — Direct upload to MinIO, quarantine bucket.** The client uploads directly to MinIO using the pre-signed URL, landing in a `quarantine` bucket/prefix — never the public-serving bucket. Nothing in the quarantine bucket is reachable via the CDN or any public URL; it exists solely as a moderation staging area.

**Stage 4 — Upload-complete callback and metadata write.** On successful PUT, the client calls back to the backend confirming the object key. The backend writes a media record (owner, object key, upload timestamp, associated entity — profile/activity/verification — and status = `pending_moderation`) to PostgreSQL, and enqueues a moderation job (background-jobs-queue-architecture.md) referencing that object key.

**Stage 5 — Moderation gate.** The enqueued job runs the full pipeline defined in image-video-moderation-pipeline.md (NSFW/violence classification, reverse-image-search check). The media record's status transitions to `approved`, `rejected`, or `manual_review` based on that outcome — this document does not duplicate that logic, only the handoff into and out of it.

**Stage 6 — Promotion to public storage.** Only on `approved` status does a backend job copy (server-side, MinIO-to-MinIO, not through the client) the object from the quarantine bucket to the public-serving bucket that the CDN fronts, and updates the media record's public URL. The quarantine copy is deleted after a short grace period once promotion succeeds. Rejected media is deleted from quarantine outright and never promoted.

**Stage 7 — Client state.** While in `pending_moderation` or `manual_review`, the client shows the media with a locally-cached preview (from the client's own compressed copy, not yet the public CDN URL) plus a subtle "under review" state where relevant (e.g., a new profile photo) — the user is never blocked from continuing to use the app while moderation runs asynchronously in the background.

## Enforcement

No code path is permitted to write directly to the public-serving MinIO bucket except the Stage 6 promotion job — this is enforced via MinIO bucket policy/IAM scoping, not just convention, so a bug elsewhere in the codebase cannot accidentally publish unmoderated content.

## Related Documents

- [image-video-moderation-pipeline.md](image-video-moderation-pipeline.md) — the moderation gate this pipeline routes through
- [storage-cdn-strategy.md](storage-cdn-strategy.md) — MinIO bucket architecture and CDN fronting referenced above
- [media-compression-standards.md](media-compression-standards.md) — client-side compression applied in Stage 1
- [../10-database/database-selection-rationale.md](../10-database/database-selection-rationale.md) — prerequisite database context
- [../08-backend-services/background-jobs-queue-architecture.md](../08-backend-services/background-jobs-queue-architecture.md) — job queue used in Stages 4–6
- [../17-maps-location/location-privacy-strategy.md](../17-maps-location/location-privacy-strategy.md) — location-privacy rationale for EXIF stripping
