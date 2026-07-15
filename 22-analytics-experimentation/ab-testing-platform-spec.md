---
title: "Ab Testing Platform Spec"
folder: 22-analytics-experimentation
purpose: "Experiment platform"
pages_estimate: 4
prerequisites: "analytics-event-taxonomy"
dependencies: "product"
priority: P1
audience: "BE, PM"
status: draft-v1
generated: 2026-07-15
---

# A/B Testing Platform Spec

## Overview

ActivityMate needs to run controlled experiments early — on onboarding flow, verification friction, matching surfacing, and notification cadence — without each one requiring a bespoke build. This spec defines assignment, exposure logging, and (most importantly for a trust-first product) guardrail-metric auto-abort logic that stops an experiment before it damages safety outcomes, not just growth ones.

## Design Principles

1. **Guardrails outrank the primary metric.** A test that lifts activity-creation rate but degrades female retention or the report-resolution rate is a failed test, full stop, regardless of statistical significance on the primary metric. This is a direct consequence of Core Product Principle #3 in the PRD ("Safety Over Growth").
2. **Assignment is deterministic and sticky.** A user assigned to a variant stays in it for the life of the experiment (no re-randomization on each session) to avoid contaminating funnel metrics.
3. **Server-side assignment, not client-side.** Assignment logic lives in the backend so the same decision is available to push notification content, chat behavior, and the Flutter client consistently, and so a user cannot flip variants by clearing app state.

## Assignment Model

- **Unit of randomization:** `user_id` by default. Activity-level or city-level randomization is supported as a variant for tests where user-level assignment would leak between participants of the same activity (e.g., testing a new join-request UI where host and joiner interact).
- **Bucketing:** deterministic hash of `(user_id, experiment_key)` mapped to a 0-9999 range, sliced into variant allocations (e.g., 5000-9999 control, 0-4999 treatment for a 50/50 split). This guarantees the same user always lands in the same bucket for a given experiment without a persisted assignment table lookup on every request — though assignments are still persisted for audit and for the `experiment_keys` property on every event (per [analytics-event-taxonomy.md](analytics-event-taxonomy.md)).
- **Eligibility filters:** experiments can be scoped by city (`mumbai` only at launch — the field exists for future cities), verification level, app version (to exclude users on versions predating a feature), or account age (to exclude very new signups from onboarding tests that need a settled baseline).
- **Mutual exclusion groups:** experiments touching the same surface (e.g., two onboarding-flow tests) are placed in a mutual-exclusion layer so a user is never in two conflicting tests simultaneously.

## Exposure Logging

An `experiment_exposed` event fires the first time a user actually encounters the varied surface (not at assignment time) — this distinction matters because assignment can happen at signup while exposure to, say, a chat-nudge variant only happens days later when the user opens a chat thread. Analyzing on exposure rather than assignment avoids diluting effect sizes with users who were assigned but never actually saw the treatment.

## Guardrail Metrics & Auto-Abort

Every experiment must declare, at creation time, one primary metric and at least these standing guardrails (pulled automatically from the north-star dashboard's component metrics, per [north-star-kpi-dashboard-spec.md](north-star-kpi-dashboard-spec.md)):

| Guardrail | Trigger threshold | Action |
|---|---|---|
| Female retention (7-day) | Treatment drops >3pp below control, p<0.1 | Auto-pause, notify PM + Trust & Safety lead |
| Report rate per completed activity | Treatment exceeds control by >20% relative, p<0.1 | Auto-pause, notify Trust & Safety lead immediately (page, not just Slack) |
| No-show rate | Treatment exceeds control by >15% relative | Auto-pause, notify PM |
| Trust score distribution (median) | Treatment median trust score drops meaningfully vs. control | Flag for manual review, does not auto-pause (slower-moving metric, prone to noise at low sample) |

Auto-abort logic runs as a scheduled job against the warehouse (daily, per [data-warehouse-architecture.md](data-warehouse-architecture.md) ingestion cadence — this is why sub-minute freshness is not required) using a sequential-testing-safe check (not naive repeated significance testing, which inflates false-positive rates). On breach, the experiment is flipped to 100% control automatically and a `experiment_auto_aborted` event/alert fires to the owning PM and the Trust & Safety on-call.

## Statistical Approach

- Minimum detectable effect and required sample size calculated per experiment before launch, using Mumbai's expected DAU ramp as the sizing input — this is explicitly conservative given known low initial user density (a named risk in PRD-001 §11).
- Two-sided tests by default; one-sided only with explicit PM sign-off and written rationale.
- No peeking-driven early stops on the primary metric — only guardrail breaches justify early termination, to keep primary-metric conclusions valid.

## Experiment Lifecycle

1. **Proposal** — PM files experiment brief: hypothesis, primary metric, guardrails, minimum runtime, target sample size.
2. **Review** — Data/Growth reviews sizing math; Trust & Safety reviews any experiment touching matching, verification, or chat surfaces.
3. **Launch** — flagged in the assignment service; exposure logging verified in a staging city-scoped test before go-live.
4. **Monitor** — guardrail job runs daily; dashboard tile shows live status.
5. **Readout** — results doc with effect size, confidence interval, guardrail status, and a ship/hold/kill recommendation.
6. **Ramp or rollback** — winning variants ramp via the same assignment service (no separate feature-flag rebuild needed).

## Related Documents

- [analytics-event-taxonomy.md](analytics-event-taxonomy.md) — `experiment_keys` property and `experiment_exposed` event
- [data-warehouse-architecture.md](data-warehouse-architecture.md) — where exposure and outcome data is analyzed
- [north-star-kpi-dashboard-spec.md](north-star-kpi-dashboard-spec.md) — source of standing guardrail metrics
- [../13-trust-safety-fraud/](../13-trust-safety-fraud/README.md) — trust score and report-rate definitions used as guardrails
- [../04-product/](../04-product/README.md) — product prioritization this platform serves
