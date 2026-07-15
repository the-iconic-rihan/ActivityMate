---
title: "Ranking Signals"
folder: 14-matching-recommendation
purpose: "Ranking weights"
pages_estimate: 3
prerequisites: "matching-algorithm-spec"
dependencies: "analytics-experimentation"
priority: P1
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Ranking Signals

## 1. Overview

[matching-algorithm-spec.md](matching-algorithm-spec.md) scores how well a *specific* activity fits a *specific* user. This document defines how those per-activity MatchScores get combined with feed-level signals — recency, urgency, social proof — to produce the final ordered list a user scrolls through. Matching answers "is this relevant to you"; ranking answers "in what order should relevant things appear."

## 2. Inputs / Signals

| Signal | Weight | Rationale |
|---|---|---|
| MatchScore (from matching-algorithm-spec.md) | 45% | Primary relevance driver |
| Recency (time since activity was published) | 15% | Freshly posted activities get initial visibility to gather RSVP momentum |
| Fill-rate urgency (spots remaining ÷ capacity) | 15% | Nearly-full activities surface higher — creates healthy urgency and reduces last-minute cancellations from indecision |
| Host Trust Tier | 15% | Higher-trust hosts get a modest visibility boost, reinforcing the "trust before matching" product principle |
| Social proof (mutual connections/attendees already joined) | 10% | Familiar faces reduce first-meetup anxiety, a known driver of no-shows |

## 3. Processing / Logic

```
FeedRank = 0.45*MatchScore + 0.15*RecencyScore + 0.15*UrgencyScore
         + 0.15*HostTierScore + 0.10*SocialProofScore

RecencyScore = decay function, half-life ~48 hours from publish time

UrgencyScore = spots_filled / capacity
               (capped: fully-full activities are excluded from the
               feed entirely once capacity-locked, per
               ../15-activities-communities/rsvp-capacity-waitlist-logic.md
               — this signal only applies while still joinable)

HostTierScore = normalized Trust Tier of activity host (0 to 1 scale)

SocialProofScore = min(1.0, mutual_connections_attending * 0.25)
```

Weights are the P1-launch defaults; [ab-testing-plan-for-matching.md](ab-testing-plan-for-matching.md) governs how they get tuned against real post-launch data rather than staying fixed indefinitely.

## 4. Outputs & UI Surfacing

- Determines discovery-feed scroll order.
- Feeds the "Trending near you" and "Filling up fast" feed sections as distinct rankings sorted primarily by UrgencyScore and RecencyScore respectively, rather than the blended FeedRank.

## 5. Edge Cases

- **New host with no Trust Tier history yet** (Tier 0/1): HostTierScore contributes minimally rather than zeroing out the activity's visibility entirely — a new legitimate host shouldn't be invisible, just not artificially boosted.
- **Popularity feedback loop**: without correction, high-ranking activities get more views, more RSVPs, and rank even higher, starving new/small hosts of visibility. Mitigated by injecting a small exploration slot (roughly 1 in 8 feed positions) reserved for otherwise-lower-ranked but eligible activities, revisited once analytics data is available (see [../22-analytics-experimentation/analytics-event-taxonomy.md](../22-analytics-experimentation/analytics-event-taxonomy.md)).

## 6. Related Documents

- [matching-algorithm-spec.md](matching-algorithm-spec.md) — MatchScore input
- [ab-testing-plan-for-matching.md](ab-testing-plan-for-matching.md) — weight-tuning process
- [../15-activities-communities/rsvp-capacity-waitlist-logic.md](../15-activities-communities/rsvp-capacity-waitlist-logic.md) — capacity-lock interaction with UrgencyScore
- [../22-analytics-experimentation/analytics-event-taxonomy.md](../22-analytics-experimentation/analytics-event-taxonomy.md) — event data this relies on
