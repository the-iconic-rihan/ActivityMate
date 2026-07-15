---
title: "Recommendation Engine Architecture"
folder: 14-matching-recommendation
purpose: "Algorithm choice"
pages_estimate: 6
prerequisites: "database-selection-rationale"
dependencies: "none"
priority: P1
audience: "BE, ML"
status: draft-v1
generated: 2026-07-15
---

# Recommendation Engine Architecture

## 1. Overview

ActivityMate's discovery feed has to answer "what activity should I see first" for a user who, on day one in a new city, has zero interaction history. This document picks the foundational recommendation approach and sets the migration path as the platform accumulates data. It is the architectural parent of [matching-algorithm-spec.md](matching-algorithm-spec.md) (person-to-activity fit logic) and [ranking-signals.md](ranking-signals.md) (feed ordering).

## 2. Inputs / Signals

Two standard recommendation paradigms were evaluated:

- **Collaborative filtering** — infers relevance from patterns across many users' interaction history ("people who joined activities like you also joined..."). Requires substantial interaction density to produce meaningful neighborhoods; with a handful of activities per neighborhood in a newly launched city, there simply isn't enough signal, and recommendations default to noise.
- **Content-based filtering** — infers relevance from the attributes of the activity itself (interest tags, category, location, time) matched against a user's stated interests and profile, with no interaction history required. Works identically for the first user in a city and the ten-thousandth.

## 3. Processing / Logic

**Decision: content-based filtering at launch**, blending in collaborative signals once activity volume crosses a density threshold per city.

```
Phase 1 (0 to ~5,000 completed activities per city):
    recommendation_score = content_based_similarity(user_interests, activity_tags)
                            * proximity_decay(distance)
                            * recency_boost(time_to_activity)
    No collaborative component — insufficient neighbor density to trust it.

Phase 2 (city crosses ~5,000 completed activities):
    recommendation_score = 0.7 * content_based_similarity
                          + 0.3 * collaborative_filtering_score
    Collaborative weight increases gradually (tracked via
    ab-testing-plan-for-matching.md) as density grows, never
    fully replacing content-based scoring — content-based signal
    remains valuable even at scale for genuinely novel activity
    categories with no comparison history.
```

Content-based similarity is computed as cosine similarity between a user's interest-tag vector (set during onboarding, refined by explicit category follows) and an activity's tag vector, drawn from the fixed category list in PRD-001 §8 (Coffee, Cycling, Trekking, Board Games, Networking, etc.) plus free-text tags.

## 4. Outputs & UI Surfacing

- Powers the primary discovery feed ranking (final blend defined in [ranking-signals.md](ranking-signals.md)).
- Powers "Suggested for you" activity carousels and community recommendations.
- Exposed internally as a per-(user, activity) relevance score consumed by [matching-algorithm-spec.md](matching-algorithm-spec.md), which layers trust-compatibility and schedule fit on top.

## 5. Edge Cases

- **Sparse-tag users** (filled minimal onboarding interests): fall back further toward proximity + popularity, per [cold-start-strategy.md](cold-start-strategy.md).
- **Cross-city travel** (a user browsing a city they don't reside in, e.g. a solo traveller): location context switches to the browsed city, interest vector carries over unchanged.
- **Category imbalance** (Mumbai launch data skews toward Coffee/Dinner over Trekking): content-based scoring doesn't correct for this on its own; addressed operationally through host-recruitment strategy in [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md), not by the algorithm.

## 6. Related Documents

- [matching-algorithm-spec.md](matching-algorithm-spec.md) — person-level scoring built on this foundation
- [ranking-signals.md](ranking-signals.md) — feed-ordering signal blend
- [cold-start-strategy.md](cold-start-strategy.md) — fallback for zero-history users/cities
- [../10-database/database-selection-rationale.md](../10-database/database-selection-rationale.md) — storage/query engine this relies on
- [../20-search-discovery/discovery-feed-algorithm.md](../20-search-discovery/discovery-feed-algorithm.md) — explicit-intent counterpart to this passive-discovery system
