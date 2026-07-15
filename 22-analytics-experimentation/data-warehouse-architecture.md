---
title: "Data Warehouse Architecture"
folder: 22-analytics-experimentation
purpose: "Warehouse choice"
pages_estimate: 5
prerequisites: "analytics-event-taxonomy"
dependencies: "infrastructure-cloud"
priority: P1
audience: "BE, DO, G"
status: draft-v1
generated: 2026-07-15
---

# Data Warehouse Architecture

## Overview

This document selects and describes the system of record for ActivityMate's analytical (as opposed to operational) data — every event in [analytics-event-taxonomy.md](analytics-event-taxonomy.md), plus periodic snapshots of operational tables (users, activities, trust scores) from PostgreSQL. It feeds the north-star dashboard, the A/B testing platform, and eventually investor reporting.

## Context / Problem

ActivityMate's operational stack is a modular monolith (FastAPI + PostgreSQL + Redis + MongoDB + MinIO + Meilisearch, per PRD-001 §12) intentionally kept simple pre-PMF. Analytical workloads — funnels, cohort retention, experiment readouts, trust-score trend analysis — are a poor fit for that same PostgreSQL instance: they run large scans and joins that would compete with production OLTP traffic and risk latency spikes on activity creation and chat, the two flows where ActivityMate cannot afford slowness. A separate analytical store is needed before the event taxonomy produces meaningful volume, but it must not become an infrastructure project that competes with core product build time. Mumbai-only launch means data volume is initially modest (low single-digit millions of events/month), which shapes the cost side of this decision heavily toward "cheap at low volume" over "cheap at high volume."

## Options Considered

### Option A — Managed cloud warehouse (BigQuery or Snowflake)
**Pros:**
- Zero operational burden: no cluster sizing, no patching, no on-call for the warehouse itself — critical while the DevOps team (per [../25-infrastructure-cloud/README.md](../25-infrastructure-cloud/README.md)) is sized for a pre-PMF startup, not a data platform team.
- Usage-based pricing means near-zero fixed cost at Mumbai-launch volume; BigQuery's on-demand query pricing and free monthly query tier is close to free at current scale.
- Fast to stand up — days, not sprints. Native connectors exist for the event pipeline (via a lightweight ingestion service or a tool like Fivetran/Airbyte) and for BI tools.
- Elastic scale-up requires no re-architecture if ActivityMate expands to a second or third city and event volume grows 10-50x.
- Strong SQL ergonomics for the Data/Growth team building dashboards and experiment readouts without needing dedicated data-platform engineers.

**Cons:**
- Usage-based cost becomes a real line item at high query volume or if dashboards run frequent, unoptimized full-table scans.
- Data leaves the self-hosted perimeter, which raises questions addressed in [data-governance-access-policy.md](data-governance-access-policy.md) and [../31-privacy-compliance-legal/](../31-privacy-compliance-legal/README.md) — mitigated by pseudonymizing `user_id` at ingestion and never landing raw PII (per the taxonomy's super-property rules) in warehouse tables.
- Vendor lock-in on SQL dialect and cost model, though both BigQuery and Snowflake support standard export formats (Parquet/Avro) that keep a future migration technically feasible.

### Option B — Self-hosted warehouse (ClickHouse or a Postgres-based columnar extension, run in-house alongside the existing Docker Compose stack)
**Pros:**
- No usage-based billing surprise; cost is a flat, predictable infrastructure spend once running.
- Full data locality — everything stays inside the existing self-hosted perimeter (this is the same philosophy behind choosing MinIO over S3 and self-hosted PostgreSQL/MongoDB for the operational stack).
- At high query and storage volume, meaningfully cheaper than metered cloud pricing.

**Cons:**
- Real operational burden: someone owns cluster health, backups, disaster recovery, query performance tuning, and upgrades — for a team currently sized around a modular monolith and Docker Compose (per PRD-001 §12), not a distributed analytical database.
- Slower to stand up and iterate on; every new dashboard requirement competes with core product engineering time during the highest-priority pre-launch and post-launch window.
- Elastic scale requires proactive capacity planning rather than automatic elasticity — a mismatch risk during unpredictable early growth curves.
- No clear cost advantage at Mumbai-launch data volumes; the "cheaper at scale" argument only pays off well past the volume ActivityMate will see before Series A.

## Recommendation & Rationale

**Recommend a managed cloud warehouse (BigQuery, as the default pick, with Snowflake as an acceptable substitute if the team has stronger existing Snowflake expertise) until data volume or warehouse cost crosses a defined threshold.**

Rationale: ActivityMate's near-term constraint is engineering time, not infrastructure spend — the whole thesis of "modular monolith initially, microservices after PMF" (PRD-001 §12) applies here too. A self-hosted warehouse is the microservices-too-early mistake, applied to data infra. The managed option lets the Data/Growth team ship the north-star dashboard and first A/B tests in the first sprint after event volume exists, rather than after a multi-week warehouse build-out.

**Defined re-evaluation threshold:** revisit this decision when either (a) monthly warehouse spend exceeds $2,000 for three consecutive months, or (b) event volume exceeds 500M rows/month (roughly consistent with a 3-4 city footprint), whichever comes first. At that point a self-hosted ClickHouse cluster becomes cost-competitive and the team is more likely to have dedicated data-platform capacity to run it.

**Ingestion path:** events land in Redis (transient buffer) → a lightweight batch exporter service (part of [../08-backend-services/](../08-backend-services/README.md)) → warehouse, on a 5-15 minute batch cadence. Sub-minute streaming is explicitly not a launch requirement; the north-star dashboard and experiment readouts do not need real-time freshness.

**Retention:** raw event tables retained 24 months; aggregated/rollup tables retained indefinitely. Aligns with data minimization principles in [data-governance-access-policy.md](data-governance-access-policy.md).

## Related Documents

- [analytics-event-taxonomy.md](analytics-event-taxonomy.md) — the schema landing in this warehouse
- [ab-testing-platform-spec.md](ab-testing-platform-spec.md) — reads experiment exposure and outcome data from here
- [north-star-kpi-dashboard-spec.md](north-star-kpi-dashboard-spec.md) — primary consumer
- [data-governance-access-policy.md](data-governance-access-policy.md) — access rules for warehouse tables
- [../25-infrastructure-cloud/README.md](../25-infrastructure-cloud/README.md) — cloud provider and networking context
- [../39-decision-records-rfcs/](../39-decision-records-rfcs/README.md) — this decision should be logged as a formal RFC/ADR
