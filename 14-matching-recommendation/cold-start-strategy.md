---
title: "Cold Start Strategy"
folder: 14-matching-recommendation
purpose: "New-user / new-city fallback"
pages_estimate: 3
prerequisites: "recommendation-engine-architecture"
dependencies: "market-research"
priority: P1
audience: "BE, PM"
status: draft-v1
generated: 2026-07-15
---

# Cold Start Strategy

## 1. Overview

Content-based filtering (per [recommendation-engine-architecture.md](recommendation-engine-architecture.md)) solves the zero-*interaction*-history problem, but two harder cold-start cases remain: a brand-new user with barely-filled-in interests, and a brand-new city with barely any activities to recommend at all. This document defines the fallback logic for both.

## 2. Inputs / Signals

- **New-user case**: onboarding-collected interest categories (from the fixed PRD-001 §8 category list), stated home locality, age bracket — minimal but present even for a zero-history user.
- **New-city case**: seed activity volume, organizer/ambassador-hosted activity count, category coverage breadth — tracked via [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md).

## 3. Processing / Logic

### 3.1 New user, established city

```
IF user.completed_activities == 0 AND user.interest_tags.count < 3:
    ranking falls back to: popularity_in_city_score * proximity_score
    (skip MatchScore's interest_overlap component — insufficient signal,
    would just add noise)
ELSE IF user.interest_tags.count >= 3:
    standard matching-algorithm-spec.md scoring applies immediately —
    onboarding-declared interests are enough to seed content-based
    matching even with zero interaction history
```

Onboarding is deliberately designed to collect at least 3 interest tags before a user reaches the main feed, specifically to shorten time spent in the weaker popularity-fallback mode.

### 3.2 New city, pre-density

A newly launched city has no activity volume to be popular *or* proximate to. Launch strategy (see [../32-growth-marketing/launch-city-playbook-mumbai.md](../32-growth-marketing/launch-city-playbook-mumbai.md)) seeds initial supply through:

- Organizer/ambassador-hosted activities, manually recruited ahead of public launch, tagged across a deliberately broad spread of categories to avoid an empty-feed impression in any one interest area.
- Community-partnership activities (hobby clubs, coworking spaces) imported as a density floor.
- A relaxed proximity radius during the pre-density phase (e.g. 5km default expands to 15km) so the sparse initial supply still populates a feed, tightening back down as density grows.

```
density_threshold_reached = completed_activities_last_30_days_in_city >= 200
  (approximate operational floor for standard proximity radius and
  popularity-fallback to produce a non-empty, relevant feed)
```

## 4. Outputs & UI Surfacing

- New users see an explicit "Building your recommendations" state during the popularity-fallback phase rather than a feed presented as fully personalized, managing expectations honestly.
- New-city launches are marketed and product-gated (see [../41-localization-expansion](../41-localization-expansion/)) to avoid opening a city before the ambassador-seeded activity floor is met.

## 5. Edge Cases

- **User travels to a city below the density threshold**: same relaxed-radius fallback applies regardless of home-city status.
- **Rapid early growth outpacing the density check's 30-day window**: threshold is re-evaluated on a rolling basis, not a one-time city-launch gate, so a city can move in and out of fallback mode if activity volume dips.

## 6. Related Documents

- [recommendation-engine-architecture.md](recommendation-engine-architecture.md) — algorithm this falls back from
- [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md) — city-launch readiness criteria
- [../32-growth-marketing/launch-city-playbook-mumbai.md](../32-growth-marketing/launch-city-playbook-mumbai.md) — supply-seeding tactics
