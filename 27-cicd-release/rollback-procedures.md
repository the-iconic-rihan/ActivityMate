---
title: "Rollback Procedures"
folder: 27-cicd-release
purpose: "Rollback runbook"
pages_estimate: 2
prerequisites: "release-management-process"
dependencies: "monitoring-observability"
priority: P0
audience: "DO"
status: draft-v1
generated: 2026-07-15
---

# Rollback Procedures

## Overview

This is the runbook an on-call engineer follows the moment a rollback trigger from `release-management-process.md` fires. It covers both the automated rollback path and the manual path for cases automation doesn't cover, and it exists specifically so that "roll it back" is a rehearsed, five-minute action rather than a stressful first-time improvisation during an incident.

## Automated Rollback

The deploy pipeline (`cicd-pipeline-architecture.md`) keeps the previously-deployed image SHA available at all times as `<env>-previous` in ECR (per `../26-docker-containers/container-registry-strategy.md`). An automated rollback is triggered in either of two ways:

1. **Health-check-based:** if the post-deploy smoke test (run automatically immediately after a prod deploy completes) fails, the deploy pipeline automatically re-points the target group / ECS service definition at `prod-previous` and aborts — no human action needed for this class of failure, since it's caught before the release ever receives real traffic.
2. **Alert-triggered, human-confirmed:** if a rollback-trigger alert fires (per the thresholds in `release-management-process.md`, wired through `../28-monitoring-observability/alerting-oncall-policy.md`), the on-call engineer runs a single pipeline action (`gh workflow run rollback.yml -f environment=prod`) that redeploys `prod-previous`. This is a one-command action, not a multi-step manual process, specifically so it can be executed correctly under incident stress.

## Manual Rollback (when automation doesn't apply)

Some failure modes aren't a bad application deploy and need a different manual response:

- **Database migration issue:** if the triggering release included an Alembic migration that's now suspected of causing the failure, application rollback alone is insufficient — a forward-only migration philosophy (per `../10-database/migration-strategy.md`) means we do not auto-revert schema changes. Instead: roll back the application code first (stops new bad writes), assess whether the migration itself needs a hand-written down-migration, and involve the DBA-role engineer before touching schema.
- **Feature-flag-related regression:** if the regression traces to a flag rollout rather than the deploy itself (e.g., a percentage ramp exposed a bug at higher traffic), the correct action is flipping the flag off via the flag service — faster than a full deploy rollback and does not require CI. This is the first thing checked when the deploy itself looks clean but a specific feature area regresses.
- **Third-party dependency outage** (SMS/OTP provider, MinIO/S3 issue): not a rollback scenario at all — handled via `../40-incident-disaster-recovery/incident-management-process.md` instead.

## Rollback Runbook Steps (manual path)

1. Confirm the trigger against the thresholds in `release-management-process.md` — avoid rolling back on a false alarm (check the dashboard in `../28-monitoring-observability/dashboards-spec.md` for corroborating signal, not a single alert in isolation).
2. Declare the incident per `../40-incident-disaster-recovery/incident-management-process.md` if user impact is confirmed — rollback and incident declaration happen in parallel, not sequentially.
3. Run `gh workflow run rollback.yml -f environment=prod` (or the staging equivalent for a staging-only issue).
4. Verify the target group / ECS service now reports the previous image SHA as healthy, and that the rollback-trigger metric (error rate, latency, or the specific safety-check failure) returns to baseline within 5 minutes.
5. Post a status update in the incident channel with the rollback confirmation and the reverted release version.
6. Do not re-attempt the failed release until root cause is understood and a fix is validated in staging — a second blind deploy of the same code is not an acceptable next step.
7. File a postmortem using `../40-incident-disaster-recovery/postmortem-template.md` within 2 business days for any rollback triggered by the safety-critical or data-integrity criteria; optional but encouraged for error-rate/latency-triggered rollbacks.

## Rollback Time Target

From trigger confirmation to previous version serving healthy traffic: under 10 minutes for the automated/single-command path, under 30 minutes for the manual database/flag-involved paths. This target is rehearsed via periodic game days rather than assumed.

## Related Documents

- [release-management-process.md](release-management-process.md)
- [cicd-pipeline-architecture.md](cicd-pipeline-architecture.md)
- [../28-monitoring-observability/alerting-oncall-policy.md](../28-monitoring-observability/alerting-oncall-policy.md)
- [../28-monitoring-observability/sla-slo-definitions.md](../28-monitoring-observability/sla-slo-definitions.md)
- [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md)
