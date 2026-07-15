---
title: "Referral Program Design"
folder: 32-growth-marketing
purpose: "Referral mechanics"
pages_estimate: 3
prerequisites: "growth-strategy-loops"
dependencies: "trust-safety-fraud"
priority: P1
audience: "G"
status: draft-v1
generated: 2026-07-15
---

# Referral Program Design

## Overview

A conventional referral program rewards invite volume: send a code, both parties get a discount or credit, repeat. That mechanic is wrong for ActivityMate, because raw invite volume is exactly what a trust-first platform cannot absorb — it produces exactly the low-context signups that Options B and C in [growth-strategy-loops.md](growth-strategy-loops.md) were rejected for. This document specifies referral mechanics that reward bringing *trusted, activity-completing* connections in, not invite counts.

## Problem

If we reward a user for every signup their referral code produces, we create an incentive to spam the code into WhatsApp broadcast lists and Instagram bios — high volume, near-zero trust context, and a spike in exactly the fake-profile and no-show risk the [PRD-001 risk register](../PRD-001_Master_Product_Requirements.md) calls out. The reward has to be anchored to an outcome that only happens if the referred person is a real, engaged, safety-compliant participant.

## Mechanics

### Reward trigger: completed activity, not signup

A referral is only credited once the referred user (a) completes phone and selfie verification (see [../13-trust-safety-fraud/user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md)) and (b) attends and is rated for their first activity with no report filed against them in that window. Signup alone earns nothing. This means the referrer has a direct incentive to refer someone they believe will actually show up and behave well — their own reward is contingent on the referred person's early trust signal, which mirrors real-world social vouching.

### Reward structure

- **Referrer reward:** A trust-score micro-boost (small, capped, decaying — not a permanent multiplier, to prevent gaming) plus a discretionary in-app credit usable toward future paid features (see [../19-payments-subscriptions/subscription-tier-design.md](../19-payments-subscriptions/subscription-tier-design.md)) once that tier exists. The trust-score boost is intentionally the more emphasized reward, because it reinforces the platform's actual currency — reputation — rather than training users to optimize for cash-equivalent rewards.
- **Referred-user reward:** A one-time "fast track" — their first activity join request skips the standard new-account cooldown window, since they arrive with an implicit vouch from an existing trusted member. This is a genuinely useful reward (removes real friction) rather than a discount, and it costs the platform nothing.
- **Caps:** A hard cap of 5 credited referrals per user per rolling 90 days. This is a deliberate anti-abuse ceiling — it keeps the mechanic aligned with "bring your real circle," which for almost every genuine user tops out well under 5 people at a time, while making bulk referral farming economically pointless.

### Anti-gaming controls

- Referral credit is void, and both accounts are flagged for manual trust-safety review, if the referred account is later banned, receives 2+ reports, or the pair shows collusive rating patterns (e.g., mutual 5-star ratings with no other platform activity) — cross-checked against [../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md).
- Referral links are personalized and rate-limited (not postable as a generic public code), which keeps the channel social-graph-shaped rather than broadcast-shaped.
- No referral reward stacking with paid-acquisition promo codes — the two growth channels are kept accounting-separate so the organic loop's quality metrics (per [growth-strategy-loops.md](growth-strategy-loops.md)) aren't contaminated by paid traffic routed through a referral wrapper.

## Recommendation & Rationale

Outcome-gated rewards, small and reputation-weighted rather than cash-weighted, with a hard per-user cap: this keeps the referral mechanic as an amplifier of the organizer-led organic loop rather than a parallel growth channel with its own incentive to prioritize volume over quality. It is deliberately less "viral" in the growth-hacking sense than a typical two-sided cash referral program — that tradeoff is intentional and consistent with "safety over growth" (PRD-001 §6).

## Related Documents

- [growth-strategy-loops.md](growth-strategy-loops.md) — the primary loop this mechanic feeds
- [../13-trust-safety-fraud/trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md) — how the trust-score boost is disclosed to users
- [../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md) — collusion and abuse detection
- [../15-activities-communities/cancellation-no-show-policy.md](../15-activities-communities/cancellation-no-show-policy.md) — no-show behavior that would void referral credit
