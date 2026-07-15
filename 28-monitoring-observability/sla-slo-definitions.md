---
title: "Sla Slo Definitions"
folder: 28-monitoring-observability
purpose: "SLOs"
pages_estimate: 3
prerequisites: "observability-stack-evaluation"
dependencies: "none"
priority: P0
audience: "DO, BE"
status: draft-v1
generated: 2026-07-15
---

# SLA / SLO Definitions

## Overview

This document sets Service Level Objectives for ActivityMate's critical paths — the specific flows where a slowdown or failure directly affects a user's trust in the platform or their physical safety. These SLOs are the quantitative backbone behind the rollback triggers in `../27-cicd-release/release-management-process.md` and the alert thresholds in `alerting-oncall-policy.md` — an alert fires because an SLO defined here is at risk, not on an arbitrary threshold invented separately.

## Why These Three Paths

Of everything the platform does, three flows are singled out for formal SLOs because degradation in any of them has consequences beyond "annoying": **authentication** (a user locked out can't reach help or safety features when they need them), **safety check-in** (the core promise behind the product principle "Safety Over Growth" in PRD-001 — a check-in that silently fails defeats the entire point of the feature), and **chat delivery** (coordination for an in-person meetup depends on messages arriving; a delayed message can mean two people failing to find each other at night, which is itself a safety-adjacent failure for this product).

## SLO Definitions

### Authentication
- **Availability:** 99.9% of login/OTP-verify requests succeed (excluding user error, e.g. wrong OTP entered), measured monthly. 99.9% allows roughly 43 minutes of downtime per month — appropriate for a single-region MVP without the cost of full active-active redundancy.
- **Latency:** p95 end-to-end OTP-verify-to-session-token under 1.5 seconds; p99 under 3 seconds. OTP send (to SMS provider) p95 under 5 seconds (bounded partly by the third-party provider, tracked separately from our own latency).
- **Error budget policy:** if the monthly error budget (0.1%) is exhausted before month-end, no non-essential authentication-touching changes deploy to prod until the next budget window opens, per the same discipline as Google's SRE error-budget model — protects the path most directly gating access to the rest of the app, including safety features.

### Safety Check-In
- **Availability:** 99.95% of check-in submissions are recorded successfully — a tighter target than general authentication, reflecting that this path has no acceptable "try again later" for a user who is, in the moment, relying on it. 99.95% allows roughly 22 minutes of downtime per month.
- **Latency:** p95 check-in submission acknowledged under 1 second — a user confirming they're safe (or that they need help) needs fast confirmation that the action registered.
- **Correctness (in addition to availability/latency):** zero tolerance for a check-in being recorded as "completed" when it wasn't actually submitted, or an emergency-contact alert failing to fire when triggered — these are SEV1 per `alerting-oncall-policy.md` regardless of how small the affected population is, since correctness failures here are evaluated per-incident, not as an aggregate percentage.

### Chat Delivery
- **Availability:** 99.9% of messages sent are delivered to the recipient's client (or queued for delivery on reconnect, per the offline-first mobile design in `../09-frontend-mobile/offline-first-sync-strategy.md`) within the latency target below.
- **Latency:** p95 message delivery (sender send-tap to recipient client receipt, when recipient is online) under 2 seconds; p99 under 5 seconds.
- **Note on scope:** this SLO covers delivery of the message to a connected client, not read-receipt or notification-push latency, which are tracked separately as part of `../16-realtime-chat-notifications/`.

## Measurement

All three SLOs are measured from server-side metrics emitted at the relevant API boundary (per `observability-stack-evaluation.md`'s APM/metrics setup) — not client-reported metrics, which are useful for a separate client-perceived-performance view but too noisy (device variance, network variance) to be the basis for an SLO an on-call engineer is paged against. Measurement windows are rolling 30-day for availability, rolling 7-day for latency percentiles (shorter window for latency so a real regression is visible fast, not diluted across a month).

## SLA vs SLO

These are internal SLOs, not externally published SLAs — ActivityMate does not currently offer contractual uptime guarantees to end users or partners. If a future B2B community-partner agreement requires a formal SLA, it is derived from these SLOs with appropriate margin, not defined independently.

## Related Documents

- [observability-stack-evaluation.md](observability-stack-evaluation.md)
- [alerting-oncall-policy.md](alerting-oncall-policy.md)
- [dashboards-spec.md](dashboards-spec.md)
- [../27-cicd-release/release-management-process.md](../27-cicd-release/release-management-process.md)
- [../27-cicd-release/rollback-procedures.md](../27-cicd-release/rollback-procedures.md)
- [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md)
