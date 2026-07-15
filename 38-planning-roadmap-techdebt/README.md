# 38-planning-roadmap-techdebt/

**Phase:** Phase 8 — Investor, People & Governance  
**Priority:** P1 (P1 — blocks public launch)  
**Owner:** CPO / CTO  
**Depends on:** product, architecture  
**Document count:** 4

## Purpose

How work gets planned and sequenced, and how technical debt is tracked rather than silently accumulated.

## Why this folder exists

A separate technical-debt register exists because 'we'll fix it later' without a written record is exactly how trust- and safety-adjacent shortcuts quietly become permanent. This folder makes debt visible and prioritizable instead.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [sprint-planning-process.md](sprint-planning-process.md) | Planning cadence | Compares Scrum (predictable cadence, overhead a small team may not need) against Kanban (continuous flow, lower ceremony). Recommends Kanban with a lightweight weekly planning sync at the current team size, revisiting Scrum only if team and stakeholder count grow enough to need fixed-cadence coordination. | 4 | none | none | P1 | PM, BE, FE |
| [quarterly-roadmap-process.md](quarterly-roadmap-process.md) | Roadmap cadence | The roadmap review and reprioritization cadence. | 3 | product | none | P1 | PM |
| [technical-debt-register.md](technical-debt-register.md) | Debt register | A living log of known shortcuts, with explicit severity tags for anything touching trust, safety, or security code paths. | 3 | decision-records-rfcs | none | P1 | BE, CTO |
| [capacity-planning.md](capacity-planning.md) | Capacity planning | Engineering capacity tracked against roadmap demand. | 2 | hiring-plan-org-chart | none | P2 | CTO, PM |
