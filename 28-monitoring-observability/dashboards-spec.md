---
title: "Dashboards Spec"
folder: 28-monitoring-observability
purpose: "Ops dashboards"
pages_estimate: 2
prerequisites: "observability-stack-evaluation"
dependencies: "none"
priority: P1
audience: "DO"
status: draft-v1
generated: 2026-07-15
---

# Dashboards Spec

## Overview

This document specifies the standard set of operational dashboards built in Datadog (per `observability-stack-evaluation.md`) that every on-call engineer and DevOps lead uses day-to-day. The goal is a small, opinionated set of dashboards that answer "is the system healthy" and "what's happening right now during an incident" — not an unbounded pile of ad hoc charts nobody maintains.

## Dashboard 1: System Overview (default landing dashboard)

The first thing anyone opens. One screen, glanceable in under 10 seconds:
- Overall API error rate (5xx %) and request volume, last 1h/24h/7d toggle.
- p50/p95/p99 latency across all endpoints, same time toggles.
- Health of each core dependency: RDS Postgres (connections, CPU, replica lag), ElastiCache Redis (memory, evictions), MongoDB (connections), MinIO (disk usage), Meilisearch (query latency).
- Current deploy version running in prod (SHA + release tag, pulled from the CI/CD metadata per `../27-cicd-release/cicd-pipeline-architecture.md`) — so "did this start after the last deploy" is answerable in one glance.
- Active SEV1/SEV2 alert count, linking directly into the alert list.

## Dashboard 2: Critical Path SLOs

One panel per SLO defined in `sla-slo-definitions.md` — authentication, safety check-in, chat delivery — each showing: current availability against its target, latency percentiles against target, and remaining error budget for the current 30-day window. This dashboard exists specifically so the error-budget policy in `sla-slo-definitions.md` has a visible home, not just a rule written in a document nobody checks.

## Dashboard 3: Trust & Safety Operational Health

Distinct from the SLO dashboard — this tracks the *business* signal, not just the technical one: trust-score computation job success rate and lag, report-submission volume and processing latency (feeds `../21-moderation-content-ops/`), verification pipeline throughput (phone/email/selfie/ID stages, per PRD-001 section 9), and check-in-to-emergency-alert latency distribution. Owned jointly by DevOps and Trust & Safety, reviewed together weekly — this is the dashboard that makes the "observability is a safety requirement" framing from the folder README concrete and checkable.

## Dashboard 4: Infrastructure & Cost

Host-level CPU/memory/disk across the app tier, NAT gateway data-processing volume (a known hidden-cost lever per `../25-infrastructure-cloud/cost-optimization-playbook.md`), and daily AWS spend trend against the budget thresholds defined there. Reviewed weekly by DevOps, monthly with Founders.

## Dashboard 5: Deploy & Release Activity

A timeline view: every deploy to dev/staging/prod (from `../27-cicd-release/cicd-pipeline-architecture.md`), every feature-flag ramp change (from `../27-cicd-release/feature-flag-strategy.md`), overlaid on the System Overview's error-rate and latency graphs. This is the primary tool for answering "did this regression correlate with a deploy or a flag ramp" without manually cross-referencing timestamps across separate systems.

## Dashboard Maintenance

Each dashboard has a single named owner responsible for keeping it accurate as services change (an owner list, not "DevOps" generically — diffuse ownership is how dashboards rot). Dashboards are defined as code (Datadog's Terraform provider, consistent with the IaC-everything approach in `../25-infrastructure-cloud/terraform-iac-standards.md`) rather than built ad hoc in the UI, so dashboard definitions are version-controlled and reviewable like any other infrastructure change.

## Related Documents

- [observability-stack-evaluation.md](observability-stack-evaluation.md)
- [sla-slo-definitions.md](sla-slo-definitions.md)
- [alerting-oncall-policy.md](alerting-oncall-policy.md)
- [../22-analytics-experimentation/north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md)
- [../25-infrastructure-cloud/cost-optimization-playbook.md](../25-infrastructure-cloud/cost-optimization-playbook.md)
