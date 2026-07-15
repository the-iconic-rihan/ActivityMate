---
title: "Alerting Oncall Policy"
folder: 28-monitoring-observability
purpose: "Alerting & on-call"
pages_estimate: 4
prerequisites: "observability-stack-evaluation"
dependencies: "incident-disaster-recovery"
priority: P0
audience: "DO"
status: draft-v1
generated: 2026-07-15
---

# Alerting & On-Call Policy

## Overview

This document defines alert severity tiers, routing, and the on-call rotation for ActivityMate. Its central design decision, stated in the folder README and repeated here deliberately: **safety-incident alerts share the highest severity tier with infrastructure outages.** A silent failure in the safety check-in flow gets the same 24/7, page-immediately treatment as the database going down — not a lower tier because "nothing is technically broken."

## Severity Tiers

**SEV1 — Page immediately, 24/7, any hour.**
- Full service outage (API unreachable, database unreachable).
- Safety check-in flow failing for any user (silent failure to notify an emergency contact, or failure to record a check-in at all).
- Trust-score computation producing incorrect scores at scale (not a single edge case — a systemic miscalculation).
- Reporting/blocking system failing to process a report or execute a block.
- Authentication fully down (no one can log in).
- Data-integrity issue — confirmed incorrect writes to Postgres affecting user-facing data.

**SEV2 — Page during business hours (9am-9pm IST), else queued for first-thing-next-day, unless it's trending toward SEV1.**
- Elevated error rate on a non-safety-critical path (e.g., activity search degraded but not down).
- Latency SLO breach on a non-critical-path endpoint (per `sla-slo-definitions.md`).
- A single third-party integration degraded (SMS/OTP provider slow, not fully down) with a working fallback.
- Chat delivery delayed (not failed) beyond SLO.

**SEV3 — Ticketed, no page, addressed within 2 business days.**
- Elevated warning-log volume with no user-facing symptom yet.
- Non-critical background job (e.g., a nightly trust-score recompute batch) failing with a retry path still succeeding.
- Cost-anomaly alerts from `../25-infrastructure-cloud/cost-optimization-playbook.md` that aren't urgent.

**SEV4 — Informational, reviewed in weekly ops review, no individual action required.**
- Capacity trending toward a threshold (e.g., RDS storage at 70%) with weeks of runway left.

## Why Safety and Infrastructure Share SEV1

This is the policy's core reasoning, not just a table entry: an infrastructure outage is visible — users see an error, engineers get paged by the same signal users experience. A silent safety-flow failure is invisible by definition — a user who doesn't get their check-in confirmation, or whose emergency-contact alert doesn't fire, has no error screen telling them something broke. The alert is the *only* signal anyone gets. Treating that alert as lower-severity than an outage would be backwards: the safety-flow failure is arguably higher actual risk to a real person, even though it "looks" quieter in a dashboard. SEV1 tier assignment reflects risk to users, not visibility of the symptom.

## On-Call Rotation

- **Rotation:** weekly, single primary on-call engineer plus one secondary (escalation backup), rotating across the DevOps/backend engineers capable of handling a production incident. At MVP team size (2-4 relevant engineers), this means each engineer is on-call roughly one week in every 2-4 — reviewed and adjusted as the team grows, since a rotation that thin is a burnout risk worth watching explicitly, not silently accepting as permanent.
- **Escalation path:** primary on-call has 10 minutes to acknowledge a SEV1 page before it auto-escalates to secondary; secondary has a further 10 minutes before escalating to the DevOps lead directly.
- **Tooling:** Datadog Monitors → PagerDuty-compatible on-call routing (or equivalent, integrated with the Datadog stack chosen in `observability-stack-evaluation.md`) → phone call + push notification for SEV1, push notification only for SEV2, ticket creation only for SEV3/SEV4.
- **Handoff:** each rotation handoff includes a 15-minute sync covering any open SEV1/SEV2 issues, any flag ramps in progress (per `../27-cicd-release/feature-flag-strategy.md`) that might be relevant, and any known fragile areas from the past week.

## Alert Quality Bar

An alert that fires without a clear, documented next action is a bug in the alerting config, not a tolerable inconvenience — alert fatigue is a direct threat to the SEV1 safety-tier promise above, since an on-call engineer who's learned to distrust pages from noise will be slower on the page that matters. Every SEV1/SEV2 alert links to a runbook (starting with `../27-cicd-release/rollback-procedures.md` for deploy-related triggers, or the relevant playbook in `../40-incident-disaster-recovery/`) rather than leaving the responder to improvise a first response.

## Incident Escalation

Any SEV1 alert automatically opens an incident per `../40-incident-disaster-recovery/incident-management-process.md`; safety-specific SEV1s additionally trigger the escalation path in `../40-incident-disaster-recovery/safety-incident-escalation.md`, which brings in Trust & Safety leadership alongside DevOps.

## Related Documents

- [observability-stack-evaluation.md](observability-stack-evaluation.md)
- [sla-slo-definitions.md](sla-slo-definitions.md)
- [../27-cicd-release/rollback-procedures.md](../27-cicd-release/rollback-procedures.md)
- [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md)
- [../40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md)
