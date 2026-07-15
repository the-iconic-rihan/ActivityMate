---
title: "Git Workflow Conventions"
folder: 37-engineering-standards
purpose: "Git workflow"
pages_estimate: 2
prerequisites: "cicd-release"
dependencies: "none"
priority: P0
audience: "BE, FE"
status: draft-v1
generated: 2026-07-15
---

# Git Workflow Conventions

**Status:** Draft v1.

## Scope

Branch naming and commit conventions, tied to the trunk-based strategy in cicd-release.

## Branching model

ActivityMate uses **trunk-based development**, per [branching-strategy.md](../27-cicd-release/branching-strategy.md): `main` is always deployable, feature branches are short-lived (target: merged within 1–3 days), and long-lived feature branches are avoided in favor of feature flags for incomplete work in production (see [feature-flag-strategy.md](../27-cicd-release/feature-flag-strategy.md)). This document covers the naming and commit conventions that make that model workable day to day; the release mechanics themselves live in the cicd-release folder.

## Branch naming

```
<type>/<short-description>
```

Types: `feat`, `fix`, `chore`, `refactor`, `docs`, `spike`, `hotfix`

Examples:
- `feat/activity-creation-flow`
- `fix/trust-score-recalc-race-condition`
- `hotfix/verification-webhook-timeout`
- `chore/upgrade-fastapi-0.115`

Ticket/issue references, where a tracker is in use, are appended: `feat/PROJ-482-activity-creation-flow`. Branch names are lowercase, hyphen-separated, no personal name prefixes (`feat/rahul-activity-flow`) — the branch describes the change, not the author (Git already tracks that).

## Commit message convention

ActivityMate follows **Conventional Commits**:

```
<type>(<scope>): <short summary, imperative mood, no trailing period>

<optional body: the "why", not a restatement of the diff>

<optional footer: BREAKING CHANGE, Refs #123>
```

Types: `feat`, `fix`, `refactor`, `chore`, `docs`, `test`, `perf`, `revert`

Scope is typically the domain module (`activities`, `trust`, `chat`, `auth`, `mobile`) matching the structure in [coding-style-guides.md](coding-style-guides.md).

Examples:

```
feat(trust): add cancellation-rate decay to trust score

Cancellation rate previously never decayed, so a single bad month from
18 months ago still dragged score today. Applies a 90-day rolling window
per the formula in trust-engine-architecture.md.
```

```
fix(chat): prevent duplicate message delivery on reconnect

Client was replaying the last unacked message on WebSocket reconnect,
causing dupes. Server now dedupes on client-supplied message UUID.

Refs #241
```

A commit message body is required (not just the one-line summary) for any commit touching Tier 2 or Tier 3 code paths per [code-review-standards.md](code-review-standards.md) — the reasoning matters as much as the diff for anything trust- or security-relevant, and future engineers (including the author, six months later) need the "why" preserved in history, not just discoverable in a closed PR thread.

## Pull request conventions

- PR titles mirror the primary commit's Conventional Commits format.
- PR description template: **What** changed, **Why**, **How to test**, and — for Tier 2/3 changes — **Abuse case considered**, a mandatory field naming at least one way the change could be misused and why it's mitigated.
- PRs are squash-merged to `main` by default, keeping `main` history one commit per logical change; the PR itself retains the full review discussion.

## Tags and releases

Release tags follow `vMAJOR.MINOR.PATCH` semantic versioning, cut from `main` per [release-management-process.md](../27-cicd-release/release-management-process.md). Hotfix branches (`hotfix/*`) are the only branches permitted to be cut from a release tag rather than current `main`, and must be merged back to `main` immediately after deploy.

## Maintenance

Owned by the CTO. Any change to branch/commit conventions is proposed as a short PR to this file and communicated in an engineering all-hands before enforcement (e.g. via a commit-msg hook) begins, so nobody is silently blocked by a new rule.
