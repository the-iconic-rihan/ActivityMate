---
title: "Technical Debt Register"
folder: 38-planning-roadmap-techdebt
purpose: "Debt register"
pages_estimate: 3
prerequisites: "decision-records-rfcs"
dependencies: "none"
priority: P1
audience: "BE, CTO"
status: draft-v1
generated: 2026-07-15
---

# Technical Debt Register

## Overview

The technical debt register is a living log of known shortcuts taken during ActivityMate's build — deliberate or accidental — so that "we'll fix it later" is a written commitment rather than a verbal one that quietly becomes permanent. This document defines what qualifies as a register entry, the severity taxonomy, and the review cadence.

## Context / Problem

A modular monolith built fast toward a Mumbai launch will accumulate debt: skipped test coverage, hard-coded config, a fraud check that's a regex instead of a model, a chat feature built without message retention limits. Most of that debt is fine to defer. The specific risk this folder exists to prevent (per the [folder README](README.md)) is debt in trust-, safety-, or security-adjacent code paths going undocumented — a shortcut in phone-OTP verification or trust score computation that nobody wrote down stops being a known tradeoff and becomes a silent vulnerability. Without a register, this class of debt is discovered only during an incident, at which point it's a [postmortem](../40-incident-disaster-recovery/postmortem-template.md) item instead of a planned fix.

## Options Considered

**Option A — No formal register; debt tracked as regular backlog tickets tagged "tech-debt."**
Pros: zero process overhead, uses existing tooling.
Cons: tech-debt tickets get silently deprioritized forever next to feature work with clearer business value; no severity distinction means a cosmetic shortcut and an auth bypass shortcut look identical in the backlog; no forcing function to review.

**Option B — Dedicated technical debt register with mandatory severity tagging, reviewed quarterly, with explicit escalation for trust/safety/security-tagged items.**
Pros: makes debt visible and comparable across the codebase; severity tagging lets CTO triage in seconds; the trust/safety/security tag forces those items into roadmap conversations rather than letting them rot; creates an audit trail useful for security reviews and, eventually, investor due diligence.
Cons: another artifact to maintain; only works if engineers actually log debt at the time it's created, which requires cultural buy-in, not just a template.

## Recommendation & Rationale

**Dedicated register (Option B)**, because the cost of an undocumented trust/safety/security shortcut vastly exceeds the maintenance cost of a spreadsheet-grade log. This is consistent with the "Trust Before Matching" and "Safety Over Growth" principles in the [PRD](../PRD-001_Master_Product_Requirements.md) — those principles are meaningless if the shortcuts taken against them aren't tracked.

## Register Format

Each entry is a row with the following fields:

| Field | Description |
|---|---|
| ID | `TD-NNN`, sequential |
| Title | One-line description of the shortcut |
| Module | e.g. auth, trust-engine, chat, matching |
| Severity | `S-critical` (trust/safety/security-adjacent, must be resolved before scale-up), `S-high` (correctness or data-integrity risk), `S-medium` (maintainability/performance), `S-low` (cosmetic/style) |
| Trust/Safety/Security flag | Yes/No — forces quarterly roadmap visibility per [quarterly-roadmap-process.md](quarterly-roadmap-process.md) |
| Introduced | Date, PR/commit link, related [ADR](../39-decision-records-rfcs/adr-index.md) if applicable |
| Reason | Why the shortcut was taken (deadline, unknown scale, missing dependency) |
| Payoff plan | What resolving it looks like, and the trigger condition (e.g., "before opening city #2," "before payments launch") |
| Status | Open / Scheduled / Resolved |
| Owner | Engineer or team accountable |

## Illustrative Entries

- **TD-001** — Trust score recalculation runs synchronously on every rating submission instead of via an async queue. Module: trust-engine. Severity: S-high. Flag: Yes. Reason: MVP speed, no queue infra yet. Payoff plan: move to Redis-backed async job before activity volume exceeds ~500/day. Status: Open.
- **TD-002** — Selfie verification uses manual admin review only, no liveness-detection model. Module: auth/trust. Severity: S-critical. Flag: Yes. Reason: no ML resourcing at MVP stage. Payoff plan: introduce automated liveness check before opening city #2, per [user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md). Status: Scheduled.
- **TD-003** — MongoDB document schemas for chat messages are unversioned. Module: chat. Severity: S-medium. Flag: No. Reason: schema still evolving. Payoff plan: introduce schema versioning before v1.0 chat freeze. Status: Open.

## The Actual Process

1. Any engineer introducing a known shortcut adds a register entry in the same PR, per [../37-engineering-standards/README.md](../37-engineering-standards/README.md) code review norms.
2. `S-critical` entries are flagged to the CTO within 24 hours and reviewed for immediate mitigation, not just future scheduling.
3. The register is reviewed in full at every [quarterly roadmap review](quarterly-roadmap-process.md); a fixed 20% of Now-quarter capacity is reserved for debt paydown, prioritized by severity.
4. Entries tied to an [ADR](../39-decision-records-rfcs/adr-index.md) reference it directly, so the original tradeoff reasoning isn't lost.
5. Resolved entries are kept in the register (marked Resolved) rather than deleted, preserving the audit trail for future security reviews.

## Why Severity, Not Just Priority

A plain priority field (P0-P3) conflates two different questions: "how bad is this if it stays broken" and "how soon do we want to fix it." The severity taxonomy above answers the first question independently of roadmap pressure, so that a genuinely dangerous shortcut (e.g. a fraud check that can be trivially bypassed) can't get quietly reprioritized down simply because the quarter is busy. The `Trust/Safety/Security flag` is deliberately a separate boolean from severity — a low-severity item can still carry the flag if it touches a sensitive code path, which forces it into the quarterly roadmap visibility pass in [quarterly-roadmap-process.md](quarterly-roadmap-process.md) even if it wouldn't otherwise rise to the top of a severity-sorted list.

## What Does Not Belong in the Register

Not every imperfection is debt. Code style preferences, disagreements about naming, or "I'd have architected this differently" are not register entries — they belong in normal code review per [../37-engineering-standards/README.md](../37-engineering-standards/README.md). The register is reserved for shortcuts that were *known and deliberate at the time*, or discovered later to represent a real gap between intended and actual behavior — for example, a postmortem in [../40-incident-disaster-recovery/postmortem-template.md](../40-incident-disaster-recovery/postmortem-template.md) revealing that an alert threshold was never tuned belongs here; a subjective preference does not.

## Related Documents

- [quarterly-roadmap-process.md](quarterly-roadmap-process.md) — where debt competes for roadmap capacity.
- [../39-decision-records-rfcs/adr-index.md](../39-decision-records-rfcs/adr-index.md) — formal decisions that may generate debt entries.
- [../40-incident-disaster-recovery/postmortem-template.md](../40-incident-disaster-recovery/postmortem-template.md) — postmortems often produce new register entries.
- [../37-engineering-standards/README.md](../37-engineering-standards/README.md) — code review standards that enforce logging debt at introduction time.
- [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md) — owner consulted on any entry carrying the trust/safety/security flag.
