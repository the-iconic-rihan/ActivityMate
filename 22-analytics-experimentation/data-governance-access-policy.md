---
title: "Data Governance Access Policy"
folder: 22-analytics-experimentation
purpose: "Access governance"
pages_estimate: 3
prerequisites: "analytics-event-taxonomy"
dependencies: "privacy-compliance-legal"
priority: P0
audience: "SEC, LG"
status: draft-v1
generated: 2026-07-15
---

# Data Governance & Access Policy

## Overview

This policy governs who inside ActivityMate can access which categories of analytical data. It exists because the event taxonomy ([analytics-event-taxonomy.md](analytics-event-taxonomy.md)) and warehouse ([data-warehouse-architecture.md](data-warehouse-architecture.md)) will, by design, aggregate signals that range from harmless (button-tap counts) to sensitive (report contents, verification failures, location patterns tied to night activities). Trust and safety data is treated as a distinct, more restricted tier than general engagement analytics — this is a settled decision, not an open question.

## Data Classification Tiers

**Tier 1 — General engagement (open to all employees with warehouse access):**
Aggregated, non-identifying metrics: activity-category popularity, DAU/WAU, funnel conversion rates, dashboard-level rollups. No `user_id`-level drill-down without additional permission.

**Tier 2 — User-level behavioral (Data/Growth, PM, Engineering leads):**
Row-level event data joined to pseudonymous `user_id`. Enables cohort analysis and experiment debugging. Excludes anything in Tier 3.

**Tier 3 — Trust & safety sensitive (Trust & Safety team, Security Lead, on-call incident responders, Legal on a need-to-know basis only):**
Report contents and categories, verification failure reasons and selfie/ID review artifacts, moderation case notes, admin audit logs, no-show/cancellation patterns tied to individually identifiable users, and any data supporting an active safety investigation. Access requires named, logged approval — not blanket role membership — reviewed per [audit-log-spec.md](../24-admin-panel/audit-log-spec.md) conventions applied to data access rather than just admin-panel actions.

**Tier 4 — Regulated PII (restricted to a named minimal set: BE Lead, DPO/Legal, and automated systems only):**
Phone numbers, email addresses, government ID images/numbers, precise GPS traces, raw selfie images. This tier never enters the analytical warehouse at all — it stays in the operational PostgreSQL/MongoDB/MinIO stores behind [../12-auth/README.md](../12-auth/README.md) and [../18-media-storage/README.md](../18-media-storage/README.md) access controls, consistent with the taxonomy's rule that raw PII never becomes an event property.

## Access Principles

1. **Least privilege by default.** New hires get Tier 1 access on day one; Tier 2/3/4 require an explicit, time-bound request tied to a stated business reason.
2. **Trust & safety data is never used for growth optimization.** Report rates and verification failure patterns exist to protect users, not to be mined for engagement tuning — a hard boundary enforced by tiering, not just policy language.
3. **Pseudonymization at the join layer.** Warehouse tables join on an internal `user_id`, never phone/email. Re-identification (mapping `user_id` back to a real person) requires Tier 4 access and is logged.
4. **Access reviews.** Tier 3 and 4 access lists are reviewed quarterly by Security Lead and DPO; any access unused for 90 days is revoked automatically.
5. **Vendor data sharing.** If the managed warehouse (BigQuery/Snowflake per [data-warehouse-architecture.md](data-warehouse-architecture.md)) or any analytics/BI vendor is given access, contracts must include a Data Processing Agreement consistent with [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md), and only pseudonymized data ever leaves the operational perimeter.

## Enforcement

Warehouse access is provisioned via role-based grants mapped to these four tiers (not ad hoc per-table grants), reviewed alongside — but kept administratively separate from — the internal admin-panel RBAC model in [../24-admin-panel/rbac-admin-roles.md](../24-admin-panel/rbac-admin-roles.md), since warehouse/analytics access and admin-panel operational access serve different purposes and different audiences. Any access-tier violation (e.g., a Tier 2 credential querying Tier 3 tables) is treated as a security incident under [../40-incident-disaster-recovery/README.md](../40-incident-disaster-recovery/README.md).

## Related Documents

- [analytics-event-taxonomy.md](analytics-event-taxonomy.md) — defines what data exists to be governed
- [data-warehouse-architecture.md](data-warehouse-architecture.md) — where governed data lives
- [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md) — regulatory basis
- [../24-admin-panel/audit-log-spec.md](../24-admin-panel/audit-log-spec.md) — audit conventions this policy borrows
- [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md) — owner of Tier 3 data definitions
