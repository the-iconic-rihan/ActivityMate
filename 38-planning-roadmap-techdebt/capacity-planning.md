---
title: "Capacity Planning"
folder: 38-planning-roadmap-techdebt
purpose: "Capacity planning"
pages_estimate: 2
prerequisites: "hiring-plan-org-chart"
dependencies: "none"
priority: P2
audience: "CTO, PM"
status: draft-v1
generated: 2026-07-15
---

# Capacity Planning

## Overview

This document defines how ActivityMate tracks engineering capacity against roadmap demand, so quarterly and sprint-level planning never commits to more than the team can actually deliver. It is a P2 document — needed as the team grows past founder-led execution and roadmap commitments start being made to investors and multi-city expansion plans, per [../41-localization-expansion/README.md](../41-localization-expansion/README.md).

## Context / Problem

At MVP stage with a handful of engineers, capacity planning is mostly implicit — everyone knows what everyone else is doing. That breaks down the moment the team crosses roughly 6-8 engineers or takes on parallel workstreams (e.g., core product team plus a dedicated trust & safety engineering pod per [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md)). Without explicit capacity tracking, the quarterly roadmap in [quarterly-roadmap-process.md](quarterly-roadmap-process.md) risks becoming aspirational rather than deliverable, which erodes trust with both the team (chronic overcommitment burns people out) and investors (missed quarters without a clear capacity explanation look like execution failure rather than a planning input problem).

## Options Considered

**Option A — No explicit capacity model; roadmap items prioritized purely by business value, capacity assumed elastic.**
Pros: no overhead, fast to plan.
Cons: guarantees overcommitment; makes the 20% debt-paydown reservation in [technical-debt-register.md](technical-debt-register.md) the first casualty of any deadline pressure — exactly the pattern this folder exists to prevent.

**Option B — Detailed engineering-hours estimation per ticket, rolled up against team hours (classic story-point-derived velocity).**
Pros: high precision in theory.
Cons: false precision in practice at pre-PMF stage where estimation variance is enormous; heavy overhead inconsistent with the low-ceremony Kanban model chosen in [sprint-planning-process.md](sprint-planning-process.md).

**Option C — Lightweight capacity model: headcount by function, minus fixed overhead (on-call, debt reservation, PTO buffer), reviewed at each quarterly roadmap cycle.**
Pros: matches the low-ceremony philosophy already adopted; good enough precision for Now/Next/Later planning; scales naturally as [hiring-plan-org-chart.md](../36-hiring-people-culture/hiring-plan-org-chart.md) headcount grows.
Cons: coarser than per-ticket estimation, so still relies on PM/CTO judgment for individual roadmap item sizing.

## Recommendation & Rationale

**Option C**, consistent with the team's existing low-ceremony process choices. Precision beyond function-level headcount tracking isn't earned until the team and roadmap are large enough that misallocation actually costs meaningful money — which, per [../41-localization-expansion/README.md](../41-localization-expansion/README.md), is roughly the multi-city expansion stage.

## The Actual Process

1. **Capacity baseline**: at each quarterly roadmap review, CTO tallies available engineer-weeks by function (BE, mobile/FE, DevOps, ML) sourced from current headcount in [../36-hiring-people-culture/hiring-plan-org-chart.md](../36-hiring-people-culture/hiring-plan-org-chart.md).
2. **Fixed deductions** are subtracted before any roadmap item is planned: 15% for on-call/incident response (per [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md)), 20% reserved for technical debt paydown, and a PTO/buffer allowance (~10%).
3. **Remaining capacity** is what the Now-quarter roadmap is allowed to draw against; PM and CTO size roadmap items in relative terms (S/M/L/XL) against this pool, not precise hour estimates.
4. **Overflow rule**: if Now-quarter demand exceeds available capacity, items are cut from Now to Next — the roadmap is never stretched by extending working hours or skipping the debt/on-call reservations.
5. **Reassessment**: capacity is re-baselined every quarter and any time headcount changes materially (a hire starts, a departure occurs).

## Function-Specific Notes

Capacity is tracked by function, not as one undifferentiated engineering pool, because roadmap demand is rarely evenly distributed across functions. A quarter heavy on trust-engine and verification work draws primarily on BE capacity; a quarter focused on the Flutter app's activity discovery UX draws primarily on mobile/FE. Treating all engineers as interchangeable capacity would understate real constraints — a roadmap item can be blocked on a specific function being fully allocated even while another function has slack. The CTO calls this out explicitly at each quarterly review rather than only reporting an aggregate capacity number.

## When This Document Gets Used

At current team size, this document is mostly latent — capacity is visible enough informally that a full model is P2, not P0. It becomes load-bearing at two triggers: (1) headcount crosses roughly 8-10 engineers, per the reassessment trigger in [sprint-planning-process.md](sprint-planning-process.md), where informal tracking stops being reliable; or (2) the company begins committing roadmap dates externally to investors or partners in a way that makes overcommitment costly beyond internal team frustration, per [../35-investor-relations/README.md](../35-investor-relations/README.md).

## Related Documents

- [quarterly-roadmap-process.md](quarterly-roadmap-process.md) — the planning cycle this capacity model feeds.
- [technical-debt-register.md](technical-debt-register.md) — source of the fixed 20% capacity reservation.
- [../36-hiring-people-culture/hiring-plan-org-chart.md](../36-hiring-people-culture/hiring-plan-org-chart.md) — headcount source of truth.
- [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md) — basis for the on-call capacity deduction.
- [sprint-planning-process.md](sprint-planning-process.md) — the weekly execution layer capacity ultimately bounds.
