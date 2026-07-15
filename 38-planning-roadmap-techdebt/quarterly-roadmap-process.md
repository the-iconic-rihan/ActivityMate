---
title: "Quarterly Roadmap Process"
folder: 38-planning-roadmap-techdebt
purpose: "Roadmap cadence"
pages_estimate: 3
prerequisites: "product"
dependencies: "none"
priority: P1
audience: "PM"
status: draft-v1
generated: 2026-07-15
---

# Quarterly Roadmap Process

## Overview

This document defines how ActivityMate reviews, reprioritizes, and communicates its product roadmap on a quarterly cadence — separate from the weekly execution cadence in [sprint-planning-process.md](sprint-planning-process.md). The roadmap process is where "are we building the right things" gets asked; sprint planning is where "are we executing well" gets asked.

## Context / Problem

A pre-PMF, single-city launch (Mumbai) faces a specific roadmap risk: the temptation to plan too far ahead on features (payments, multi-city expansion, AI matching) before the core trust loop — verification, activity creation, safe first meetup, rating — is proven. Conversely, planning too little ahead means engineering thrashes between reactive priorities and never builds toward the Series A milestones in the [investor relations](../35-investor-relations/README.md) documents. The roadmap process has to hold both: a rolling near-term plan detailed enough to execute, and a longer-term direction stable enough to give the team and investors confidence, without hard-committing to dates the team can't yet estimate reliably.

## Options Considered

**Option A — Annual roadmap, revisited only at major pivots.**
Pros: minimal planning overhead; long runway for deep technical investments.
Cons: catastrophically slow to react to what a Mumbai pilot cohort actually reveals (e.g., discovering female retention requires a feature nobody scoped); wrong tool for a pre-PMF product where nearly every assumption in the [PRD](../PRD-001_Master_Product_Requirements.md) is still unvalidated.

**Option B — Monthly full roadmap re-plan.**
Pros: maximally responsive to new data.
Cons: too much overhead for a small team; destroys the ability to make multi-sprint commitments (e.g., a trust score model rollout that spans 6 weeks); signals instability to hiring candidates and investors reviewing the roadmap.

**Option C — Quarterly roadmap review with a Now/Next/Later structure, refreshed monthly at a lighter touch.**
Pros: matches the natural cadence of a startup's OKR/investor-update cycle; Now/Next/Later avoids false-precision date commitments on anything beyond the current quarter; the lighter monthly touch lets urgent learnings (a safety incident, a churn signal) reprioritize "Next" without a full re-plan.
Cons: requires discipline to keep "Later" genuinely non-committal rather than becoming a stealth backlog of promises.

## Recommendation & Rationale

**Quarterly roadmap review using Now/Next/Later**, matching the artifact already defined in [../04-product/product-roadmap-now-next-later.md](../04-product/product-roadmap-now-next-later.md), with a lighter monthly check-in. This is the standard shape for an early-stage product team: it gives investors and the team a legible planning horizon without pretending to know, in January, what will matter in Q3.

## The Actual Process

1. **Quarterly Roadmap Review** (half-day session, first week of the quarter): PM and CTO jointly review the previous quarter's Now items against actual delivery, walk through updated inputs (user research findings, [trust-safety-fraud](../13-trust-safety-fraud/README.md) incident data, growth metrics from [analytics-experimentation](../22-analytics-experimentation/README.md)), and reshuffle Now/Next/Later.
2. **Now** = committed for this quarter, broken down into backlog items feeding weekly sprint planning. **Next** = likely for next quarter, directionally scoped but not broken into tickets. **Later** = directionally true but explicitly not promised — no dates attached.
3. **Monthly check-in** (1 hour): PM reviews whether Now items are tracking, and whether any Next/Later item needs to be pulled forward due to new information (e.g., a safety gap surfaced by [incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md) escalates a trust feature from Later to Now).
4. **Inputs to every quarterly review**: activity completion rate, female retention, host acceptance rate, and trust score trends (the [PRD](../PRD-001_Master_Product_Requirements.md) success metrics) — the roadmap is metrics-driven, not opinion-driven.
5. **Founder/investor communication**: the quarterly roadmap output feeds directly into investor updates per [../35-investor-relations/README.md](../35-investor-relations/README.md); Now-quarter commitments are what gets reported as delivered or missed.
6. **Capacity check**: no item moves into Now unless [capacity-planning.md](capacity-planning.md) shows available engineering bandwidth — the roadmap is not allowed to exceed staffed capacity.
7. **Debt allocation**: every quarterly review reserves a fixed percentage of Now capacity (starting at 20%) for items pulled from [technical-debt-register.md](technical-debt-register.md), so debt paydown is never entirely displaced by feature pressure.

## Who Attends and Decides

The quarterly review is jointly run by PM and CTO — PM brings user research, growth, and market signal; CTO brings technical feasibility and capacity reality. Founders attend as observers/input, not as the deciding body, to keep the roadmap grounded in metrics rather than the most recent conversation a founder had. Trust & Safety leadership has standing input rights on any item that touches verification, reporting, or the trust score, consistent with the escalation authority defined in [../40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md).

## Now/Next/Later in Practice

A common failure mode with this framework is "Next" quietly becoming a promise and "Later" quietly becoming a dumping ground nobody revisits. Two guardrails: first, nothing moves from Next into Now without going through the same metrics-driven review as a brand-new proposal — sitting in Next longer does not itself earn a slot. Second, Later items are revisited explicitly, not just carried forward silently — every quarterly review includes a 15-minute "prune Later" pass where stale items are either promoted to Next with a real justification or removed from the roadmap document entirely.

## Escalation Outside the Cadence

Some events can't wait for the next quarterly or monthly checkpoint — a Sev-1 infrastructure incident revealing a structural gap (per [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md)) or an S-Emergency/S-Critical safety incident (per [safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md)) can force an out-of-cycle roadmap change. In that case, CTO and PM convene an ad hoc mini-review within 3 business days of the triggering postmortem, scoped only to the affected area of the roadmap — this is treated as an exception process, not a replacement for the regular cadence.

## Related Documents

- [../04-product/product-roadmap-now-next-later.md](../04-product/product-roadmap-now-next-later.md) — the roadmap artifact itself.
- [sprint-planning-process.md](sprint-planning-process.md) — weekly execution cadence downstream of this process.
- [capacity-planning.md](capacity-planning.md) — the capacity constraint this process must respect.
- [technical-debt-register.md](technical-debt-register.md) — competes for Now-quarter capacity.
- [../35-investor-relations/README.md](../35-investor-relations/README.md) — consumer of quarterly roadmap outputs.
- [../40-incident-disaster-recovery/safety-incident-escalation.md](../40-incident-disaster-recovery/safety-incident-escalation.md) — one of the two triggers for an out-of-cycle roadmap review.
