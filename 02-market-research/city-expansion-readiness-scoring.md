---
title: "City Expansion Readiness Scoring"
folder: 02-market-research
purpose: "Expansion framework"
pages_estimate: 4
prerequisites: "tam-sam-som"
dependencies: "localization-expansion"
priority: P2
audience: "F, G"
status: draft-v1
generated: 2026-07-15
---

# City Expansion Readiness Scoring

## Overview

A repeatable scoring model for ranking candidate cities — Pune, Bengaluru, Hyderabad, Delhi NCR, and Chennai — for launch after Mumbai, to be applied once the density thresholds in [../01-business-strategy/go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md) are met and the Year-2/3 window in [../01-business-strategy/five-year-strategic-roadmap.md](../01-business-strategy/five-year-strategic-roadmap.md) opens. This replaces founder intuition with an explicit, weighted framework so expansion sequencing is defensible to the board and consistent with Core Product Principle 5 (Local Network Density).

## Context / Problem

Not all large Indian cities are equally ready for a trust-first, activity-first product. Population size alone is a poor predictor — a city can be large but have low existing meetup culture, weak baseline safety perception, or a demographic mix poorly matched to the relocated-professional/student persona the Mumbai wedge validated. A scoring model forces expansion decisions to be evidence-based rather than narrative-driven ("we should be in Bengaluru because it's the tech capital").

## Scoring Dimensions

| Dimension | Weight | Rationale |
|---|---|---|
| Population density of target persona (relocated professionals, students, young urban migrants) | 25% | Directly determines bottom-up SOM per the method in [tam-sam-som.md](tam-sam-som.md) |
| English/Hindi digital fluency | 15% | Affects onboarding friction and content/notification localization cost, per [../41-localization-expansion/](../41-localization-expansion/README.md) |
| Existing meetup/informal-activity culture | 20% | Cities with mature running clubs, trekking groups, hobby communities lower cold-start host-recruitment cost, per Finding 9 in [india-social-behavior-research.md](india-social-behavior-research.md) |
| Baseline safety perception (particularly for women) | 25% | Given the gendered safety asymmetry validated in [survey-interview-findings.md](survey-interview-findings.md) as the highest safety-relevance finding, a city with poor baseline safety perception raises both product risk and Female Retention risk regardless of other strengths |
| Competitive density (informal + direct competitor presence) | 15% | A market where WhatsApp-group and dating-app substitutes are weaker gives a faster path to felt differentiation |

Weights deliberately mirror the product's own priorities: safety perception and target-persona density together account for half the score, reflecting that this is a trust company expanding cities, not a generic marketplace optimizing for raw reach.

## Directional Scoring (Pre-Fieldwork Estimate)

Scores are illustrative and directional (1-5 scale per dimension), pending real city-specific fieldwork before any launch decision is finalized:

| City | Persona Density | Digital Fluency | Meetup Culture | Safety Perception | Competitive Gap | Weighted Score |
|---|---|---|---|---|---|---|
| Bengaluru | 5 | 5 | 4 | 4 | 3 | High |
| Pune | 4 | 4 | 4 | 4 | 3 | High |
| Hyderabad | 4 | 4 | 3 | 4 | 4 | Moderate-High |
| Delhi NCR | 5 | 4 | 3 | 2 | 3 | Moderate (safety perception drags score) |
| Chennai | 3 | 3 | 3 | 4 | 4 | Moderate |

Bengaluru and Pune score highest — both have large relocated-professional and student populations (tech and education hubs respectively), strong existing informal meetup culture, and reasonably strong baseline safety perception. Delhi NCR has very high persona density but scores materially lower overall because of comparatively weaker baseline safety perception, which the weighting model deliberately treats as a near-disqualifying factor rather than an offsettable weakness, consistent with the "safety over growth" operating value in [../00-foundation/mission-vision-values.md](../00-foundation/mission-vision-values.md).

## Implications for Product

- Bengaluru and Pune should be treated as the leading candidates for the Year-2/3 second-city launch window in [../01-business-strategy/five-year-strategic-roadmap.md](../01-business-strategy/five-year-strategic-roadmap.md), pending real fieldwork confirmation.
- Delhi NCR's low safety-perception score should not be read as "never launch," but as "launch only with a materially stronger trust/safety operational footprint pre-established" — a nuance for [../21-moderation-content-ops/](../21-moderation-content-ops/README.md) to plan around if Delhi NCR is later prioritized for other reasons (e.g., investor or talent-market considerations).
- Each future expansion should re-run this scoring model with real fieldwork data (mirroring the bottom-up method in [tam-sam-som.md](tam-sam-som.md)) rather than reusing Mumbai assumptions, since relocation and safety-perception patterns are not uniform across Indian metros.

## Risks & Open Questions

- All scores in this document are directional placeholders based on general knowledge of these cities, not primary fieldwork; this document must not be used as the sole basis for a real expansion decision without a dedicated fieldwork pass per city.
- Open question: should the model weight cost-of-living and local price sensitivity (relevant to [../01-business-strategy/pricing-strategy.md](../01-business-strategy/pricing-strategy.md)) as a sixth dimension — currently excluded because monetization maturity by Year 2-3 is expected to vary independently of city choice, but this should be revisited once Stage 2/3 monetization is live.
- Open question: how much weight should local government/regulatory posture toward late-night gathering and safety (relevant given the PRD-001 use case of night activities post-10pm) receive independently of general safety perception — currently folded into the Safety Perception dimension rather than scored separately.

## Related Documents

- [tam-sam-som.md](tam-sam-som.md)
- [india-social-behavior-research.md](india-social-behavior-research.md)
- [../01-business-strategy/five-year-strategic-roadmap.md](../01-business-strategy/five-year-strategic-roadmap.md)
- [../41-localization-expansion/](../41-localization-expansion/README.md)
