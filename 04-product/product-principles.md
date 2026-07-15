---
title: "Product Principles"
folder: 04-product
purpose: "Design-decision filter"
pages_estimate: 2
prerequisites: "foundation"
dependencies: "none"
priority: P0
audience: "ALL"
status: draft-v1
generated: 2026-07-15
---

# Product Principles

## Overview

ActivityMate will spend the next two years being pulled in two directions: grow fast, or stay trusted. Every roadmap review, every PM's feature request, every "just ship it" moment under investor pressure will implicitly ask which one wins. These principles exist so that question is answered once, in writing, before the pressure arrives — not improvised in a Slack thread at 11pm before a launch.

These are not aspirational values. They are tiebreakers. When two reasonable options remain after normal debate, the team defaults to whichever one these principles point to.

## The Principles

**1. Activity First, Not Profile First.** The product surface leads with what a person is doing (Saturday morning badminton, a Bandra photography walk), not who they are. Feeds, notifications, and onboarding all optimize for "find the activity," and profile/matching mechanics stay secondary. If a feature makes ActivityMate feel more like browsing people than browsing plans, it does not ship as designed.

**2. Trust Before Matching.** No user is surfaced to another user, and no chat thread opens, before both parties clear the minimum verification bar (phone OTP, mandatory). Growth features that would relax this — e.g., "preview unverified profiles to boost early-city liquidity" — are rejected outright, even when Mumbai's early user density is thin and the temptation to loosen the gate is highest.

**3. Safety Over Growth.** When a safety metric (report rate, no-show rate, panic-button trigger rate) and a growth metric (DAU, activity-creation rate) trade off against each other, safety wins by default. This is the literal tiebreaker used in [experimentation-framework.md](experimentation-framework.md)'s guardrail-metric design: a safety regression auto-aborts an experiment regardless of the growth lift observed.

**4. Community Driven, Not Algorithm Driven.** Recommendation and matching logic (see [../14-matching-recommendation/](../14-matching-recommendation/README.md)) assists discovery; it does not replace host judgment. Hosts retain the final call on who joins their activity. The platform will not auto-add participants to activities to hit engagement targets.

**5. Local Network Density Over National Reach.** ActivityMate would rather be the obvious choice for 50,000 highly active Mumbai users than a thin layer across ten under-served cities. Expansion (see [../41-localization-expansion/](../41-localization-expansion/README.md)) is gated on density thresholds in the current city, not on calendar time.

## How These Are Used

Every PRD written from [prd-template.md](prd-template.md) carries a mandatory trust/safety review checkbox that references these five principles directly. Feature debates that reach a stalemate in planning are resolved by asking "which option does Principle 2 or 3 favor?" rather than by seniority or opinion. Product leadership (CPO) is the principle owner and adjudicates disputes; Founders hold override authority only for existential business decisions, and any override must be logged in [../39-decision-records-rfcs/decision-log.md](../39-decision-records-rfcs/decision-log.md).

## Risks & Open Questions

- Principle 3 will be tested hardest during the first 90 days post-launch, when DAU is small and every growth data point matters emotionally to the founding team. The org needs a pre-committed guardrail (see [experimentation-framework.md](experimentation-framework.md)) rather than a judgment call made in the moment.
- "Local network density over reach" assumes Mumbai has enough addressable population in the target 21–40 urban professional segment to reach liquidity. If early cohort data disagrees, Principle 5 may need a revision — tracked as an open question for [product-strategy-doc.md](product-strategy-doc.md).

## Related Documents

- [product-strategy-doc.md](product-strategy-doc.md)
- [feature-prioritization-framework.md](feature-prioritization-framework.md)
- [prd-template.md](prd-template.md)
- [../00-foundation/](../00-foundation/README.md)
- [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md)
