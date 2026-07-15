---
title: "Branching Strategy"
folder: 27-cicd-release
purpose: "Branching model"
pages_estimate: 3
prerequisites: "none"
dependencies: "none"
priority: P0
audience: "BE, FE, DO"
status: draft-v1
generated: 2026-07-15
---

# Branching Strategy

## Overview

This document sets ActivityMate's git branching model. It applies to the whole monorepo — backend, Flutter mobile, and infrastructure — and every engineer is expected to follow it without exception, since the CI/CD pipeline (`cicd-pipeline-architecture.md`) and release process (`release-management-process.md`) are both built around its assumptions.

## Context / Problem

A small, fast-iterating team building toward an activity-first social product needs to merge frequently — long-lived branches are where merge conflicts, integration surprises, and stale reviews accumulate. At the same time, a trust-and-safety product cannot ship half-finished, unreviewed features to real users. The branching model has to resolve that tension.

## Options Considered

### Trunk-Based Development with Feature Flags
- **Pros:** All work merges to `main` frequently (daily or near-daily), keeping integration pain small and constant rather than large and periodic. Every merged PR is deployable — incomplete features stay dark behind a flag (see `feature-flag-strategy.md`) rather than living on a divergent branch. Matches a small team's actual working style: 2-6 engineers touching overlapping parts of a modular monolith benefit far more from continuous integration than from isolation. CI/CD pipelines are simpler — one long-lived branch to build, test, and deploy from.
- **Cons:** Requires real flag discipline — a team that merges incomplete work behind flags but never cleans the flags up accumulates dead conditional branches in the codebase (mitigated by the flag lifecycle in `feature-flag-strategy.md`). Riskier without decent test coverage, since `main` is always one merge away from being deployed.

### GitFlow
- **Pros:** Clear separation between in-progress work (`develop`), release candidates (`release/*`), and production (`main`) — safer for teams that release infrequently (monthly or slower) and want a long-lived integration branch to stabilize before a release.
- **Cons:** Adds branch-management overhead (develop, release, hotfix, feature branches, each with merge rules) disproportionate to a small team's velocity needs. Long-lived `develop` branch reintroduces the large, infrequent-integration pain trunk-based development avoids. Optimized for a release cadence slower than what ActivityMate needs pre-PMF, when the team should be shipping and learning multiple times a week.

## Recommendation & Rationale

**Trunk-based development with feature flags.** The team's stage — pre-PMF, small, needing fast learning loops — makes continuous integration and frequent small merges more valuable than GitFlow's release-branch safety net. The safety GitFlow buys is instead bought by feature flags (incomplete work stays dark) and by the staging gate in `release-management-process.md` (nothing reaches prod without a smoke-tested staging pass).

## The Model, Concretely

- **`main` is the only long-lived branch.** It is always deployable to dev automatically (per `../25-infrastructure-cloud/environment-strategy.md`) and is the source for every release cut.
- **All work happens on short-lived branches off `main`**, named `<type>/<ticket-id>-<short-description>`:
  - `feat/AM-142-trust-score-decay`
  - `fix/AM-201-otp-retry-race`
  - `chore/AM-088-bump-fastapi`
  - `infra/AM-310-add-elasticache-replica`
- **Branch lifetime target: under 2 days.** A branch open longer than that is either too large (split it) or blocked (flag it in standup) — long-lived feature branches are exactly the failure mode trunk-based development exists to prevent.
- **Merges to `main` are squash-merged**, producing one clean commit per PR on `main`'s history, with the PR number in the commit message for traceability (feeds `../37-engineering-standards/git-workflow-conventions.md`).
- **No direct commits to `main`.** All changes go through a PR with at least one approval (`../37-engineering-standards/code-review-standards.md`) and a green CI run.
- **Release branches are cut, not developed on.** `release/2026.07.3` is created from a `main` commit purely to freeze a candidate for staging validation (per `release-management-process.md`); no new feature work happens on it — fixes found during staging validation are cherry-picked from a fix merged to `main` first.

## Feature Flag Discipline (summary — full lifecycle in feature-flag-strategy.md)

Any change that is user-visible but not ready for all users — a new activity category, a redesigned trust-score display, a matching-algorithm change — merges to `main` behind a flag, defaulted off in prod. This is what makes "merge to `main` daily" compatible with "don't ship half-built features to real users."

## Related Documents

- [feature-flag-strategy.md](feature-flag-strategy.md)
- [cicd-pipeline-architecture.md](cicd-pipeline-architecture.md)
- [release-management-process.md](release-management-process.md)
- [../37-engineering-standards/git-workflow-conventions.md](../37-engineering-standards/git-workflow-conventions.md)
- [../37-engineering-standards/code-review-standards.md](../37-engineering-standards/code-review-standards.md)
