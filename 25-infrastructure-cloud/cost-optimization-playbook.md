---
title: "Cost Optimization Playbook"
folder: 25-infrastructure-cloud
purpose: "Cost control"
pages_estimate: 3
prerequisites: "environment-strategy"
dependencies: "none"
priority: P1
audience: "DO, F"
status: draft-v1
generated: 2026-07-15
---

# Cost Optimization Playbook

## Overview

ActivityMate is pre-Series-A: every dollar spent on infrastructure is a dollar of runway. This playbook sets concrete rules for reserved capacity, autoscaling, and cost alerting so that infra spend stays proportional to actual usage rather than growing by default.

## Baseline Sizing (MVP, single Mumbai launch)

At MVP scale (low thousands of DAU), the entire stack runs on modest, non-autoscaled infrastructure:

- **App tier:** 2x `t4g.medium` EC2 instances behind the ALB (Docker Compose per host until the ECS migration), not autoscaled at MVP — capacity is reviewed weekly against actual traffic rather than reacted to automatically, since automatic scale-out on unproven traffic patterns risks masking bugs (e.g., a retry storm) as "just add capacity."
- **RDS Postgres:** single `db.t4g.medium`, Multi-AZ in prod only (not dev/staging).
- **ElastiCache Redis:** single `cache.t4g.micro` node, no cluster mode until session/queue volume requires it.
- **MongoDB, MinIO, Meilisearch:** self-hosted on a single `t4g.medium` each in prod, co-located with headroom in dev/staging.

This baseline is intentionally under-provisioned relative to headroom — we scale up in response to observed load (via `../28-monitoring-observability/dashboards-spec.md` metrics), not in anticipation of it.

## Reserved Capacity

- **Trigger:** once a resource has run at a stable size for 60+ consecutive days with no planned change, convert it from on-demand to a 1-year Compute Savings Plan or RDS Reserved Instance. Do not reserve capacity earlier than that — pre-PMF traffic and architecture are too likely to change, and a 1-year commitment on a soon-to-be-resized instance destroys the savings.
- **Coverage target:** once stable, aim for 60-70% of baseline (non-autoscaled) compute under Savings Plans, leaving the autoscaled margin on-demand.
- **Review cadence:** quarterly, tied to the roadmap review in `../38-planning-roadmap-techdebt/quarterly-roadmap-process.md`.

## Autoscaling Thresholds

Autoscaling is introduced only after the ECS migration (post-PMF); at MVP, manual capacity review substitutes for it. When autoscaling is enabled, the starting thresholds are:

| Metric | Scale-out trigger | Scale-in trigger | Cooldown |
|---|---|---|---|
| App tier CPU | >65% avg over 5 min | <30% avg over 15 min | 5 min out / 10 min in |
| App tier p95 latency | >800ms over 5 min | — | 5 min |
| RDS connections | >70% of max_connections | — | manual review, not auto |
| Redis memory | >75% used | — | manual review, not auto |

Database and cache scaling stay manual-trigger even post-autoscaling-adoption on the app tier — resizing a stateful data store is a higher-risk operation than adding a stateless app instance, and merits a human decision.

## Cost Alerting

- AWS Budgets configured per environment: dev + staging combined budget alert at ₹15,000/month (80% and 100% thresholds notify DevOps Slack); prod budget alert at ₹60,000/month at MVP scale, revised each quarter against actuals.
- A daily Cost Explorer anomaly-detection alert flags any single-day spend >150% of the trailing 7-day average — catches runaway resources (e.g., a forgotten large instance, an infinite retry loop driving NAT gateway data-processing charges) fast rather than at month-end billing.
- All resources are tagged `project=activitymate`, `environment=<env>`, `owner=devops` (enforced by the Terraform module convention in `terraform-iac-standards.md`) so Cost Explorer can break spend down by environment and catch a staging environment quietly costing as much as prod.

## Specific Levers, in Priority Order

1. **Right-size before reserving.** Never reserve an instance class chosen under time pressure without a load-test-informed sizing pass.
2. **Turn off non-prod outside working hours.** Dev and staging app-tier instances (not RDS, to avoid backup-window complications) are stopped 9pm-8am IST and weekends via a scheduled Lambda, cutting non-prod compute cost by roughly 60%.
3. **S3/MinIO storage-class discipline.** Media older than 90 days with no recent access (per `../18-media-storage/` retention rules) moves to Infrequent Access; nothing sits in Standard indefinitely by default.
4. **NAT gateway data-processing awareness.** NAT gateway per-GB data-processing charges are a common hidden cost; the single-NAT design in `networking-vpc-architecture.md` is monitored specifically for this line item.
5. **Startup credits first.** AWS Activate credits (per `cloud-provider-evaluation.md`) are applied before any Savings Plan purchase — never buy a 1-year commitment against credit-covered spend.

## Related Documents

- [cloud-provider-evaluation.md](cloud-provider-evaluation.md)
- [environment-strategy.md](environment-strategy.md)
- [../28-monitoring-observability/dashboards-spec.md](../28-monitoring-observability/dashboards-spec.md)
- [../38-planning-roadmap-techdebt/capacity-planning.md](../38-planning-roadmap-techdebt/capacity-planning.md)
