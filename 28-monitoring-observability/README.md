# 28-monitoring-observability/

**Phase:** Phase 6 — Platform Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** DevOps / SRE  
**Depends on:** infrastructure-cloud  
**Document count:** 5

## Purpose

Logging, metrics, alerting, and on-call.

## Why this folder exists

Trust and safety features are only as good as the team's ability to notice when they fail silently — observability here is a safety requirement, not just an operations nicety.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [observability-stack-evaluation.md](observability-stack-evaluation.md) | Stack choice | Compares a managed stack (Datadog — fast setup, real cost at scale) against a self-hosted stack (Grafana, Prometheus, Loki — cheaper at volume, real setup and maintenance burden). Recommends a managed stack at MVP given team size, revisiting at the cost-crossover point. | 4 | infrastructure-cloud | none | P0 | DO |
| [logging-standards.md](logging-standards.md) | Logging standards | Structured logging format, with explicit PII-redaction rules given how much identity and trust data flows through logs. | 3 | observability-stack-evaluation | privacy-compliance-legal | P0 | BE, DO, SEC |
| [alerting-oncall-policy.md](alerting-oncall-policy.md) | Alerting & on-call | Alert severity tiers and on-call rotation — safety-incident alerts share the highest severity tier with infrastructure outages. | 4 | observability-stack-evaluation | incident-disaster-recovery | P0 | DO |
| [sla-slo-definitions.md](sla-slo-definitions.md) | SLOs | SLOs per critical path: authentication, safety check-in, chat delivery. | 3 | observability-stack-evaluation | none | P0 | DO, BE |
| [dashboards-spec.md](dashboards-spec.md) | Ops dashboards | Standard operational dashboards. | 2 | observability-stack-evaluation | none | P1 | DO |
