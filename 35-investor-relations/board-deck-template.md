---
title: "Board Deck Template"
folder: 35-investor-relations
purpose: "Board deck template"
pages_estimate: 3
prerequisites: "north-star-kpi-dashboard-spec"
dependencies: "none"
priority: P1
audience: "F, I"
status: draft-v1
generated: 2026-07-15
---

# Board Deck Template

**Status:** Draft v1 — recurring template, keyed to the metrics defined in [north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md).

## Scope

A recurring board-update template.

## Purpose

Board meetings should run on a predictable, repeatable deck structure so that (a) board members can track trends across meetings without re-orienting each time, and (b) preparation time for the founding team shrinks as the template becomes routine rather than reinvented each quarter. This template is designed to be filled in every board cycle (recommended: quarterly pre-Series A, moving to a standing monthly/quarterly cadence post-Series A) directly from live data sources rather than manually reconstructed each time.

## Standing section order

### 1. Snapshot (1 slide)

One slide, refreshed every meeting, showing: the north-star metric (weekly completed trusted meetups per active user) trend line, headline WAU/DAU, cash position and runway, and a one-line "state of the company" sentence. This is the only slide a time-constrained board member needs to read to get the gist.

### 2. North-star and safety metrics (1–2 slides)

Pulled directly from [north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md):
- Weekly completed trusted meetups per active user (trend, vs. last quarter, vs. plan)
- Activity completion rate, host acceptance rate, average rating
- Female retention (explicitly tracked as a trust proof point, not folded into generic retention)
- Report rate and average trust score trend — the board's primary visibility into whether growth is outpacing the trust & safety function's ability to keep the platform safe

### 3. Product and engineering update (1 slide)

Shipped since last board meeting, what's in flight, and any material technical-debt or architecture decisions logged in [decision-records-rfcs](../39-decision-records-rfcs/README.md) that the board should be aware of (e.g. modular-monolith-to-microservices migration timing).

### 4. Growth and GTM update (1 slide)

Progress against the go-to-market wedge in [go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md): city/neighborhood density, power-host count and activity, CAC by channel vs. the model in [financial-model.md](../35-investor-relations/financial-model.md).

### 5. Trust & safety incident summary (1 slide, standing item — never skipped)

Any safety incidents since last meeting, resolution status, and any process changes made as a result. This slide is intentionally a permanent fixture even in a "quiet" quarter — a board that only hears about trust & safety when something goes wrong will underinvest in it; a standing slide keeps it visible as core infrastructure, not a crisis function.

### 6. Financials (1–2 slides)

Actuals vs. the model in [financial-model.md](financial-model.md): revenue, burn, runway, and the biggest variance from plan with a one-line explanation.

### 7. Hiring and org (1 slide)

Headcount vs. the plan in [hiring-plan-org-chart.md](../36-hiring-people-culture/hiring-plan-org-chart.md), key open roles, and any regretted attrition.

### 8. Asks of the board (1 slide)

Specific decisions or introductions needed from board members this cycle — kept explicit so board time converts into action rather than passive updates.

### 9. Appendix

Detailed metric tables, cohort charts, and anything referenced but not walked through live — kept separate so the core deck stays under ~10 slides and a 45–60 minute meeting.

## Maintenance note

This template's metric definitions must stay in lockstep with [north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md) — if that spec's definitions change (e.g. how "completed activity" is counted), this template inherits the change automatically by reference rather than redefining metrics locally.
