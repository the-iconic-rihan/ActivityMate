# 18-media-storage/

**Phase:** Phase 4 — Trust, Safety & Core Product  
**Priority:** P1 (P1 — blocks public launch)  
**Owner:** BE / Infra  
**Depends on:** database  
**Document count:** 4

## Purpose

Photo and video upload, storage, and moderation pipeline.

## Why this folder exists

Media is a primary vector for both fake profiles (stolen photos) and unsafe content. Storage engineering and moderation are paired here because they must ship together, not sequentially — an upload pipeline with no moderation gate is a launch blocker, not a fast-follow.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [media-upload-pipeline.md](media-upload-pipeline.md) | Upload flow | Client upload flow and the processing pipeline. | 4 | database-selection-rationale | none | P1 | BE |
| [storage-cdn-strategy.md](storage-cdn-strategy.md) | Storage & CDN | MinIO as the already-adopted self-hosted, S3-compatible object store, fronted by a CDN for India edge latency. Documents the operational cost of self-hosting MinIO against a defined scale threshold for migrating to a managed provider. | 3 | media-upload-pipeline | infrastructure-cloud | P1 | BE, DO |
| [image-video-moderation-pipeline.md](image-video-moderation-pipeline.md) | Media moderation | Automated NSFW/violence detection before publish, plus a reverse-image-search check against known stolen-photo sources to catch fake profiles. | 6 | media-upload-pipeline | moderation-content-ops | P0 | BE, SEC |
| [media-compression-standards.md](media-compression-standards.md) | Compression standards | Compression and format standards suited to the mobile data constraints common among the target user base in India. | 2 | media-upload-pipeline | none | P2 | BE, FE |
