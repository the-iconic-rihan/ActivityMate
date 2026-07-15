---
title: "Message Moderation Safety"
folder: 16-realtime-chat-notifications
purpose: "Chat moderation"
pages_estimate: 4
prerequisites: "chat-architecture"
dependencies: "moderation-content-ops"
priority: P0
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Message Moderation Safety

## Overview

Chat is where trust breaks down fastest: it is the one surface where a verified user can say something to another verified user that the profile, activity listing, and rating system never surface. This document defines what ActivityMate automatically detects inside chat messages, what it blocks versus flags, and how flagged content reaches a human reviewer.

## Context / Problem

ActivityMate is explicitly not a dating platform (PRD-001, Non-Goals), and its trust model depends on activities staying activity-first. Two failure modes are common on any chat-enabled meetup platform and both are foreseeable here: (1) harassment — sexual advances, hostility, or coercion directed at a guest, disproportionately experienced by women, which directly threatens the Female Retention success metric in PRD-001 Section 10; and (2) premature off-platform migration — a user pushing a phone number or Instagram handle within the first message so the conversation (and any future harassment) moves somewhere ActivityMate cannot see or act on, often before the other party's verification level is even known. A chat layer with no moderation gate is, per the parent README, a launch blocker rather than a fast-follow.

## The Actual Policy

**What is scanned:** every message sent through chat is scanned server-side (or via the managed provider's webhook, per chat-architecture.md) before or immediately upon delivery, using a layered approach:

1. **Deterministic pattern matching** — regex and keyword rules for phone numbers, email addresses, and common social-handle patterns (`@instagram`, `wa.me` links, etc.), plus a maintained blocklist of slurs, sexual solicitation phrases, and known scam phrasing.
2. **ML-based classification** — a lightweight text classifier (hosted alongside the trust engine described in trust-safety-fraud) scoring each message for harassment, sexual content, hostility, and spam/scam intent, run asynchronously so it never blocks message delivery latency.
3. **Contextual rules tied to trust state** — contact-info sharing is treated differently depending on verification level and activity state: it is soft-warned (a client-side nudge: "keep it on ActivityMate until you've met") pre-activity-confirmation for any user, and flagged with higher severity if either party is below Phone+Selfie verification (see 13-trust-safety-fraud/user-verification-levels.md), because that is exactly the profile of a scam or catfishing attempt.

**Action tiers:**
- **Block-and-warn (real-time):** explicit sexual solicitation, slurs, and known scam scripts are blocked from sending entirely; the sender sees an in-app warning citing the relevant community guideline (content-moderation-policy.md).
- **Allow-and-flag (async):** borderline harassment signals, first-instance contact-info sharing, and hostility below the block threshold are delivered normally but queued into the moderation-content-ops review pipeline with the message, sender, and recipient context attached.
- **Escalate (immediate):** messages scoring above the threat/coercion threshold, or a user-initiated in-chat report, bypass the standard queue and route to the sub-hour SLA tier defined in ../21-moderation-content-ops/human-moderation-workflow.md.

**User controls:** every chat thread has an always-visible Report and Block action. A report on a message auto-attaches the preceding 10 messages of context to the moderation queue entry — reviewers never see a single message in isolation.

## Enforcement

Automated flags do not themselves suspend an account — only a human reviewer (or, for block-and-warn, the deterministic rule itself preventing send) takes action, keeping false-positive risk contained per the human-moderation-workflow SLA tiers. Repeated block-and-warn triggers from the same account raise that user's internal risk score inside the trust engine (13-trust-safety-fraud/trust-engine-architecture.md), independent of any single human review outcome, so a pattern of near-miss behavior is visible even when no single message crosses the escalation threshold. All moderation actions taken on chat content are logged to the audit trail specified in ../24-admin-panel/audit-log-spec.md for appeals and legal-hold purposes.

## Related Documents

- [chat-architecture.md](chat-architecture.md) — the transport layer this moderation hooks into
- [chat-data-retention-privacy.md](chat-data-retention-privacy.md) — how long flagged and unflagged messages are retained
- [../21-moderation-content-ops/human-moderation-workflow.md](../21-moderation-content-ops/human-moderation-workflow.md) — the review queue and SLA tiers referenced above
- [../21-moderation-content-ops/content-moderation-policy.md](../21-moderation-content-ops/content-moderation-policy.md) — the guidelines cited in in-app warnings
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) — how flags feed trust score
- [../13-trust-safety-fraud/user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md) — verification tiers referenced in contextual rules
