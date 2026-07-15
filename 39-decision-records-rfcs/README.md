# 39-decision-records-rfcs/

**Phase:** Phase 8 — Investor, People & Governance  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** CTO  
**Depends on:** architecture  
**Document count:** 4

## Purpose

Architecture Decision Records and the RFC process that produces them.

## Why this folder exists

Every 'compares X vs Y, recommends Z' decision made elsewhere in this repo — tech stack, database, chat build-vs-buy — should eventually be formalized as an ADR here. This folder is the durable record that survives beyond whoever made the original call.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [adr-template.md](adr-template.md) | ADR format | The standard ADR format: context, options considered, decision, consequences. | 1 | none | none | P0 | BE, CTO |
| [rfc-process.md](rfc-process.md) | RFC process | When an RFC is required before a decision is made — anything affecting more than one module or team. | 3 | none | none | P0 | BE, CTO |
| [adr-index.md](adr-index.md) | ADR index | A running index of all accepted ADRs. | 2 | adr-template | none | P0 | BE, CTO |
| [decision-log.md](decision-log.md) | Decision log | A lighter-weight log for decisions too small for a full ADR but still worth recording. | 2 | none | none | P1 | BE, CTO |
