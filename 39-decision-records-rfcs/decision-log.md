---
title: "Decision Log"
folder: 39-decision-records-rfcs
purpose: "Decision log"
pages_estimate: 2
prerequisites: "none"
dependencies: "none"
priority: P1
audience: "BE, CTO"
status: draft-v1
generated: 2026-07-15
---

# Decision Log

## Overview

The decision log is a lighter-weight record for decisions that are worth writing down but don't clear the bar for a full [ADR](adr-template.md) or [RFC](rfc-process.md). It exists so that small but non-obvious calls — a library choice, a naming convention, a config default — aren't lost the moment the Slack thread that produced them scrolls out of view.

## Context / Problem

Not every decision crosses a module boundary or touches a trust/safety-critical path — the [RFC process](rfc-process.md) deliberately keeps those out of the heavyweight ADR pipeline to protect team velocity. But "doesn't need an RFC" is not the same as "doesn't need a record." Six months in, an engineer asking "why do we paginate with cursor tokens instead of offset" or "why is the Meilisearch index rebuilt nightly instead of on write" deserves a one-line answer, not an archaeology project through commit history.

## Options Considered

**Option A — No log; small decisions live only in PR descriptions and Slack.**
Pros: no additional artifact to maintain.
Cons: PR descriptions are hard to search across; Slack history has practical retention/searchability limits; the same question gets re-litigated repeatedly because nobody can find the answer.

**Option B — Every decision, however small, goes through the full ADR template.**
Pros: maximum consistency.
Cons: wildly disproportionate overhead — the ADR template's Options Considered / Consequences / Revisit Trigger structure is overkill for "we're using snake_case for API field names."

**Option C — A single running append-only log, one line per decision, no template ceremony, with a pointer to a full ADR for anything that later turns out to matter more than expected.**
Pros: near-zero friction to add an entry (encourages actually logging things); still searchable and dated; explicitly allows promotion to a full ADR if a "small" decision turns out to have bigger consequences than assumed.
Cons: less structured, so it's not a substitute for ADR-level reasoning on anything that actually needs it — this is a feature, not a bug, since it keeps the two artifacts distinct.

## Recommendation & Rationale

**Option C.** A low-friction append-only log maximizes the chance small decisions actually get recorded, which is the whole point — a perfectly structured log that nobody uses is worse than a messy one that's actually maintained.

## The Actual Process

1. **Format**: one row per decision in a single table (below), newest at the top. No separate file per decision.
2. **Fields**: Date | Decision | Reasoning (1-2 sentences) | Decided by | Module | Promoted to ADR? (link if yes).
3. **Who logs**: any engineer, at the time the decision is made — added directly in the PR that implements it, or immediately after a Slack/meeting discussion settles it.
4. **Promotion rule**: if a logged decision later proves to have cross-module impact, or gets revisited more than once, it should be promoted to a full [ADR](adr-template.md) rather than left in this lighter log — the log entry stays, updated with a link to the resulting ADR.
5. **Review**: scanned (not formally reviewed) at each [quarterly roadmap review](../38-planning-roadmap-techdebt/quarterly-roadmap-process.md) to catch anything that should have been an RFC in hindsight.

## Log

| Date | Decision | Reasoning | Decided by | Module | Promoted? |
|---|---|---|---|---|---|
| 2026-07-15 | Repo documentation structured as numbered topic folders (00-41) rather than a single wiki | Numbered folders give a stable, greppable priority-ordered structure that scales better than a flat wiki as the team grows | CTO / Founders | docs | No |
| 2026-07-15 | Activity categories fixed to the 16-item list in the PRD for MVP, no user-generated categories | Keeps the initial taxonomy small enough to build safety/moderation tooling around before opening it up | PM | activities | No |
| 2026-07-15 | Docker Compose (not Kubernetes) for local and initial staging environments | Matches the modular monolith's operational simplicity; Kubernetes deferred until the microservices migration trigger in [ADR-001](adr-index.md) | DevOps Lead | infra | No |

## Related Documents

- [adr-template.md](adr-template.md) / [adr-index.md](adr-index.md) — where promoted decisions end up.
- [rfc-process.md](rfc-process.md) — the trigger conditions that route a decision to the heavier process instead of this log.
- [../38-planning-roadmap-techdebt/quarterly-roadmap-process.md](../38-planning-roadmap-techdebt/quarterly-roadmap-process.md) — periodic review point for this log.
