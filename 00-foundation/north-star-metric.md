---
title: "North Star Metric"
folder: 00-foundation
purpose: "Single success metric"
pages_estimate: 3
prerequisites: "mission-vision-values"
dependencies: "none"
priority: P0
audience: "F, I, PM"
status: draft-v1
generated: 2026-07-15
---

# North Star Metric

## The Metric

**Weekly Completed Trusted Meetups per Active User (WCTM/AU)** — the number of activities, per active user per week, that (a) reached the scheduled meetup time with at least two verified participants checked in, and (b) were not followed by a safety report or a below-threshold rating from either party.

This is a compound metric by design, not a raw count of meetups. A meetup only counts toward the north star if it happened (not just scheduled) and if it was trustworthy (no report, acceptable rating). This is the one number the company optimizes above all others, and every team's roadmap should be traceable to moving it.

## Why This Metric, Not the Obvious Alternatives

**Signups / registered users** — rejected. Signups measure top-of-funnel curiosity, not the product's actual promise. A user can register, complete phone-OTP verification per [PRD-001](../PRD-001_Master_Product_Requirements.md) Section 9, and never attend anything. Optimizing for signups rewards the marketing team for driving installs that never convert to trust, which is the opposite of what a trust-first company should reward.

**DAU / MAU** — rejected as the primary metric, though retained as a supporting metric. ActivityMate is not a habitual-checking app like a social feed or a messaging client; a healthy user might open the app twice a week to plan a weekend trek and that is not a failure state. Optimizing for DAU would pressure the product toward notification-bait and infinite-scroll patterns that contradict Core Product Principle 1 (Activity First) and would specifically punish the exact usage pattern — low-frequency, high-intent — that the target personas in [PRD-001](../PRD-001_Master_Product_Requirements.md) actually exhibit.

**GMV / revenue** — rejected as a north star this early, though it becomes a co-metric post-monetization (see [revenue-model-monetization-strategy.md](../01-business-strategy/revenue-model-monetization-strategy.md)). At the pre-PMF stage, optimizing for revenue would pull the roadmap toward payment features before the core trust loop is proven, inverting the dependency order the tech stack in PRD-001 already assumes (modular monolith first, payments layered in later).

**Matches made / activities created** — rejected because it measures supply-side intent, not realized outcomes. A dating-app-style "match" metric would reward the exact behavior — creating interest without following through — that the Non-Goals section of PRD-001 explicitly excludes (no anonymous chatting, no hookup-style engagement loops). Activities created without completion is closer to a leading indicator of ghosting than of trust.

## Why "Trusted" Is Load-Bearing in the Metric

A meetup that happens but ends in a safety report is worse for the business than a meetup that never happens at all — it damages the trust score of both participants, generates moderation load (see [../21-moderation-content-ops/](../21-moderation-content-ops/README.md)), and if repeated, undermines the entire value proposition versus WhatsApp groups or Meetup, neither of which promise safety. Excluding reported/low-rated meetups from the numerator forces every team, especially growth, to internalize that raw volume is not success.

## How It Decomposes (Guardrail Metrics)

WCTM/AU is a lagging, compound signal, so the following guardrails are tracked alongside it, each owned by a specific function:

| Guardrail | What it catches | Owner |
|---|---|---|
| Activity Completion Rate | scheduled meetups that no-show | PM |
| Host Acceptance Rate | hosts rejecting too many/too few join requests | PM / Trust & Safety |
| Female Retention (Week 4) | the single highest-leverage safety proxy — see [../03-user-research/trust-perception-research.md](../03-user-research/trust-perception-research.md) | Trust & Safety |
| Average Trust Score (cohort) | whether the population is trending safer over time | Trust & Safety |
| Report Rate per 1,000 Meetups | absolute safety incident volume | Trust & Safety |
| Repeat Participation (Meetup 2 within 30 days) | whether trust converts to habit | Growth |

These map directly to the Success Metrics list in [PRD-001](../PRD-001_Master_Product_Requirements.md) Section 10; WCTM/AU is the aggregation point that forces DAU, WAU, Activity Completion Rate, and Average Trust Score to be read together rather than cherry-picked individually in board updates.

## Targets and Cadence

At Mumbai launch, the target is qualitative (establish measurement, instrument the "checked in" and "report-free" signals correctly) rather than numeric, since a false-precision target on an unlaunched product invites gaming. Once weekly active hosts exceed roughly 500 in the Mumbai wedge geography defined in [go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md), the metric graduates to a board-reported number with a quarter-over-quarter growth target, reviewed at the same cadence as the [../35-investor-relations/](../35-investor-relations/README.md) reporting cycle.

## Risks & Open Questions

- "Checked in" requires a reliable presence signal (GPS check-in, QR at venue, or mutual confirmation) that is not yet specified — owned by [../17-maps-location/](../17-maps-location/README.md).
- The metric could be gamed by hosts who only accept participants they already know, inflating completion at the cost of the "meet someone new" value proposition; the Host Acceptance Rate guardrail exists specifically to detect this.
- Open question: should a repeat meetup between the same two users count fully toward WCTM/AU, or decay in weight over repeat occurrences, to keep the metric honest about *new* trusted connections versus scheduling convenience for existing ones. Unresolved pending Analytics tooling in [../22-analytics-experimentation/](../22-analytics-experimentation/README.md).

## Related Documents

- [mission-vision-values.md](mission-vision-values.md) — the mission this metric operationalizes
- [../01-business-strategy/five-year-strategic-roadmap.md](../01-business-strategy/five-year-strategic-roadmap.md) — how the metric's target evolves with monetization maturity
- [../22-analytics-experimentation/](../22-analytics-experimentation/README.md) — instrumentation owner
