# 22-analytics-experimentation/

**Phase:** Phase 5 — Data, AI & Admin  
**Priority:** P1 (P1 — blocks public launch)  
**Owner:** Data / Growth  
**Depends on:** backend-services  
**Document count:** 5

## Purpose

Event tracking, data warehouse, and the experimentation platform. Corresponds to PRD-017.

## Why this folder exists

Nearly every prioritization framework, matching algorithm, and growth loop elsewhere in this repo depends on usage data existing and being trustworthy — this folder is a dependency of most later-stage documents, which is why it is prioritized earlier than the P1 label alone might suggest.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [analytics-event-taxonomy.md](analytics-event-taxonomy.md) | Event schema | The canonical event schema so every team logs events consistently. | 5 | backend-services | none | P0 | BE, G |
| [data-warehouse-architecture.md](data-warehouse-architecture.md) | Warehouse choice | Compares a managed warehouse — BigQuery or Snowflake, fast to stand up, usage-based cost — against a self-hosted warehouse, cheaper at high volume but a real operational burden pre-PMF. Recommends a managed warehouse until data volume or cost crosses a defined threshold. | 5 | analytics-event-taxonomy | infrastructure-cloud | P1 | BE, DO, G |
| [ab-testing-platform-spec.md](ab-testing-platform-spec.md) | Experiment platform | Experiment assignment and guardrail-metric auto-abort logic. | 4 | analytics-event-taxonomy | product | P1 | BE, PM |
| [north-star-kpi-dashboard-spec.md](north-star-kpi-dashboard-spec.md) | KPI dashboard | A dashboard surfacing the north-star metric and its component drivers. | 3 | data-warehouse-architecture | foundation | P1 | F, PM, I |
| [data-governance-access-policy.md](data-governance-access-policy.md) | Access governance | Who can access what data internally — trust and safety data gets stricter access controls than general engagement metrics. | 3 | analytics-event-taxonomy | privacy-compliance-legal | P0 | SEC, LG |
