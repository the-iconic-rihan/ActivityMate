---
title: "Five Year Strategic Roadmap"
folder: 01-business-strategy
purpose: "Long-range plan"
pages_estimate: 6
prerequisites: "all business-strategy docs"
dependencies: "planning-roadmap-techdebt"
priority: P1
audience: "F, I"
status: draft-v1
generated: 2026-07-15
---

# Five-Year Strategic Roadmap

## Overview

This document sets the long-range arc — Mumbai to a five-city footprint to a national platform — synchronized against the monetization maturity curve in [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md) and a staged approach to AI/ML investment. It is deliberately a directional arc, not a committed quarter-by-quarter plan; the operational roadmap lives in [../38-planning-roadmap-techdebt/](../38-planning-roadmap-techdebt/README.md).

## Context / Problem

Investors and the founding team need a shared long-range picture that ties city expansion, revenue maturity, and technical investment together, so that none of the three run ahead of the others — e.g., expanding to a fifth city before Mumbai proves retention, or investing in ML-driven matching (see [../23-ai-ml-features/](../23-ai-ml-features/README.md)) before there is enough behavioral data to train on.

## Year-by-Year Arc

**Year 1 — Mumbai Density.** Execute the single-wedge launch in [go-to-market-strategy.md](go-to-market-strategy.md), expand geo-gate to Mumbai-wide once wedge density thresholds are met, hold monetization at Stage 1 (freemium) throughout, and treat every dollar of infrastructure spend as justified only by the modular monolith / Docker Compose architecture in [PRD-001](../PRD-001_Master_Product_Requirements.md) — no premature microservices investment. Primary goal: prove [north-star-metric.md](../00-foundation/north-star-metric.md) trends positive with an acceptable safety-incident rate.

**Year 2 — Mumbai Monetization + Second-City Prep.** Introduce Stage 2 subscription pricing per [pricing-strategy.md](pricing-strategy.md) once retention data justifies it. Begin applying [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md) to rank Pune, Bengaluru, Hyderabad, Delhi NCR, and Chennai. Begin Stage 3 commission activation for paid/ticketed activity categories as the catalog matures. This is also the point at which Series A fundraising conversations (see [../35-investor-relations/](../35-investor-relations/README.md)) are expected to begin, backed by a full year of real Mumbai unit economics rather than projections.

**Year 3 — Multi-City Expansion (2-3 additional cities).** Launch the highest-scoring cities from the readiness framework, applying the same single-wedge-per-city discipline used in Mumbai rather than a broad simultaneous rollout, per the lesson embedded in [go-to-market-strategy.md](go-to-market-strategy.md). Begin staged investment in matching/recommendation intelligence (see [../14-matching-recommendation/](../14-matching-recommendation/README.md)) since by this point there is enough cross-city behavioral data to move beyond simple filters toward learned ranking.

**Year 4 — National Footprint (5-city target reached) + AI Investment Ramp.** Reach the five-city milestone. Begin heavier investment in [../23-ai-ml-features/](../23-ai-ml-features/README.md) — trust-signal modeling (fraud/fake-profile detection augmentation), smarter activity recommendation, and potentially predictive no-show/cancellation risk scoring feeding the trust engine in [../13-trust-safety-fraud/](../13-trust-safety-fraud/README.md). Architecture migration from modular monolith toward microservices (flagged as a post-PMF step in PRD-001 Section 12) is expected to be substantially underway by this point, driven by team scaling and service-level operational needs documented in [../07-architecture/](../07-architecture/README.md), not by premature optimization.

**Year 5 — National Scale + Platform Maturity.** Full national urban footprint across major Tier-1 and selected Tier-2 cities. Monetization mix matures toward a stable blend of subscription and commission revenue (Stage 2 + Stage 3 coexisting at scale, per [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md)). Trust infrastructure — the compounding data moat — is by this point the primary defensible asset against any well-funded competitor entering later, since a five-year trust-score dataset cannot be replicated by a new entrant regardless of their funding.

## Monetization Maturity Curve

| Year | Primary revenue stage | Approx. revenue character |
|---|---|---|
| 1 | Stage 1 (Freemium) | Pre-revenue / negligible |
| 2 | Stage 2 activating + early Stage 3 | Early recurring + transactional revenue |
| 3 | Stage 2 + Stage 3 scaling | Multi-city recurring + transactional |
| 4 | Stage 2 + Stage 3 mature | Revenue diversification, higher-value activity categories |
| 5 | Full mix, national | Stable blended national revenue base |

## Staged AI Investment

AI/ML investment is deliberately sequenced *after* sufficient behavioral data exists, not before — building a recommendation model on Mumbai-only, Year-1 data would overfit to a single dense wedge and generalize poorly. The staged approach: Year 1-2 uses rule-based and simple heuristic matching only (proximity, category overlap, trust-score thresholds); Year 3 introduces learned ranking once multi-city data exists; Year 4-5 introduces predictive trust-risk signals. Full detail owned by [../23-ai-ml-features/](../23-ai-ml-features/README.md) and [../14-matching-recommendation/](../14-matching-recommendation/README.md).

## Risks & Open Questions

- This roadmap assumes Mumbai proves the model within Year 1; if density or safety metrics underperform, every subsequent year shifts right, and the five-city Year-4 milestone becomes unrealistic — the roadmap should be treated as re-baselined annually, not as a fixed commitment.
- City sequencing in Years 2-3 depends entirely on [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md) data that does not yet exist in validated form.
- Open question: does international expansion (beyond India) belong on this roadmap at all within five years, or is it explicitly out of scope until the national footprint is achieved — current position is out of scope, to avoid diluting focus, consistent with Core Product Principle 5 (Local Network Density).

## Related Documents

- [go-to-market-strategy.md](go-to-market-strategy.md)
- [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md)
- [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md)
- [../38-planning-roadmap-techdebt/](../38-planning-roadmap-techdebt/README.md)
- [../35-investor-relations/](../35-investor-relations/README.md)
