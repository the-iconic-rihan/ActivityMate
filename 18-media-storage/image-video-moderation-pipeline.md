---
title: "Image Video Moderation Pipeline"
folder: 18-media-storage
purpose: "Media moderation"
pages_estimate: 6
prerequisites: "media-upload-pipeline"
dependencies: "moderation-content-ops"
priority: P0
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Image & Video Moderation Pipeline

## Overview

This is the concrete pipeline every photo and video passes through between landing in MinIO's quarantine bucket (media-upload-pipeline.md, Stage 3) and being promoted to public visibility. It covers two distinct threats: unsafe content (NSFW, violence, graphic material) and fake profiles built on stolen photos — a threat category unique to a platform where profile photos are load-bearing trust signals.

## Context / Problem

Media is named in the parent README as a primary vector for both fake profiles and unsafe content, and the two require genuinely different detection techniques. NSFW/violence detection is a solved-enough classification problem with mature off-the-shelf and open-source models. Stolen-photo detection is different: a well-produced fake profile on ActivityMate is not going to upload an obviously NSFW image — the risk is a plausible, attractive photo lifted from someone else's Instagram or a stock-photo site, used to build a fabricated identity that then earns trust score and gets close to real users in person. Catching that requires reverse-image search against known source databases, not content classification. Because ActivityMate's entire premise depends on a photo meaning "this is a real, specific person," this pipeline is P0 — it ships with the upload pipeline, not after it.

## Pipeline (Ordered Stages)

**Stage 1 — Ingestion trigger.** A moderation job is enqueued for every object landing in the quarantine bucket, keyed to the object's media record and the entity it's attached to (profile photo, activity cover, selfie verification image) — the pipeline treats these entity types with different severity, described below.

**Stage 2 — Automated NSFW/violence classification.** The object is run through an image/video content classifier (a hosted or self-run model tuned for nudity, sexual content, graphic violence, and weapons) returning a confidence score per category. Video is sampled at intervals (not every frame, to keep pipeline latency reasonable) plus the first and last frame, which are disproportionately likely to be representative or deliberately misleading thumbnails.

**Stage 3 — Reverse-image-search / stolen-photo check.** In parallel, the image (for profile photos and activity covers specifically — this stage is skipped for verification selfies, handled separately in Stage 5) is checked against a reverse-image-search capability to detect whether it appears elsewhere on the public web under a different name or context — a strong signal of a stolen or stock photo rather than an original upload. A perceptual-hash comparison against ActivityMate's own historical media library also runs here, catching a rejected or banned user re-uploading the same stolen photo under a new account.

**Stage 4 — Decision routing.** Based on Stage 2 and Stage 3 scores:
- **Auto-approve:** both scores clearly below threshold — promoted to public storage automatically (media-upload-pipeline.md, Stage 6), typically within seconds of upload.
- **Auto-reject:** NSFW/violence score clearly above threshold — object deleted from quarantine, media record marked `rejected`, uploader notified with the relevant content-moderation-policy.md guideline cited, and the rejection recorded as a signal into the user's trust score (13-trust-safety-fraud/trust-engine-architecture.md).
- **Manual review:** either score falls in the ambiguous middle band, or a stolen-photo match is found but with ambiguous confidence (e.g., a plausible coincidental match) — routed to the human moderation queue (21-moderation-content-ops/human-moderation-workflow.md) rather than auto-decided in either direction, since both false-approve and false-reject carry real cost here.
- **High-confidence stolen-photo match:** routed directly to the elevated-severity queue, since a confirmed stolen photo is a strong fake-profile signal warranting faster human attention than an ordinary flagged upload.

**Stage 5 — Verification selfie handling (distinct path).** Selfie and government-ID verification images (13-trust-safety-fraud/user-verification-levels.md) skip Stage 3's public-web reverse-image search (irrelevant for this use case) and instead run liveness/face-match checks against the profile photo already on file, confirming the verification selfie is of the same person as the profile — this is trust-engine functionality this pipeline hands off to, not duplicates.

**Stage 6 — Human review outcome feedback.** Every manual-review decision (approve or reject) is logged with the moderator's reasoning and fed back as a labeled training/calibration example, so classifier thresholds can be periodically retuned rather than left static as false-positive/negative patterns emerge over time.

## Enforcement

No media reaches public storage without passing through Stage 4's decision routing — this is structurally enforced by media-upload-pipeline.md's bucket-policy restriction that only the promotion job (which requires an `approved` status) can write to the public bucket. SLA for the manual-review queue on media specifically follows the severity tiers defined in human-moderation-workflow.md, with profile-photo and verification-image reviews prioritized above activity-cover reviews given their closer proximity to identity trust.

## Related Documents

- [media-upload-pipeline.md](media-upload-pipeline.md) — upstream pipeline this moderation gate is embedded in
- [storage-cdn-strategy.md](storage-cdn-strategy.md) — bucket structure this pipeline promotes into
- [../21-moderation-content-ops/human-moderation-workflow.md](../21-moderation-content-ops/human-moderation-workflow.md) — manual review queue and SLA tiers
- [../21-moderation-content-ops/content-moderation-policy.md](../21-moderation-content-ops/content-moderation-policy.md) — guidelines cited on auto-reject
- [../13-trust-safety-fraud/user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md) — Stage 5 verification handoff
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) — how rejections and stolen-photo matches feed trust score
