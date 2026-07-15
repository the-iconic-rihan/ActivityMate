---
title: "Ab Testing Plan For Matching"
folder: 14-matching-recommendation
purpose: "Tuning plan"
pages_estimate: 3
prerequisites: "ranking-signals"
dependencies: "analytics-experimentation"
priority: P2
audience: "BE, PM"
status: draft-v1
generated: 2026-07-15
---

# Ab Testing Plan For Matching

## 1. Overview

The weights in [ranking-signals.md](ranking-signals.md) and [matching-algorithm-spec.md](matching-algorithm-spec.md) are launch-day best estimates, not empirically tuned values — there is no post-launch behavioral data yet to tune them against. This document is the experiment plan for how those weights get validated and adjusted once real usage data exists, built on the shared experimentation infrastructure in [../22-analytics-experimentation/ab-testing-platform-spec.md](../22-analytics-experimentation/ab-testing-platform-spec.md).

## 2. Inputs / Signals

- **Primary metric**: RSVP conversion rate (feed impression → RSVP) and 30-day repeat participation rate.
- **Guardrail metrics** (must not regress in any variant): activity completion rate, report rate per 1,000 activities, host acceptance rate. A ranking change that lifts conversion but degrades safety-adjacent guardrails is a failed experiment regardless of the primary metric.
- **Segment cuts**: new vs. returning users, by city (once multi-city), by gender (Female Retention is an explicit PRD-001 success metric and warrants its own segment view given the trust/safety stakes).

## 3. Processing / Logic

```
Experiment unit: user (bucketed, sticky assignment for experiment duration)
Minimum sample size: computed per-experiment from baseline conversion
                      variance, targeting 80% power at a 5% MDE
Standard duration: minimum 2 full weeks to capture weekday/weekend
                    activity-category variance (e.g. Coffee skews weekday,
                    Weekend Trip skews Friday-feed)

Rollout sequence:
  1. Shadow mode — compute variant ranking, log it, serve control ranking
     (validates the new weights don't produce degenerate output before
     any user sees it)
  2. 5% traffic canary — guardrail metrics monitored daily
  3. 50/50 split — full statistical run to the pre-computed sample size
  4. Winner rolled out to 100%, losing variant's weights archived with
     rationale in ../39-decision-records-rfcs/
```

Guardrail-metric regression at any stage halts the experiment and reverts to control automatically — guardrails are treated as hard stops, not just reporting metrics, consistent with "Safety Over Growth" as a core product principle.

## 4. Outputs & UI Surfacing

- Internal-only; no user-facing indication that ranking is under experiment.
- Results feed a changelog of ranking-weight iterations, tracked so the "why did the feed change" question is always answerable internally.

## 5. Edge Cases

- **Insufficient traffic pre-PMF to reach statistical power**: acceptable to run underpowered directional tests early, explicitly labeled as such, rather than blocking all tuning until traffic is large — but no underpowered result changes a guardrail-adjacent weight without additional qualitative review.
- **Multi-armed interaction effects** (matching experiment overlapping a pricing or notification experiment): coordinated through the shared experimentation platform's mutual-exclusion groups, not run in this document's isolation.

## 6. Related Documents

- [ranking-signals.md](ranking-signals.md) — weights under test
- [matching-algorithm-spec.md](matching-algorithm-spec.md) — scoring logic under test
- [../22-analytics-experimentation/ab-testing-platform-spec.md](../22-analytics-experimentation/ab-testing-platform-spec.md) — shared experimentation infrastructure
- [../22-analytics-experimentation/north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md) — where guardrail metrics are tracked
