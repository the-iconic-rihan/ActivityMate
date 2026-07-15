---
title: "Revenue Model Monetization Strategy"
folder: 01-business-strategy
purpose: "Monetization decision"
pages_estimate: 8
prerequisites: "business-model-canvas"
dependencies: "payments-subscriptions"
priority: P0
audience: "F, I, PM"
status: draft-v1
generated: 2026-07-15
---

# Revenue Model Monetization Strategy

## Overview

ActivityMate's trust-first positioning ([mission-vision-values.md](../00-foundation/mission-vision-values.md)) is a UX and safety commitment, not a revenue model — the two must be reconciled explicitly, because monetization choices change downstream architecture priorities. Commission-based revenue requires payments infrastructure sooner than a pure subscription model would; a freemium model requires usage metering earlier than either.

## Context / Problem

The product has no revenue at launch by design — the [PRD-001](../PRD-001_Master_Product_Requirements.md) MVP feature list (auth, profile, verification, create/join activity, chat, ratings, reports, notifications, communities, trust score, admin dashboard) contains no payments surface. The company must decide, before backend architecture locks in, which monetization path to build toward, because it determines whether [../19-payments-subscriptions/](../19-payments-subscriptions/README.md) is a pre-launch or post-PMF dependency.

## Options Considered

**Option 1 — Subscription (flat monthly/annual fee for full access).**
- *Pros:* predictable, forecastable revenue; aligns with investor-familiar SaaS-style metrics for [unit-economics-cac-ltv.md](unit-economics-cac-ltv.md); no per-transaction complexity.
- *Cons:* adds payment friction at the exact moment the product has zero trust to lean on — a brand-new user asked to pay before experiencing a single safe activity has no reason to convert. At a zero-trust stage, a paywall in front of the core loop directly undermines the trust-building objective the whole product exists to serve.

**Option 2 — Freemium + boosts (Bumble/Tinder-style visibility purchases).**
- *Pros:* proven model with well-understood conversion mechanics; monetizes power users (frequent hosts, high-activity participants) without blocking casual users; low friction at signup.
- *Cons:* "boosts" (paying to be more visible in activity discovery) risk a visibility arms race that rewards spending over trust score — directly contradicts Core Product Principle 2 (Trust Before Matching) in PRD-001. A paid-visibility mechanic that lets a lower-trust user out-rank a higher-trust user in discovery is an architectural contradiction of the entire trust engine, not just a monetization nuance.

**Option 3 — Commission-per-activity (percentage or flat fee on paid/ticketed activities).**
- *Pros:* revenue aligns directly with real usage — the company only earns when a real-world activity actually happens, which is consistent with the [north-star-metric.md](../00-foundation/north-star-metric.md) definition of success (completed, trusted meetups). Naturally scales with GMV as activity volume grows.
- *Cons:* most MVP activity categories (coffee, casual runs, board games — see PRD-001 Section 8) are free or trivially cheap to begin with, so there is little transaction value to take commission on early. Pricing a commission cleanly (flat fee vs. percentage, who pays — host or participant) is unresolved and requires payments infrastructure that does not yet exist.

**Option 4 — Advertising.**
- *Pros:* zero user-facing friction, well-understood ad-tech tooling.
- *Cons:* rejected outright. Advertising monetizes attention and engagement time, which creates incentive pressure toward feed-like, engagement-maximizing UX patterns that directly conflict with Core Product Principle 1 (Activity First) and the trust positioning generally — a user cannot fully trust a platform that is simultaneously trying to sell their attention to third parties. This option is not carried forward into unit economics modeling at all.

## Recommendation & Rationale

Adopt a **staged path**:

**Stage 1 — Freemium at Mumbai launch.** Core usage (browse activities, create a limited number per month, join activities, chat, rate) is free. This removes all friction during the highest-risk trust-building phase, when the product has no track record to justify asking for money. Usage caps (e.g., activity-creation limits for non-verified or low-trust-score users) exist for safety and spam-control reasons already, per the Trust Model in PRD-001 Section 9 — reusing this cap mechanism as a soft monetization lever later avoids building a second metering system.

**Stage 2 — Subscription, introduced once retention data justifies it.** Once the [north-star-metric.md](../00-foundation/north-star-metric.md) shows a stable cohort of repeat, high-trust-score users, introduce a subscription tier (e.g., higher activity-creation caps, priority host visibility *gated by trust score, not purchase*, advanced filters) targeted at the power-user segment who has already proven retention. This sequencing avoids Option 1's core flaw — by Stage 2 the product has trust equity to spend.

**Stage 3 — Commission, reserved for paid/ticketed activities only.** As the activity catalog matures beyond free casual categories into higher-value experiences (weekend trips, curated experiences, paid workshops), commission activates on the transaction value of those specific activities, leaving the free casual core (coffee, running, board games) commission-free indefinitely. This preserves low-friction usage for the bulk of activity volume while capturing revenue where real money is already changing hands.

Boosts (Option 2) are explicitly **not** adopted as designed above, but a constrained variant may be revisited post-PMF: visibility improvements that are *earned* (e.g., a "recently highly rated" badge) rather than *purchased* preserve the trust-before-matching principle while still creating a status-driven engagement incentive.

## Financial Shape (Directional)

| Stage | Primary revenue lever | Trigger to advance | Payments dependency |
|---|---|---|---|
| 1. Freemium | none (or negligible ad-hoc) | Launch | none |
| 2. Subscription | recurring tier fee | Retention cohort stability, ~500+ weekly active hosts in Mumbai wedge | [../19-payments-subscriptions/](../19-payments-subscriptions/README.md) MVP |
| 3. Commission | % or flat fee on paid activities | Paid/ticketed activity catalog reaches meaningful volume | Full payments + payout infra, escrow handling |

## Risks & Open Questions

- Freemium caps that are too generous fail to create Stage 2 conversion pressure; too strict caps damage the exact new-user trust-building experience Stage 1 protects. The specific numeric caps are an open product question owned by [../04-product/](../04-product/README.md), not resolved here.
- Commission structure (host-pays vs. participant-pays vs. split) is unresolved and has legal implications (see [../31-privacy-compliance-legal/](../31-privacy-compliance-legal/README.md)) around payment intermediary status in India.
- Risk: subscription tier features must be chosen carefully so that none of them constitute a disguised trust-score purchase (see Option 2 rejection rationale) — this is a standing constraint on all future pricing feature design, not just a launch-time decision.
- Open question: should verified-but-unpaid users ever see reduced discovery visibility versus paying users — current position is no, since that would reintroduce the Option 2 problem through the back door; to be revisited only with explicit trust-safety sign-off.

## Related Documents

- [business-model-canvas.md](business-model-canvas.md)
- [pricing-strategy.md](pricing-strategy.md)
- [unit-economics-cac-ltv.md](unit-economics-cac-ltv.md)
- [../19-payments-subscriptions/](../19-payments-subscriptions/README.md)
- [../00-foundation/north-star-metric.md](../00-foundation/north-star-metric.md)
