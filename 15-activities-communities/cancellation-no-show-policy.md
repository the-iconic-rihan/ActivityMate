---
title: "Cancellation No Show Policy"
folder: 15-activities-communities
purpose: "Cancellation policy"
pages_estimate: 3
prerequisites: "rsvp-capacity-waitlist-logic"
dependencies: "trust-safety-fraud"
priority: P0
audience: "PM, BE"
status: draft-v1
generated: 2026-07-15
---

# Cancellation No Show Policy

## 1. Overview

Ghosting and no-shows are named explicitly as platform risks in PRD-001 §11. This policy defines when a cancellation is penalty-free, when it costs the user something, and how a no-show — the more damaging failure mode, since it wastes a venue trip and can leave a solo counterpart waiting alone — feeds into the Trust Engine so that flaking carries a real, visible cost rather than being consequence-free.

## 2. Context / Problem

Two competing needs pull against each other here. Users need genuine flexibility — plans change, and a policy too punitive discourages RSVPing at all, hurting the density the platform depends on. But a counterpart who's shown up to a public venue, possibly at night, needs the platform to make flaking costly enough that it's rare. The policy below resolves this with graduated windows rather than a single flat rule.

## 3. Options Considered

| Approach | Pros | Cons |
|---|---|---|
| No cancellation penalty ever | Maximizes RSVP willingness | No deterrent against habitual flaking; erodes the trust the whole platform is built on |
| Flat penalty regardless of timing | Simple to implement | Punishes a 2-week-advance cancellation identically to a doorstep no-show — disproportionate |
| Graduated windows (recommended) | Matches penalty to actual harm caused | More states to implement and communicate clearly |

## 4. Recommendation & Rationale — Windows and Consequences

| Timing | Classification | Trust Score effect |
|---|---|---|
| >24h before start | Free cancellation | None |
| 2–24h before start | Late cancellation | Minor negative weight in the Behavior component's `late_cancel_rate` signal (see [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) §2.2) |
| <2h before start, or no check-in by start_time + 20min | No-show | Major negative weight in the Behavior component's `no_show_rate` signal — weighted roughly 2x a late cancellation, reflecting the higher real-world cost |
| Host cancels the whole activity | Host cancellation | Applies the same graduated windows to the host, since a host cancelling on committed guests carries identical real-world cost to a guest no-show |

```
IF no_shows_last_90_days >= 3:
    temporary suspension from joining new activities for 14 days
    (does not affect ability to complete already-confirmed upcoming
    RSVPs, only new joins — avoids compounding harm to a third party
    who's already counting on this user)
```

## 5. Enforcement

- Enforcement is fully automated through the Trust Engine's behavioral scoring — no manual review needed for a standard cancellation/no-show, keeping the system fast and consistent.
- The 3-strikes 14-day suspension is a hard product rule, not a scoring nudge, because repeated no-shows are exactly the pattern that erodes trust density fastest in a small-group, real-world-meetup product.
- Exceptions (documented emergency, platform outage preventing check-in) are handled through a manual appeal to Trust & Safety, logged and excluded from the automatic count when substantiated — consistent with the appeals pattern in [../21-moderation-content-ops/appeals-process.md](../21-moderation-content-ops/appeals-process.md).

## 6. Related Documents

- [rsvp-capacity-waitlist-logic.md](rsvp-capacity-waitlist-logic.md) — where no-show detection originates (check-in cutoff)
- [activity-lifecycle-spec.md](activity-lifecycle-spec.md) — lifecycle states this policy attaches to
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) — scoring formula this feeds
- [../21-moderation-content-ops/appeals-process.md](../21-moderation-content-ops/appeals-process.md) — exception-handling process
