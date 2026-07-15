---
title: "Test Strategy"
folder: 29-testing-qa
purpose: "Test pyramid"
pages_estimate: 5
prerequisites: "backend-services"
dependencies: "none"
priority: P0
audience: "QA, BE"
status: draft-v1
generated: 2026-07-15
---

# Test Strategy

## Overview

ActivityMate is a modular monolith (FastAPI + PostgreSQL + Redis + MongoDB + MinIO + Meilisearch, per [PRD-001](../PRD-001_Master_Product_Requirements.md)) that coordinates strangers meeting offline. That last fact is what separates this test strategy from a typical consumer-app pyramid: most bugs cost a user a bad session, but a bug in verification, check-in, or the panic button can put a user in a physically unsafe situation. This document sets the test pyramid shape and names the flows that are exempt from the "thin e2e" default.

## Context / Problem

A standard pyramid — lots of unit tests, some integration tests, very few end-to-end (e2e) tests — exists because e2e tests are slow, flaky, and expensive to maintain. That tradeoff is correct for most of ActivityMate's surface (profile editing, activity browsing, community feeds). It is not acceptable for the subset of flows where a silent regression is a safety incident rather than an inconvenience. We need a strategy that keeps CI fast for the bulk of the codebase while making it structurally impossible to ship a broken safety flow without an e2e test catching it.

The engineering team is small pre-PMF, so the strategy also has to be cheap to operate — no dedicated QA automation team exists yet; backend and mobile engineers write and own their own tests.

## Options Considered

1. **Uniform thin pyramid everywhere** (5-10% e2e across the board). Rejected — treats a broken "cancel activity" button the same as a broken panic button, which is not true.
2. **Heavy e2e coverage everywhere** ("test everything like it's safety-critical"). Rejected — pre-PMF velocity would collapse, CI runtime would balloon, and flaky e2e suites erode trust in the test suite, causing engineers to ignore failures.
3. **Tiered pyramid: standard shape for most code, mandatory e2e floor for a named safety-critical set.** Selected. Combines fast iteration on the majority of the product with a hard floor of coverage on the flows where regressions are unacceptable.

## Recommendation & Rationale

**Standard-tier pyramid** (applies to all services and screens not on the safety-critical list):
- **70% unit tests** — pure functions, business logic (trust score calculation, matching filters, pricing), serializers/validators. Run on every commit, target under 3 minutes for the full suite.
- **20% integration tests** — API endpoint tests against a real PostgreSQL/Redis/MongoDB test instance (via Docker Compose, see [26-docker-containers/local-dev-environment-setup.md](../26-docker-containers/local-dev-environment-setup.md)), covering request validation, auth middleware, and DB transaction correctness. Run on every PR.
- **10% e2e tests** — Flutter integration_test driving critical user journeys (signup, browse, create activity, join activity) against a staged backend. Run on merge to main and nightly, not on every commit, because of runtime cost.

**Safety-critical tier — mandatory e2e regardless of pyramid shape.** The following flows must have e2e coverage that runs on every PR touching the relevant service, cannot be skipped by a flaky-test quarantine process, and blocks merge on failure:
- **Identity verification** (phone OTP, selfie liveness check, government ID upload) — see [12-auth/identity-verification-kyc.md](../12-auth/identity-verification-kyc.md). A silent regression here means an unverified or spoofed identity gets a "verified" badge.
- **Activity check-in** — the flow confirming a participant has actually arrived at a meetup. A broken check-in silently breaks the safety net that depends on knowing who is where.
- **Panic button / emergency flow** — see [13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md). This is the single highest-consequence flow in the product; it gets e2e coverage on every environment (staging and pre-prod) and a synthetic monitoring check in production (see [28-monitoring-observability/sla-slo-definitions.md](../28-monitoring-observability/sla-slo-definitions.md)) in addition to CI tests.
- **Reporting and blocking** — see [13-trust-safety-fraud/reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md). A user must always be able to report or block, including under partial backend degradation.

These four flows are additionally covered by manual exploratory testing before each release (see [qa-process-bug-lifecycle.md](qa-process-bug-lifecycle.md)) and are the first item checked in the release go/no-go review (see [27-cicd-release/release-management-process.md](../27-cicd-release/release-management-process.md)).

**Ownership.** Backend engineers write unit and integration tests for their own services as part of the PR (enforced by CI gating, see [automated-testing-standards.md](automated-testing-standards.md)). The QA Lead owns the safety-critical e2e suite and the manual test plan, and has merge-block authority over the four flows above regardless of who authored the change.

**Non-goals for MVP.** Visual regression testing, cross-browser testing (mobile-only product), and mutation testing are deferred until post-PMF — they improve confidence but are not load-bearing for launch.

## Open Questions

- Target code-coverage percentage for the unit tier has not been set numerically; recommend deferring a hard number until the codebase stabilizes post-MVP rather than gaming a threshold early.
- Whether the panic-button e2e suite should also run against production with synthetic (non-real) test accounts on a schedule — pending infra cost review with DevOps.

## Related Documents

- [qa-process-bug-lifecycle.md](qa-process-bug-lifecycle.md)
- [automated-testing-standards.md](automated-testing-standards.md)
- [test-environment-data-strategy.md](test-environment-data-strategy.md)
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md)
- [../12-auth/identity-verification-kyc.md](../12-auth/identity-verification-kyc.md)
