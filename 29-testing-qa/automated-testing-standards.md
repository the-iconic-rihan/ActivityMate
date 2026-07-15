---
title: "Automated Testing Standards"
folder: 29-testing-qa
purpose: "CI test gating"
pages_estimate: 3
prerequisites: "test-strategy"
dependencies: "cicd-release"
priority: P0
audience: "BE, QA"
status: draft-v1
generated: 2026-07-15
---

# Automated Testing Standards

## Overview

This document sets the CI gates that a pull request must pass before it can merge, translating the pyramid ratios in [test-strategy.md](test-strategy.md) into enforceable pipeline rules inside [27-cicd-release/cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md).

## Context / Problem

A test strategy that lives only in a document is optional in practice — under deadline pressure, engineers skip tests unless the pipeline physically blocks the merge. At the same time, an overly strict gate (100% coverage required, every test must pass with zero flake tolerance) either gets disabled the first time it blocks an urgent fix, or slows the small pre-PMF team to a crawl. The goal is a gate that is strict where safety is concerned and pragmatic everywhere else.

## Options Considered

1. **No automated gating, tests run but failures are advisory.** Rejected — advisory checks get ignored under deadline pressure, which defeats the purpose of having a test strategy at all.
2. **Uniform hard gate: all tests must pass, no exceptions, no override.** Rejected as sole policy — a single flaky integration test (e.g., due to a third-party sandbox rate limit) would block unrelated work with no escape hatch, encouraging engineers to delete or skip tests instead of fixing flake.
3. **Tiered gating: hard-blocking gate for safety-critical and core suites, soft-fail-with-required-acknowledgment for known-flaky non-critical tests, with an audit trail.** Selected.

## Recommendation & Rationale

**Merge blockers (cannot be overridden by any single engineer):**
- Unit test suite passes at 100%.
- Integration test suite passes at 100% for the service(s) touched by the diff.
- The safety-critical e2e suite (verification, check-in, panic button, report/block — as defined in [test-strategy.md](test-strategy.md)) passes at 100% if the diff touches any file under those service boundaries, as determined by a path-based CI rule mapped from [07-architecture/service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md).
- Static analysis: linting (ruff for FastAPI, `flutter analyze` for the mobile client) and type checking pass with zero errors.
- Dependency vulnerability scan (see [../30-security/secure-coding-standards.md](../30-security/secure-coding-standards.md)) reports no new critical/high CVEs.
- No decrease in unit-test coverage percentage on touched files versus the base branch (ratchet, not an absolute floor, so legacy low-coverage areas don't block unrelated work while preventing new debt).

**Two-person override, logged:** if a non-safety-critical integration or e2e test is confirmed flaky (not a real regression) by both the PR author and a second engineer, the pipeline allows a documented skip via a `flaky-quarantine` label that auto-expires in 7 days and files a tracking ticket. Safety-critical suite failures have no override path — a red safety-critical test always blocks merge, full stop, and can only be resolved by fixing the test or the code.

**Pipeline stages and target runtime:** lint/type-check (under 1 min) → unit tests (under 3 min) → integration tests (under 6 min) → safety-critical e2e (under 10 min, only triggered on relevant paths) → full e2e nightly (not a merge gate). Keeping the always-run gate under ~10 minutes preserves velocity for the small team while the safety gate stays non-negotiable.

**Branch protection:** enforced at the GitHub/GitLab level per [27-cicd-release/branching-strategy.md](../27-cicd-release/branching-strategy.md) — CI status checks are required, and only the QA Lead or CTO can adjust which checks are marked required.

## Open Questions

- Whether to add a mutation-testing gate specifically on trust-score and matching logic post-MVP, to catch tests that pass but don't actually assert behavior.

## Related Documents

- [test-strategy.md](test-strategy.md)
- [../27-cicd-release/cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md)
- [../30-security/secure-coding-standards.md](../30-security/secure-coding-standards.md)
