---
title: "Experimentation Framework"
folder: 04-product
purpose: "Experiment policy"
pages_estimate: 4
prerequisites: "feature-prioritization-framework"
dependencies: "analytics-experimentation"
priority: P1
audience: "PM, BE"
status: draft-v1
generated: 2026-07-15
---

# Experimentation Framework

## Overview

Not every change needs an A/B test, and not every change is safe to A/B test. This document sets the line between "ship it," "A/B test it," and "staged rollout only, no test" — and, most importantly for a trust-first product, defines the guardrail metrics that automatically kill an experiment regardless of how good its primary metric looks. This is the operational enforcement of [product-principles.md](product-principles.md) Principle 3: Safety Over Growth, applied at the level of individual experiment configuration rather than left as an abstract value.

## Context / Problem

Growth-stage playbooks default to "test everything" — it's cheap, it's rigorous, it de-risks decisions. But ActivityMate's downside risks are not symmetric with a typical consumer app's: a losing variant in a checkout-flow A/B test costs conversion; a losing variant in an activity-matching or chat-unlock A/B test could put a real person in an unsafe situation with a stranger. An experimentation framework that treats all metrics as equally tradeable would eventually run an experiment that trades safety for growth, discover the regression a week later in the data, and by then an incident has already happened. The guardrail-metric mechanism exists to stop that at the infrastructure level, not rely on a PM noticing in time.

## What Qualifies as an A/B Test vs. a Full Rollout

**A/B test (via the platform in [../22-analytics-experimentation/ab-testing-platform-spec.md](../22-analytics-experimentation/ab-testing-platform-spec.md)):**
- Change has a plausible mechanism to move a primary metric in either direction (i.e., real uncertainty about the outcome).
- Change is reversible without leaving lasting state (e.g., UI copy, discovery ranking weights, notification timing).
- Sufficient traffic exists to reach statistical power in a reasonable window — pre-launch Mumbai volume means many changes will not qualify simply on power grounds, and get staged-rollout treatment instead.
- Does **not** change verification requirements, safety-check-in behavior, or panic-button visibility/placement — those are never A/B tested (see below).

**Full/staged rollout, no test:**
- Any change to trust-score calculation inputs, verification tier requirements, or safety-critical UI (panic button, check-in widget, report flow). These ship to 100% behind a feature flag with staged percentage rollout and manual monitoring, not a randomized experiment — the ethical and safety cost of deliberately withholding a safety feature from a control group is not acceptable, per [product-principles.md](product-principles.md) Principle 3.
- Legal/compliance-driven changes (see [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md)).
- Infrastructure/backend changes with no user-facing behavioral hypothesis (these belong to [../27-cicd-release/README.md](../27-cicd-release/README.md) release process instead).

## Guardrail Metrics That Auto-Abort an Experiment

Every experiment, regardless of what it is primarily testing, is automatically monitored against a fixed guardrail set. Breaching any one triggers automatic rollback to control — no PM approval required, no waiting for the scheduled review:

| Guardrail metric | Abort threshold | Rationale |
|---|---|---|
| Safety-report rate (reports per 1,000 completed activities) | >15% relative increase vs. control, sustained 24h | Direct proxy for user harm; the single most important guardrail in the system |
| Panic-button trigger rate | Any statistically significant increase vs. control | Zero tolerance — even a small absolute increase is treated as a critical signal |
| No-show / cancellation rate | >20% relative increase vs. control | Proxy for eroding trust in commitments, a leading indicator of platform-wide trust decay |
| Female retention (Week 1 and Week 4) | Any statistically significant decrease vs. control | Named PRD-001 success metric; treated as a hard guardrail, not just a KPI to watch |
| Verification completion rate | >10% relative decrease vs. control | Signals a change is quietly weakening the trust gate even if unintentionally |

Guardrail breaches are evaluated by the analytics pipeline defined in [../22-analytics-experimentation/data-warehouse-architecture.md](../22-analytics-experimentation/data-warehouse-architecture.md) on a rolling basis, not only at experiment end — the entire point is to catch a regression mid-flight before it accumulates exposure.

## Experiment Governance

- Every experiment must be registered with a hypothesis, primary metric, and pre-declared guardrail set before launch — post-hoc guardrail selection is not permitted, since it invites cherry-picking whichever guardrail didn't breach.
- Experiments touching anything the [prd-template.md](prd-template.md) Trust & Safety Review checkbox flags require Head of Trust & Safety sign-off before launch, not just PM sign-off, even if technically structured as an A/B test rather than a full rollout.
- Minimum sample size and run duration are set per [feature-prioritization-framework.md](feature-prioritization-framework.md)'s Confidence input — an underpowered "test" that gets shipped on vibes because it "looked positive" is treated as a process failure, logged in [../39-decision-records-rfcs/decision-log.md](../39-decision-records-rfcs/decision-log.md).

## Risks & Open Questions

- Pre-launch Mumbai traffic volumes may be too low to reach statistical power on most experiments for the first several months, meaning the "full/staged rollout" path will be the default in practice — worth revisiting this framework's balance once volume grows.
- The guardrail thresholds above (15%, 20%, 10%) are initial estimates, not yet validated against real variance in ActivityMate's own data; they should be recalibrated after the first two quarters of live experimentation.
- Need a defined escalation path for the case where an experiment auto-aborts but the team believes the guardrail breach was a false positive (e.g., a marketing campaign coincidentally spiking report rate in both arms).

## Related Documents

- [feature-prioritization-framework.md](feature-prioritization-framework.md)
- [product-principles.md](product-principles.md)
- [../22-analytics-experimentation/ab-testing-platform-spec.md](../22-analytics-experimentation/ab-testing-platform-spec.md)
- [../22-analytics-experimentation/north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md)
