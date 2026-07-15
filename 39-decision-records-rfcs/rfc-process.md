---
title: "Rfc Process"
folder: 39-decision-records-rfcs
purpose: "RFC process"
pages_estimate: 3
prerequisites: "none"
dependencies: "none"
priority: P0
audience: "BE, CTO"
status: draft-v1
generated: 2026-07-15
---

# RFC Process

## Overview

This document defines when an RFC (Request for Comments) is required before ActivityMate makes an engineering decision, and how the RFC lifecycle runs from draft to accepted [ADR](adr-index.md). It exists to make sure decisions that affect more than one module or team get deliberate, written scrutiny — while keeping every other decision fast and unencumbered.

## Context / Problem

A small team building a modular monolith (per the [PRD](../PRD-001_Master_Product_Requirements.md) and [tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md)) makes dozens of technical decisions a week. Requiring formal review for all of them would grind the team to a halt. But some decisions — changing the auth token format, altering the trust score computation, picking a message queue, changing the module boundary between chat and notifications — have blast radius beyond the engineer making the call, and a bad one is expensive to unwind once other modules depend on it. The problem is drawing the line precisely enough that engineers know, without asking, whether their change needs an RFC.

## Options Considered

**Option A — No formal RFC gate; any significant decision is discussed ad hoc in Slack or standup.**
Pros: zero process friction, fastest to decide.
Cons: no written record of alternatives considered, so decisions can't be revisited intelligently later; "significant" is subjectively judged in the moment, meaning genuinely cross-cutting decisions slip through undiscussed until they cause an incident; new engineers have no artifact to learn *why* the system is shaped the way it is.

**Option B — RFC required for every architectural or schema change, however small.**
Pros: maximum rigor and documentation coverage.
Cons: massive overhead for a small team; discourages engineers from proposing improvements because the process cost exceeds the perceived value of small changes; RFC fatigue leads to rubber-stamping, which defeats the purpose.

**Option C — RFC required only when a change crosses module or team boundaries, changes a public contract (API, schema, auth flow), or touches trust/safety/security-critical paths; everything else proceeds via normal PR review.**
Pros: scopes the overhead to where it earns its cost; gives a clear, testable trigger ("does this cross a boundary or touch a critical path?") instead of a subjective "is this significant" judgment call; keeps velocity high on the vast majority of day-to-day changes.
Cons: requires the trigger conditions to be well-specified and periodically re-taught, since a fuzzy boundary reintroduces the ambiguity of Option A.

## Recommendation & Rationale

**Option C.** The trigger is scoped precisely below so it doesn't rely on judgment calls in the moment. This mirrors how the [service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md) already defines module ownership — an RFC is required exactly where that boundary is being crossed or redrawn.

## When an RFC Is Required

An RFC is mandatory if a proposed change meets **any** of the following:

1. **Crosses module boundaries** — e.g. a change to the activities module that requires the chat module or trust engine to change their contract.
2. **Changes a public/internal API contract** — anything documented in [../11-api/README.md](../11-api/README.md) that other modules or the mobile client depend on.
3. **Touches trust, safety, fraud, or auth-critical paths** — verification logic, trust score computation, reporting/blocking pipeline, session/token handling. Consistent with "Trust Before Matching" in the [PRD](../PRD-001_Master_Product_Requirements.md), these get scrutiny by default even if the change looks small.
4. **Introduces a new external dependency or vendor** — a new database, message broker, SaaS integration, or major library that the team will be operationally responsible for.
5. **Has irreversible or expensive-to-reverse consequences** — schema migrations affecting production data, changes to data retention affecting [privacy-compliance-legal](../31-privacy-compliance-legal/README.md) obligations.

Anything not matching these five triggers proceeds through normal PR review — no RFC needed.

## The Actual Process

1. **Draft**: author writes the RFC using the same structure as an [ADR](adr-template.md) — Context, Options Considered, Recommendation — but framed as a proposal, not a settled decision. Posted as a PR to this folder or a shared doc, tagged `rfc-open`.
2. **Comment window**: minimum 3 business days for BE/relevant module owners to comment; safety/security-triggering RFCs additionally require sign-off from whoever owns [trust-safety-fraud](../13-trust-safety-fraud/README.md) at the time.
3. **Resolution**: CTO makes the final call if consensus doesn't emerge; the RFC is marked Accepted, Rejected, or Needs Revision.
4. **Conversion**: an Accepted RFC is converted into a formal [ADR](adr-template.md) and added to [adr-index.md](adr-index.md) — the RFC is the deliberation, the ADR is the durable record.
5. **Escape hatch**: for a genuinely time-critical decision (e.g. an active safety incident requiring an immediate auth change), the CTO may approve a change without the full comment window, but a retroactive ADR is still required within 5 business days, tagged `emergency-decision`.

## Related Documents

- [adr-template.md](adr-template.md) — the format an accepted RFC converts into.
- [adr-index.md](adr-index.md) — where accepted RFCs end up.
- [decision-log.md](decision-log.md) — for decisions below the RFC threshold.
- [../07-architecture/service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md) — defines the module boundaries that trigger #1.
- [../37-engineering-standards/README.md](../37-engineering-standards/README.md) — general code review standards for non-RFC changes.
