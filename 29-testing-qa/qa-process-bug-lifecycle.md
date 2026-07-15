---
title: "Qa Process Bug Lifecycle"
folder: 29-testing-qa
purpose: "Bug lifecycle"
pages_estimate: 3
prerequisites: "test-strategy"
dependencies: "none"
priority: P0
audience: "QA"
status: draft-v1
generated: 2026-07-15
---

# QA Process & Bug Lifecycle

## Overview

This document defines how bugs are found, triaged, prioritized, and closed at ActivityMate, and — the reason this folder is P0 rather than the P2 many early-stage startups assign to QA process — how safety-relevant bugs are routed through an expedited path that bypasses the normal sprint-planning queue.

## Context / Problem

A generic severity-1/2/3/4 triage scheme, sorted purely by user-facing impact and business cost, works for most SaaS products. It fails ActivityMate in one specific way: a bug that lets an unverified user appear as "verified," or that fails to surface a report to a moderator, is not merely high-impact in the usual sense (revenue, churn) — it is a live safety gap while it remains open, independent of how many users have hit it yet. Standard severity models rate by *observed* impact; we need a model that also rates by *safety class*, which can override a low observed-impact score.

## Options Considered

1. **Single severity axis (Sev1-4) scored by user/business impact only.** Rejected as sole scheme — it would let a low-traffic but safety-relevant bug (e.g., a report button that silently no-ops for 2% of users) sit in the backlog behind higher-traffic cosmetic bugs.
2. **Two-axis triage: Severity (impact/scope) × Safety Flag (yes/no).** Selected. Keeps the familiar severity ladder for prioritization within a class, while a safety flag forces a bug into the expedited lane regardless of its severity score.

## Recommendation & Rationale

### Severity levels

| Severity | Definition | Example | Standard SLA |
|---|---|---|---|
| Sev-1 (Critical) | Platform down, data loss, or a safety-critical flow (check-in, panic button, verification, report/block) is broken for any user segment | Panic button does not notify anyone | Fix or mitigate within 4 hours, any day, any time |
| Sev-2 (High) | Core flow broken for a meaningful user segment, no safe workaround | Activity creation fails for Android users on a specific OS version | Fix within 1 business day |
| Sev-3 (Medium) | Degraded experience with a workaround | Profile photo upload occasionally requires a retry | Fix within current or next sprint |
| Sev-4 (Low) | Cosmetic, low-traffic, or edge-case | Minor layout shift on a rare screen size | Backlog, scheduled opportunistically |

### Safety flag

Any bug is tagged `safety-relevant` if it touches: identity verification, trust score calculation or display, check-in/check-out, panic button, reporting/blocking, chat abuse detection, or location precision/fuzzing (see [17-maps-location/location-privacy-strategy.md](../17-maps-location/location-privacy-strategy.md)). A `safety-relevant` tag does two things regardless of the severity score:

- It is automatically triaged at minimum Sev-2, even if the observed user impact looks small, on the reasoning that safety bugs are undercounted by traffic (most users never need the panic button, which is exactly why it must always work).
- It pages the on-call engineer and the Head of Trust & Safety (not just the assigned engineer's team lead), following the escalation path in [40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md).

### Lifecycle

`New → Triaged (severity + safety flag assigned) → In Progress → Fixed (PR merged) → Verified (QA confirms in staging) → Closed (confirmed in production)`. A `Reopened` state returns a bug to `In Progress` if verification fails. Safety-relevant bugs additionally require a second reviewer sign-off (not the fixing engineer) before `Verified`, mirroring the two-person review pattern used for trust-score logic changes in [13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md).

### Triage cadence

Standard bugs are triaged in a daily 15-minute async standup (Slack thread, not a meeting) by the QA Lead. Safety-relevant bugs skip the queue entirely — they are triaged the moment they are filed, by whoever is on-call, per [qa-process-bug-lifecycle.md](qa-process-bug-lifecycle.md) itself and [28-monitoring-observability/alerting-oncall-policy.md](../28-monitoring-observability/alerting-oncall-policy.md).

## Related Documents

- [test-strategy.md](test-strategy.md)
- [../40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md)
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)
