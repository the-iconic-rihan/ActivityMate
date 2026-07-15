---
title: "Release Management Process"
folder: 27-cicd-release
purpose: "Release process"
pages_estimate: 3
prerequisites: "branching-strategy"
dependencies: "none"
priority: P0
audience: "DO"
status: draft-v1
generated: 2026-07-15
---

# Release Management Process

## Overview

This document defines how a set of merged changes on `main` becomes a deployed production release: cadence, versioning, the changelog process, and — critically for a trust-and-safety product — the concrete criteria that trigger a rollback rather than a forward fix.

## Release Cadence

- **Backend (FastAPI monolith):** released to prod up to daily, gated by the staging validation described below — cadence is driven by readiness, not a fixed calendar slot, consistent with the trunk-based model in `branching-strategy.md`. In practice this settles into 2-4 releases/week at MVP team size.
- **Mobile (Flutter):** released weekly at most, constrained by app-store review latency (Google Play review is typically same-day to 2 days; a hard floor on release frequency regardless of engineering readiness). Mobile releases follow `../09-frontend-mobile/ci-mobile-release-pipeline.md` for the store-submission specifics.
- **Infrastructure (Terraform):** no fixed cadence — applied as changes are ready, staging-applied and prod-applied per the review process in `../25-infrastructure-cloud/terraform-iac-standards.md`.

## Versioning

Backend releases are tagged `YYYY.MM.N` (e.g. `2026.07.3` — third release cut in July 2026), not semver — semver's major/minor/patch signaling is designed for consumers of a published API contract; ActivityMate's backend has one consumer (its own mobile client, always updated in step) so calendar+sequence versioning is more informative for on-call and postmortem purposes ("what shipped the week of the incident"). Mobile app versions follow store-mandated semver (`1.4.2`) with an internal build number, per `../09-frontend-mobile/ci-mobile-release-pipeline.md`.

## Release Flow

1. A release branch `release/YYYY.MM.N` is cut from a `main` commit that has been running clean on dev for at least a few hours.
2. CI builds the release candidate image and deploys it to staging (per `../25-infrastructure-cloud/environment-strategy.md`).
3. Automated smoke tests run against staging (auth flow, activity create/join, chat send/receive, trust-score read path — the critical paths defined in `../28-monitoring-observability/sla-slo-definitions.md`).
4. A manual QA pass covers anything smoke tests don't (exploratory testing of the release's specific changes, per `../29-testing-qa/`).
5. On sign-off, the release is promoted: the exact image SHA validated in staging (never a rebuild) is deployed to prod behind the manual approval gate in `cicd-pipeline-architecture.md`. Deploying the identical artifact validated in staging — not rebuilding from the same source — eliminates an entire class of "it was fine in staging" surprises caused by non-reproducible builds.
6. The release is tagged in git, and the changelog entry is finalized.

## Changelog Process

Every PR description includes a one-line changelog entry (enforced by a PR template checklist). On release-branch cut, these entries are aggregated automatically into `CHANGELOG.md`, grouped as **Added / Changed / Fixed / Security**. The changelog is user-facing-feature-oriented, not commit-log-oriented — internal refactors and dependency bumps are omitted unless they have user-visible effect. Security-relevant fixes are listed generically ("improved account-recovery validation") rather than with exploit-identifying detail, to avoid the changelog itself becoming a vulnerability roadmap.

## Rollback Trigger Criteria

A release is rolled back (procedure in `rollback-procedures.md`) — not forward-fixed — when any of the following hold within the first 2 hours post-deploy:

| Trigger | Threshold |
|---|---|
| Error rate | 5xx rate exceeds 2x the 7-day baseline for the affected endpoint, sustained 5+ minutes |
| Latency | p95 latency on a critical path (auth, activity join, chat send) exceeds its SLO in `sla-slo-definitions.md` for 10+ minutes |
| Safety-critical failure | Any failure in the safety check-in flow, trust-score computation, or reporting/blocking system — rolled back immediately regardless of scale of impact, no threshold |
| Data integrity | Any evidence of incorrect writes to Postgres (not just errors — wrong data) |

Below these thresholds, an on-call engineer's judgment call to forward-fix instead of rollback is acceptable and logged in `../39-decision-records-rfcs/decision-log.md`; above them, rollback is not optional.

## Related Documents

- [branching-strategy.md](branching-strategy.md)
- [rollback-procedures.md](rollback-procedures.md)
- [feature-flag-strategy.md](feature-flag-strategy.md)
- [../28-monitoring-observability/sla-slo-definitions.md](../28-monitoring-observability/sla-slo-definitions.md)
- [../09-frontend-mobile/ci-mobile-release-pipeline.md](../09-frontend-mobile/ci-mobile-release-pipeline.md)
