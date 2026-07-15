---
title: "Pricing Strategy"
folder: 01-business-strategy
purpose: "Price points"
pages_estimate: 4
prerequisites: "revenue-model-monetization-strategy"
dependencies: "market-research"
priority: P1
audience: "F, PM, G"
status: draft-v1
generated: 2026-07-15
---

# Pricing Strategy

## Overview

This document applies India price-sensitivity realities to the two monetizable levers defined in [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md): subscription tier pricing (Stage 2) and freemium activity-creation caps (Stage 1). It does not introduce a new monetization decision — pricing operates strictly within the staged path already adopted.

## Context / Problem

Indian consumer app pricing has a well-documented pattern: willingness to pay for subscriptions is real but low relative to US/EU benchmarks, and price sensitivity is sharply segmented by city tier and income bracket. ActivityMate's launch persona (relocated urban professionals in the Mumbai wedge, per [go-to-market-strategy.md](go-to-market-strategy.md)) sits at the more price-tolerant end of the Indian consumer spectrum, but the product category — new, unproven, non-essential — means pricing must be conservative until trust in the platform itself is established.

## Freemium Activity Caps (Stage 1)

The free tier's activity-creation and join limits double as both a spam/safety control (already required regardless of monetization, per the Trust Model in [PRD-001](../PRD-001_Master_Product_Requirements.md) Section 9) and a soft monetization lever for Stage 2. Recommended structure:

- **Unverified users:** no activity creation (join-only), consistent with trust-before-matching.
- **Phone+email verified users:** limited activity creation per week (e.g., low single digits), enough for genuine casual use, not enough for power hosting.
- **Selfie-verified, established trust score:** creation caps lift meaningfully — this ties cap relief to *earned* trust score rather than payment, preserving the Option 2 rejection rationale from [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md) (no purchased visibility/privilege).
- **Subscribers (Stage 2):** caps lift further and independent of trust-score-gated tiers, layered on top rather than replacing the trust-based gates.

This structure ensures the monetized cap increase is additive to trust-earned privileges, never a substitute for them — a user cannot pay to skip verification-based limits, only to extend beyond what trust alone would unlock.

## Subscription Tier Pricing (Stage 2)

Given India price-sensitivity patterns for comparable discretionary lifestyle subscriptions (regional OTT and fitness-app pricing bands, not US dating-app pricing bands which run substantially higher), the directional target is a **modest monthly price point positioned closer to a regional streaming subscription than to international dating-app subscription pricing**, with an annual plan offered at a meaningful discount to reward committed, high-trust users and improve the payback period modeled in [unit-economics-cac-ltv.md](unit-economics-cac-ltv.md).

Two tiers are recommended rather than one:

| Tier | Target user | Value |
|---|---|---|
| Plus | Regular participant, occasional host | Higher activity caps, expanded filters, priority support |
| Host Pro | Frequent/community host | Everything in Plus, plus community management tools (recurring activities, participant management) — bridges toward [../15-activities-communities/](../15-activities-communities/README.md) community features |

A single flat tier was considered and rejected: frequent hosts and casual participants have different willingness-to-pay curves, and collapsing them into one price point would either underprice hosts (leaving value on the table) or overprice casual participants (killing Stage 2 conversion entirely).

## Commission Pricing (Stage 3, Directional Only)

Not priced in detail in this document since Stage 3 activates later per [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md); directionally, a percentage-of-transaction-value commission (rather than a flat fee) is preferred because it scales fairly across the wide price range of paid activities (a ₹300 workshop versus a ₹5,000 weekend trip), avoiding a flat fee that would be prohibitive for low-value activities and negligible for high-value ones.

## Risks & Open Questions

- No India-specific willingness-to-pay data exists yet for this exact category (trust-scored activity-partner subscriptions); current price banding is inferred from adjacent categories, not measured — a priority for [../02-market-research/survey-interview-findings.md](../02-market-research/survey-interview-findings.md) follow-up work.
- Risk: pricing too low undermines the premium-trust positioning; pricing too high, given India price sensitivity, kills Stage 2 conversion before it starts. This tension is unresolved and should be tested via a price-sensitivity survey before Stage 2 activates, not set by founder intuition alone.
- Open question: should pricing vary by city once expansion begins (per [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md)), given cost-of-living and price-sensitivity differences across Tier-1 Indian cities — deferred until multi-city data exists.

## Related Documents

- [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md)
- [unit-economics-cac-ltv.md](unit-economics-cac-ltv.md)
- [../02-market-research/tam-sam-som.md](../02-market-research/tam-sam-som.md)
