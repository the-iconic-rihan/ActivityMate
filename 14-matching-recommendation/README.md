# 14-matching-recommendation/

**Phase:** Phase 4 — Trust, Safety & Core Product  
**Priority:** P1 (P1 — blocks public launch)  
**Owner:** ML / BE Lead  
**Depends on:** database, trust-safety-fraud  
**Document count:** 5

## Purpose

Surfaces relevant activities and people to reduce discovery friction. Corresponds to PRD-005 and PRD-006.

## Why this folder exists

Distinct from search: search serves explicit intent, this folder serves implicit and passive discovery, and the underlying algorithms and data needs differ enough to warrant separate ownership.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [recommendation-engine-architecture.md](recommendation-engine-architecture.md) | Algorithm choice | Compares collaborative filtering (needs a density of interaction data the platform won't have pre-launch — the classic cold-start problem) against content-based filtering (works from day one using interest tags and location, no interaction history required). Recommends content-based filtering at launch, blending in collaborative signals once activity volume is sufficient. | 6 | database-selection-rationale | none | P1 | BE, ML |
| [matching-algorithm-spec.md](matching-algorithm-spec.md) | Matching logic | Interest-overlap, proximity, and trust-score-compatibility scoring for activity suggestions. | 5 | recommendation-engine-architecture | trust-safety-fraud | P1 | BE |
| [ranking-signals.md](ranking-signals.md) | Ranking weights | The signal list and weights feeding the discovery feed ranking. | 3 | matching-algorithm-spec | analytics-experimentation | P1 | BE |
| [cold-start-strategy.md](cold-start-strategy.md) | New-user / new-city fallback | How new users and newly launched cities get relevant results with zero history — fallback to popularity plus proximity. | 3 | recommendation-engine-architecture | market-research | P1 | BE, PM |
| [ab-testing-plan-for-matching.md](ab-testing-plan-for-matching.md) | Tuning plan | The experiment plan for tuning ranking weights once real post-launch data exists. | 3 | ranking-signals | analytics-experimentation | P2 | BE, PM |
