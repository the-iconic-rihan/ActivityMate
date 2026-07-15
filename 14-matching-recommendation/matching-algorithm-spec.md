---
title: "Matching Algorithm Spec"
folder: 14-matching-recommendation
purpose: "Matching logic"
pages_estimate: 5
prerequisites: "recommendation-engine-architecture"
dependencies: "trust-safety-fraud"
priority: P1
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Matching Algorithm Spec

## 1. Overview

Where [recommendation-engine-architecture.md](recommendation-engine-architecture.md) decides *how* relevance is computed in general, this document specifies the concrete scoring function used to suggest specific activities to a specific user — the thing that actually runs when a user opens the app. It combines interest overlap, physical proximity, and trust-score compatibility, the last of which is what keeps this a *trust-first* matcher rather than a generic interest-based feed.

## 2. Inputs / Signals

| Signal | Source | What it measures |
|---|---|---|
| Interest overlap | User interest tags vs. activity tags | Jaccard similarity between the two tag sets |
| Proximity | User's set location vs. activity venue (from [../17-maps-location](../17-maps-location/)) | Inverse distance decay within a configurable radius (default 5km urban, wider for weekend-trip categories) |
| Trust compatibility | User Trust Tier vs. activity's host tier and existing attendee tiers (from [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)) | Penalizes large tier gaps, especially for night-window activities |
| Schedule fit | Activity start time vs. user's stated availability patterns (if provided) | Soft preference boost, not a hard filter |

## 3. Processing / Logic

```
MatchScore = 0.35 * interest_overlap
           + 0.30 * proximity_score
           + 0.20 * trust_compatibility
           + 0.15 * schedule_fit

interest_overlap = |user_tags ∩ activity_tags| / |user_tags ∪ activity_tags|

proximity_score = max(0, 1 - distance_km / radius_km)

trust_compatibility:
    base = 1.0
    tier_gap = abs(user.trust_tier - host.trust_tier)
    IF activity.is_night_window:
        # Night activities enforce a hard floor at the eligibility gate
        # (see 13-trust-safety-fraud/safety-engine-spec.md); this score
        # additionally *soft*-penalizes tier gaps even above that floor,
        # since a Tier 2-Verified newcomer matched against an
        # experienced Tier 3 group of strangers at night is a weaker
        # fit than a same-tier match, even when both are technically
        # eligible.
        penalty = 0.25 * tier_gap
    ELSE:
        penalty = 0.10 * tier_gap
    trust_compatibility = max(0, base - penalty)

schedule_fit = 1.0 if activity falls in user's stated available windows,
               0.5 if no preference stated, 0.2 if outside stated windows
```

Activities failing the hard Trust Tier eligibility gate (night-window minimum tiers, group-size minimums per [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md)) are excluded from the candidate set entirely before scoring — MatchScore never overrides a hard safety gate, it only ranks among already-eligible candidates.

## 4. Outputs & UI Surfacing

- Per-user ranked candidate list of activities, fed into the discovery feed after blending with the signals in [ranking-signals.md](ranking-signals.md).
- "Why this was suggested" micro-copy (e.g. "near you", "matches your interest in Trekking") drawn from whichever signal contributed most to the score — improves user trust in the system per the transparency reasoning in [../13-trust-safety-fraud/trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md), without exposing the exact formula.

## 5. Edge Cases

- **New activity with zero interest tags mismatched to any user** (poorly tagged listing): falls back to proximity + host trust tier only; flagged in host-side UX to encourage adding tags.
- **User with conflicting stated availability and actual behavior** (says weekday evenings, actually joins weekend activities): schedule_fit is a soft signal specifically so it never suppresses a genuinely good match — weight is capped at 15% of total score.
- **Small-group capacity activities (2-15 people per PRD-001) nearing capacity**: MatchScore is unaffected by remaining capacity — urgency/fill-rate is handled separately in ranking, not folded into per-activity relevance, keeping this spec focused purely on fit.

## 6. Related Documents

- [recommendation-engine-architecture.md](recommendation-engine-architecture.md) — foundational algorithm choice
- [ranking-signals.md](ranking-signals.md) — how MatchScore combines with other feed-ordering signals
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) — trust tier source data
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md) — hard eligibility gates applied before scoring
- [../15-activities-communities/activity-lifecycle-spec.md](../15-activities-communities/activity-lifecycle-spec.md) — activity states this operates over
