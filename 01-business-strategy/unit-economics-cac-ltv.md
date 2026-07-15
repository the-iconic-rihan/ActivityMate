---
title: "Unit Economics Cac Ltv"
folder: 01-business-strategy
purpose: "CAC / LTV model"
pages_estimate: 5
prerequisites: "revenue-model-monetization-strategy"
dependencies: "analytics-experimentation"
priority: P0
audience: "F, I"
status: draft-v1
generated: 2026-07-15
---

# Unit Economics: CAC / LTV

## Overview

This document projects Customer Acquisition Cost (CAC) by channel and Lifetime Value (LTV) under each stage of the monetization path defined in [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md), with payback-period targets benchmarked against typical Series A readiness expectations for Indian consumer marketplaces.

## Context / Problem

A trust-first, activity-first network has an unusual CAC structure: unlike a paid-acquisition-driven dating app, ActivityMate's core growth loop (per [go-to-market-strategy.md](go-to-market-strategy.md)) is host-led and organic — a completed, well-rated activity is itself a distribution surface (participants become future hosts, invite friends into the next activity). This means CAC should be modeled by channel, not as a single blended number, because organic host-led CAC and paid CAC have structurally different payback dynamics and the business's actual health depends on the organic share staying high.

## CAC by Channel (Directional, Pre-Launch Estimates)

| Channel | Description | Estimated CAC (INR) | Notes |
|---|---|---|---|
| Organic host-led | Referral from a completed activity; host invites, participant becomes future host | ₹40–120 | Near-zero marginal cost beyond incentive/referral credits; scales with activity completion rate, not ad spend |
| Community partnerships | Co-working spaces, hostels, relocation Facebook/WhatsApp groups, RWA tie-ins | ₹150–300 | Requires BD/community-ops time (see [../34-operations-support/](../34-operations-support/README.md)), cheaper than paid social per verified user |
| Paid social (Instagram/Meta) | Targeted acquisition in the Mumbai wedge geography | ₹350–600 | Highest CAC; used to seed initial density in [go-to-market-strategy.md](go-to-market-strategy.md), not sustained at scale |
| App store search/organic | Category and branded search | ₹50–100 | Low volume pre-brand-awareness, grows over time |

These are directional planning estimates pending real Mumbai launch data; they will be revised in the first post-launch cycle owned by [../22-analytics-experimentation/](../22-analytics-experimentation/README.md), which is the dependency for this document's next revision.

**Blended CAC target at launch:** weighted toward organic and community channels (target 60%+ of new verified users from non-paid channels within the first two quarters), keeping blended CAC materially below paid-social CAC alone — a direct product of the host-led referral loop being cheaper than acquisition for a trust-scarce product category, where a friend's invite pre-clears a meaningful amount of the trust barrier a paid ad cannot.

## LTV Under Each Monetization Path

LTV is modeled per the three-stage path in [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md):

**Stage 1 (Freemium, no direct monetization).** LTV is effectively indirect — measured as referral value (each retained user's contribution to lowering blended CAC for the next cohort) rather than direct revenue. This is tracked but not treated as a hard financial LTV figure.

**Stage 2 (Subscription).** LTV = (monthly subscription price) × (average subscriber tenure in months) × (gross margin after payment processing and infra cost). Given India price sensitivity findings referenced in [pricing-strategy.md](pricing-strategy.md), initial subscription ARPU is expected to be modest (comparable to regional OTT/subscription pricing tiers, not US dating-app pricing), so LTV depends heavily on retention length rather than price point — reinforcing why trust-driven retention (the [north-star-metric.md](../00-foundation/north-star-metric.md)) matters more to the financial model than conversion rate alone.

**Stage 3 (Commission).** LTV = (average number of paid/ticketed activities per active user per year) × (average transaction value) × (commission rate) × (retention years). This LTV path scales with a user's depth of engagement (how many paid experiences they take, not just whether they subscribe), and is expected to eventually exceed Stage 2 LTV per user for the power-user segment, without requiring universal adoption.

## Payback Period Targets

| Monetization stage | Target payback period | Benchmark rationale |
|---|---|---|
| Stage 2 (Subscription) | 6–9 months blended | Standard consumer subscription payback benchmark used by Series A investors for Indian consumer apps; achievable given low CAC on organic-heavy channel mix |
| Stage 3 (Commission) | 9–14 months blended | Longer payback tolerated since commission LTV compounds with usage depth rather than a flat recurring fee, and Series A readiness benchmarks weight LTV:CAC ratio (target ≥3:1) over payback speed alone at this stage |

## LTV:CAC Ratio Target

Target **LTV:CAC ≥ 3:1** by the time Series A fundraising conversations begin (see [../35-investor-relations/](../35-investor-relations/README.md)), consistent with standard venture benchmarks. Because blended CAC is deliberately kept low through the organic/community channel mix rather than through low LTV, the ratio target is achievable without requiring premium monetization — this is the central financial argument for prioritizing the host-led growth loop in [go-to-market-strategy.md](go-to-market-strategy.md) over paid-acquisition-led growth.

## Risks & Open Questions

- All figures in this document are pre-launch estimates; no real cohort data exists yet. This document must be revised against actual Mumbai cohort data within the first two quarters post-launch, dependency-gated on [../22-analytics-experimentation/](../22-analytics-experimentation/README.md) instrumentation being live.
- Organic CAC assumes referral behavior that has not been empirically validated in the Indian activity-partner context specifically (as opposed to e-commerce or ride-hailing referral behavior, which is better documented) — see open questions in [../02-market-research/survey-interview-findings.md](../02-market-research/survey-interview-findings.md).
- Trust & safety operational cost (human review, verification processing) is a real cost of goods sold that scales with user growth and is not fully captured in a pure CAC/LTV framework; it should be modeled as a cost-per-verified-user line item alongside this document in future revisions.

## Related Documents

- [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md)
- [pricing-strategy.md](pricing-strategy.md)
- [go-to-market-strategy.md](go-to-market-strategy.md)
- [../22-analytics-experimentation/](../22-analytics-experimentation/README.md)
- [../35-investor-relations/](../35-investor-relations/README.md)
