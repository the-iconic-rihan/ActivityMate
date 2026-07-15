---
title: "Sop Library Index"
folder: 34-operations-support
purpose: "SOP index"
pages_estimate: 2
prerequisites: "none"
dependencies: "none"
priority: P2
audience: "CM"
status: draft-v1
generated: 2026-07-15
---

# SOP Library Index

## Overview

Standard operating procedures accumulate naturally once support and community operations are running — step-by-step how-tos that are too granular and too frequently updated to live inside the higher-level policy documents in this folder. This document is the index and governance model for that SOP library, not the SOPs themselves; it exists so the library has a defined home, naming convention, and review cadence from day one instead of sprawling into ad hoc documents scattered across chat threads and shared drives.

## Why a Separate Index

The policy documents in this folder ([customer-support-playbook.md](customer-support-playbook.md), [community-management-ops.md](community-management-ops.md), [crisis-communication-plan.md](crisis-communication-plan.md)) answer "what is the process and why." SOPs answer "exactly which buttons do I click, in what order, right now" — operational detail that changes as tooling changes (e.g., an admin-panel UI update) without the underlying policy changing at all. Conflating the two means every minor tooling tweak forces an edit to a policy document, and every policy nuance gets lost in a step-by-step click-through guide. Keeping them separate keeps both types of document usable.

## SOP Categories (populated as they are written)

- **Support operations** — ticket triage walkthroughs, macro usage, escalation button-by-button steps inside the extended admin panel (see [support-tooling-evaluation.md](support-tooling-evaluation.md)), Tier 3 safety fast-path click-through.
- **Community operations** — new-community setup checklist, organizer onboarding steps, community health-signal dashboard usage (see [community-management-ops.md](community-management-ops.md)).
- **Crisis response** — the tabletop-exercise procedure, statement-approval routing checklist, spokesperson activation steps (operational companion to [crisis-communication-plan.md](crisis-communication-plan.md)).
- **Cross-functional handoffs** — exact steps for handing a ticket from support to trust-safety, from community management to moderation, and from either into the incident-escalation process (see [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md)).

## Naming Convention

`SOP-[category-prefix]-[NNN]_short-descriptive-name.md`, e.g., `SOP-SUP-001_tier3-safety-ticket-handoff.md`, `SOP-CM-004_new-community-setup.md`. Category prefixes: `SUP` (support), `CM` (community management), `CRI` (crisis), `XFN` (cross-functional). Numbering is sequential within category, not globally, so categories can grow independently.

## Ownership and Review Cadence

Each SOP has a single named owner (the role, not necessarily the individual, to survive staff turnover) and a review date stamped in its frontmatter. SOPs are reviewed whenever the underlying tool or policy changes, and otherwise on a standing quarterly pass to catch silent drift (a step that references a UI element that has since moved, for instance). Stale SOPs (no review in 2+ quarters) are flagged in this index rather than silently trusted.

## Current Status

No SOPs have been written yet — this index is the scaffold the library will be built into as customer-support-playbook.md, support-tooling-evaluation.md, and community-management-ops.md move from policy design into live operation during and after Mumbai launch. The first SOPs expected are the Tier 3 safety fast-path handoff and the new-community setup checklist, since both have concrete, launch-blocking operational steps behind them.

## Related Documents

- [customer-support-playbook.md](customer-support-playbook.md)
- [support-tooling-evaluation.md](support-tooling-evaluation.md)
- [community-management-ops.md](community-management-ops.md)
- [crisis-communication-plan.md](crisis-communication-plan.md)
