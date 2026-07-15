---
title: "Sprint Planning Process"
folder: 38-planning-roadmap-techdebt
purpose: "Planning cadence"
pages_estimate: 4
prerequisites: "none"
dependencies: "none"
priority: P1
audience: "PM, BE, FE"
status: draft-v1
generated: 2026-07-15
---

# Sprint Planning Process

## Overview

ActivityMate's engineering team plans and sequences work using Kanban with a lightweight weekly planning sync, not Scrum. This document explains why, and lays out exactly how planning, prioritization, and delivery tracking work day to day for the backend, mobile, and product trio building the MVP.

## Context / Problem

At MVP stage, ActivityMate has a small cross-functional team (BE, FE/mobile, PM, with founders doing double duty on product and trust & safety). The team needs a way to decide what gets built next, keep stakeholders (founders, investors, early community ops) informed of progress, and avoid the two failure modes small teams fall into: either no process at all (constant context-switching, silent scope creep) or over-processed ceremony borrowed from 200-person orgs that eats the very engineering time it's supposed to protect.

The core tension is predictability versus overhead. Founders and investors want to know "what ships this month." Engineers building trust-and-safety-adjacent features (verification, reporting, trust score) need room to reprioritize fast when a safety gap is discovered mid-cycle — a rigid two-week sprint commitment is the wrong shape for that.

## Options Considered

**Option A — Scrum (2-week sprints, sprint planning, daily standup, sprint review, retro).**
Pros: predictable cadence investors and non-technical stakeholders can track ("what's in this sprint"); forces regular scope negotiation; retro cadence builds in continuous improvement; well understood if the team scales and needs onboarding materials.
Cons: ceremony overhead (planning, review, retro, standups) is expensive relative to a team of 4-6 engineers; sprint commitment discourages the "drop everything, a safety bug just surfaced" behavior the trust model requires; two-week batching delays discovery of blocked work; story-pointing overhead has near-zero payoff below ~8 engineers.

**Option B — Kanban (continuous flow, WIP limits, pull-based).**
Pros: minimal ceremony; work items move as soon as they're ready, so urgent trust/safety fixes aren't stuck behind a sprint boundary; visualizes bottlenecks directly (a stuck column is visible immediately, not discovered at sprint review); scales down cleanly to a tiny team.
Cons: no natural forcing function for "what ships this month" reporting — has to be manufactured deliberately; without discipline, WIP limits get ignored and everything becomes "in progress"; less legible to stakeholders used to sprint demos.

**Option C — Hybrid ("Scrumban") with fixed 2-week reporting boundary but no formal sprint commitment.**
Pros: gets flow benefits of Kanban with just enough cadence for external reporting.
Cons: risks becoming worst-of-both — teams default to treating the 2-week boundary as a soft sprint anyway, reintroducing the commitment pressure Kanban was chosen to avoid.

## Recommendation & Rationale

**Kanban, with a lightweight weekly planning sync**, revisited only when team size or stakeholder count grows enough to need fixed-cadence coordination (roughly: more than ~10 engineers, or multiple external teams depending on committed delivery dates). At current size, the cost of Scrum ceremony exceeds its coordination benefit, and the flow-based model better matches a product where safety-critical bugs must jump the queue without waiting for a sprint boundary. The weekly sync (not a full Scrum planning meeting) provides just enough of a reporting cadence for founders and PM without introducing sprint commitment.

## The Actual Process

1. **Backlog** lives in the project tracker as a single prioritized list per module (BE, FE/mobile, Trust & Safety, Infra), owned by the PM/CTO jointly per [technical-debt-register.md](technical-debt-register.md) and the [quarterly roadmap](quarterly-roadmap-process.md).
2. **WIP limits**: each engineer holds at most 2 in-progress items; the "In Review" column is capped at 4 total to prevent review bottlenecks — a recurring failure mode in small teams.
3. **Weekly planning sync** (Monday, 30 minutes): review what moved to Done last week, pull the next-highest-priority items into "Ready," and flag anything blocked. This is the entire planning ceremony — no story pointing.
4. **Daily async standup**: a Slack thread update (what moved, what's blocked), not a meeting. Live standups happen only when there's an active incident per [incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md).
5. **Interrupt lane**: a dedicated "Safety/Security Hotfix" swimlane bypasses WIP limits — anything landing here (verification bypass, reporting pipeline failure, data exposure) is pulled immediately regardless of current WIP, consistent with the trust-first principle in the [PRD](../PRD-001_Master_Product_Requirements.md).
6. **Bi-weekly demo**: a 20-minute walkthrough of what shipped, open to founders and investors, replacing the formal sprint review.
7. **Monthly retro**: instead of every-sprint retros, a monthly 45-minute retro covers flow metrics (cycle time, WIP breaches, interrupt-lane volume) and process adjustments.
8. **Reassessment trigger**: this process is re-evaluated once the engineering team exceeds ~10 people or once two or more teams need coordinated release trains — at that point Scrum's forcing functions may outweigh its overhead, and this document should be revised or superseded via the [RFC process](../39-decision-records-rfcs/rfc-process.md).

## Roles

- **PM** owns backlog prioritization and represents the roadmap's Now-quarter commitments (per [quarterly-roadmap-process.md](quarterly-roadmap-process.md)) as concrete backlog items.
- **Engineers (BE/FE/mobile)** pull work, not get assigned it — a core Kanban tenet that keeps ownership clear and avoids the manager-as-dispatcher pattern that doesn't scale even at small size.
- **CTO** owns the interrupt lane trigger criteria and arbitrates when two engineers want to pull the same item.
- **Trust & Safety** has standing authority to inject items directly into the interrupt lane without going through normal backlog prioritization, reflecting the "Safety Over Growth" principle in the [PRD](../PRD-001_Master_Product_Requirements.md).

## Metrics Tracked

Rather than velocity (a Scrum-specific metric this process deliberately avoids), the team tracks flow metrics that fit Kanban's model:

- **Cycle time**: time from "Ready" to "Done" per item, reviewed at the monthly retro to spot process friction.
- **WIP breaches**: how often the 2-item-per-engineer or 4-item-review-column limits are exceeded, signaling either understaffing or a review bottleneck.
- **Interrupt-lane volume**: how many items bypass normal WIP limits in a given month — a rising trend is itself a signal worth discussing, since a constant stream of "emergency" work often means the normal backlog isn't correctly prioritizing safety/security work in the first place.
- **Aging WIP**: any item sitting in "In Progress" beyond 5 business days is flagged at the weekly sync regardless of WIP limit status, since a stuck item quietly consuming an engineer's WIP slot is worse than a visibly blocked one.

## Failure Modes This Process Guards Against

This structure exists specifically to prevent two patterns observed in small teams: (1) safety-critical fixes stuck behind unrelated feature work because nobody had explicit authority to reprioritize mid-cycle, and (2) planning theater — spending real engineering hours on ceremony (story pointing, elaborate sprint boards) that produces no additional shipped value at this team size. Both are treated as process bugs worth fixing immediately if observed, not tolerated as "just how software teams work."

## Related Documents

- [quarterly-roadmap-process.md](quarterly-roadmap-process.md) — the higher-level cadence this weekly process feeds into.
- [technical-debt-register.md](technical-debt-register.md) — where debt items compete for the same backlog slots.
- [capacity-planning.md](capacity-planning.md) — engineering capacity constraints that bound what fits in the backlog.
- [../39-decision-records-rfcs/rfc-process.md](../39-decision-records-rfcs/rfc-process.md) — process for formally changing this document.
- [../04-product/product-roadmap-now-next-later.md](../04-product/product-roadmap-now-next-later.md) — the roadmap artifact this planning process implements.
- [../40-incident-disaster-recovery/incident-management-process.md](../40-incident-disaster-recovery/incident-management-process.md) — governs when live standups replace the async default.
