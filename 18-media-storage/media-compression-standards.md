---
title: "Media Compression Standards"
folder: 18-media-storage
purpose: "Compression standards"
pages_estimate: 2
prerequisites: "media-upload-pipeline"
dependencies: "none"
priority: P2
audience: "BE, FE"
status: draft-v1
generated: 2026-07-15
---

# Media Compression Standards

## Overview

This document sets the concrete compression, resolution, and format standards applied to photos and video at upload (media-upload-pipeline.md, Stage 1) and at serving time, sized for the mobile data realities of ActivityMate's target user base rather than assuming broadband-equivalent connectivity.

## Context / Problem

ActivityMate's primary persona — urban professionals, students, and solo travellers in Mumbai — is overwhelmingly mobile-first, and a meaningful share of that usage happens on metered mobile data plans, on mid-range Android devices, and in areas with inconsistent 4G/5G coverage (train commutes, indoor venues, monsoon-affected connectivity). An upload flow or feed that assumes fast, unmetered connectivity produces a materially worse experience for exactly the users the product needs most in its density-building phase — a slow-loading discovery feed full of uncompressed activity cover photos is a silent churn driver.

## The Actual Policy

**Image standards:** client-side compression targets a maximum file size band (well under the multi-megabyte raw output of a modern phone camera) at upload, using JPEG (or WebP where client support allows, given its meaningfully better compression ratio at equivalent visual quality) with a fixed maximum dimension for each use case — profile photos capped smaller than activity cover photos, since profile photos render at consistently small display sizes across the app (list rows, avatars, chat headers) and rarely need the resolution of a full-width cover image. Multiple resolution variants are generated at promotion time (thumbnail, feed-card, full-view) so the client always requests the smallest variant sufficient for the UI context it's rendering into, rather than downloading a full-resolution image to show a 48px avatar.

**Video standards:** activity-related video (where supported) is transcoded to H.264/MP4 at a bounded resolution and bitrate ceiling suited to feed/preview playback rather than archival quality, with a hard duration cap enforced at upload to keep both storage cost and mobile playback data usage bounded.

**Format fallback:** WebP/modern formats are offered where the client can render them; JPEG remains the universal fallback, since not every device and not every network-constrained rendering path in the Flutter app can be assumed to handle newer formats without added complexity ActivityMate does not need to take on at MVP.

**Adaptive serving:** the CDN layer (storage-cdn-strategy.md) serves the pre-generated resolution variant matching the requesting context (feed thumbnail vs. full-screen view) rather than serving one canonical file and relying on client-side downscaling, which wastes mobile data on every feed scroll.

## Enforcement

Compression parameters live in shared client/backend config (not hardcoded independently on each side), since a mismatch between what the client compresses to and what the backend expects to generate variants from produces visible quality inconsistency. FE and BE jointly review these thresholds when device/network telemetry (22-analytics-experimentation) indicates a meaningful shift in the user base's typical connectivity.

## Related Documents

- [media-upload-pipeline.md](media-upload-pipeline.md) — Stage 1 client-side compression this document specifies parameters for
- [storage-cdn-strategy.md](storage-cdn-strategy.md) — variant-serving layer referenced above
- [../09-frontend-mobile/](../09-frontend-mobile/README.md) — Flutter client constraints this standard is designed around
