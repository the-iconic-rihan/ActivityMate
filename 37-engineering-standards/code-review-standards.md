---
title: "Code Review Standards"
folder: 37-engineering-standards
purpose: "Review standards"
pages_estimate: 3
prerequisites: "none"
dependencies: "none"
priority: P0
audience: "BE, FE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Code Review Standards

**Status:** Draft v1.

## Scope

Review requirements — security- and trust/safety-relevant code paths require a second reviewer with domain context.

## Baseline requirement

Every change to `main` requires at least one approving review from someone other than the author, enforced as a branch-protection rule per [branching-strategy.md](../27-cicd-release/branching-strategy.md). No self-merges, no exceptions, including for the CTO.

## Tiered review requirement

A single reviewer is the floor, not the ceiling. ActivityMate uses a tiered model based on what the change touches, because a generic "one approval" bar is insufficient for code where a bug has safety consequences, not just a broken feature.

### Tier 1 — Standard changes (one reviewer)

Most UI changes, non-critical backend features, copy changes, test additions. One approving review from any engineer familiar with the relevant codebase area is sufficient.

### Tier 2 — Trust & safety-relevant paths (two reviewers, one with domain context)

Requires **one general engineering reviewer plus one reviewer with trust & safety domain context** (a Trust & Safety Operations lead, or an engineer who has previously worked on the trust engine and consults with T&S ops on the change). This tier applies to any change touching:

- Trust score calculation logic ([trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md))
- Verification level gating and the verification pipeline ([user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md))
- Reporting, blocking, or moderation flows ([reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md), [21-moderation-content-ops](../21-moderation-content-ops/README.md))
- Fraud detection rules ([fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md))
- Anything that changes who can see, contact, or join whom (matching/discovery visibility rules)

The domain-context reviewer's job is specifically to ask "how could this be abused or misused by a bad actor" and "does this change what a user was told about how their trust score / report works" (per [trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md)) — a question a purely technical reviewer may not think to ask.

### Tier 3 — Security-critical paths (two reviewers, one from Security)

Requires a reviewer from the Security function (or the Security Lead directly, pre-dedicated-team) in addition to a standard engineering reviewer. Applies to:

- Authentication and session handling ([12-auth](../12-auth/README.md))
- Secrets and key management touchpoints ([secrets-key-management.md](../30-security/secrets-key-management.md))
- Payment flows, once built ([19-payments-subscriptions](../19-payments-subscriptions/README.md))
- Anything altering access-control checks (who can read/write which resource)

Tier 2 and Tier 3 can overlap (e.g. a change to who can view a verification document is both trust-relevant and access-control-relevant) — in that case, both required reviewers apply; the PR is not merged until both approve.

## What a reviewer is checking for

Beyond correctness and style (mostly automated per [coding-style-guides.md](coding-style-guides.md)):
1. **Does this do what the PR description says it does** — reviewers read the diff, not just skim the description.
2. **Is there a test covering the new behavior**, especially the failure/edge case, not just the happy path.
3. **For Tier 2/3 changes**: has the reviewer explicitly considered the abuse case, not just the intended case.
4. **Does this change require an update to a spec document** (e.g. a trust-score formula change should update [trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md) in the same PR or a linked follow-up, not silently drift from documentation).

## Review turnaround expectations

- Tier 1: same-day first response expected.
- Tier 2/3: same-day first response, but the domain-context or security reviewer is allowed to take up to 48 hours given the higher scrutiny required — authors should not pressure a rushed approval on these tiers.

## Escalation

If an author and reviewer disagree and can't resolve it in the PR thread, either party pulls in a third engineer or the relevant lead (CTO for technical disagreements, Head of Trust & Safety for Tier 2 disagreements) rather than the reviewer being pressured into approving, or the author merging over an unresolved objection — which is not permitted regardless of seniority.

## Relationship to other documents

- API-specific review requirements layer on top of this for any endpoint change: [api-design-review-checklist.md](api-design-review-checklist.md)
- Branch and merge mechanics: [git-workflow-conventions.md](git-workflow-conventions.md)
- This document's tiering should be reviewed whenever [service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md) changes, since new domains may need to be added to the Tier 2/3 lists.
