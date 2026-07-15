---
title: "North Star Kpi Dashboard Spec"
folder: 22-analytics-experimentation
purpose: "KPI dashboard"
pages_estimate: 3
prerequisites: "data-warehouse-architecture"
dependencies: "foundation"
priority: P1
audience: "F, PM, I"
status: draft-v1
generated: 2026-07-15
---

# North Star KPI Dashboard Spec

## Overview

This spec defines the single dashboard that Founders, PM, and Investors read to answer "is ActivityMate working" without wading through raw event tables. It surfaces one north-star metric and the component drivers that explain movement in it, sourced from [data-warehouse-architecture.md](data-warehouse-architecture.md).

## North Star Metric

**Completed Trusted Activities per Weekly Active User (CTA/WAU).**

Definition: count of activities that reach `activity_completed` status (not merely created or joined) divided by weekly active users, in a rolling 7-day window, filtered to activities where the host had `trust_score_bucket` of `basic` or above at time of hosting.

Why this metric and not raw DAU/WAU or activity-creation count: ActivityMate's stated mission is to help people actually participate in real-world experiences they'd otherwise skip (PRD-001 §1) — not to maximize app opens or activity postings that never convene. A metric anchored on *completion* punishes ghosting and no-shows (named risks in PRD-001 §11) rather than rewarding surface-level engagement. Anchoring it to activities hosted by at least `basic`-trust users also keeps the metric aligned with Core Principle #2, "Trust Before Matching" — growth in raw completed activities driven by unverified accounts would be a false positive on this dashboard.

## Component Driver Metrics

The dashboard tree below the north-star number, each with a trend sparkline and week-over-week delta:

1. **Activity Completion Rate** — `activity_completed` / `activity_published`. Explains whether the funnel from posting to convening is healthy.
2. **Repeat Participation Rate** — % of WAU who complete a second activity within 30 days of their first. Distinguishes one-off tourists from users building a habit.
3. **Female Retention (7-day, 30-day)** — cohort retention split by declared gender. Called out explicitly per PRD-001 §10 success metrics; women's safety and retention is treated as a leading indicator of trust-model health, not a diversity afterthought.
4. **Average Trust Score** (platform-wide median, and by verification level) — rising median trust score indicates the verification funnel and trust engine are functioning as designed.
5. **Host Acceptance Rate** — `join_request_accepted` / `join_request_sent`. Low values signal either host pickiness (possibly rational, trust-driven) or supply-demand imbalance by category/city.
6. **Average Rating** (post-activity) — quality signal independent of completion.
7. **DAU / WAU / DAU-WAU ratio (stickiness)** — standard engagement context, kept subordinate to the north star rather than leading it.

## Guardrail Tiles (always visible, red/amber/green)

- Report rate per 1,000 completed activities
- No-show rate
- Time-to-first-verification-level (median hours from signup to phone+email verified)
- Cancellation rate within 2 hours of activity start

These mirror the guardrails defined in [ab-testing-platform-spec.md](ab-testing-platform-spec.md) so that day-to-day dashboard monitoring and experiment guardrails use one consistent metric definition, not two drifting ones.

## Audience-Specific Views

- **Founders / PM view:** full driver tree, category-level breakdown (coffee vs. trekking vs. weekend trip completion rates differ structurally and should not be pooled), city filter (Mumbai only at launch, ready for multi-city).
- **Investor view:** a reduced slide-ready summary — north star trend, WAU, female retention, and a one-line automated narrative ("CTA/WAU up 12% WoW, driven by higher host acceptance rate in the coffee/lunch categories"). Refreshed weekly, not real-time, to keep the investor-facing number stable and explainable rather than twitchy.

## Data Freshness & Refresh Cadence

Dashboard refreshes daily from the warehouse's batch-ingested tables (per [data-warehouse-architecture.md](data-warehouse-architecture.md), a 5-15 minute ingestion lag is acceptable since nothing here requires real-time). Investor view snapshots and freezes every Monday 00:00 IST for use in the weekly investor update, per [../35-investor-relations/README.md](../35-investor-relations/README.md).

## Related Documents

- [data-warehouse-architecture.md](data-warehouse-architecture.md) — data source
- [analytics-event-taxonomy.md](analytics-event-taxonomy.md) — underlying events
- [ab-testing-platform-spec.md](ab-testing-platform-spec.md) — shared guardrail definitions
- [../00-foundation/](../00-foundation/README.md) — mission and principles this metric operationalizes
- [../35-investor-relations/README.md](../35-investor-relations/README.md) — consumer of the investor view
