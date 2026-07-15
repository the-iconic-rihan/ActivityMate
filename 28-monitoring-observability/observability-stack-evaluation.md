---
title: "Observability Stack Evaluation"
folder: 28-monitoring-observability
purpose: "Stack choice"
pages_estimate: 4
prerequisites: "infrastructure-cloud"
dependencies: "none"
priority: P0
audience: "DO"
status: draft-v1
generated: 2026-07-15
---

# Observability Stack Evaluation

## Overview

ActivityMate is a trust-and-safety product before it is anything else: a matching feature that silently breaks is a bad bug, but a safety check-in flow that silently breaks is a user-harm risk. This document picks the logging, metrics, tracing, and alerting stack that sits underneath everything else in this folder, on the premise that observability here is a safety requirement, not an operations nicety.

## Context / Problem

The team is 2-4 DevOps/backend engineers at MVP, running a modular monolith on a handful of AWS instances in ap-south-1. The stack needs to cover: structured application logs (with PII redaction, per `logging-standards.md`), infrastructure and application metrics, distributed tracing across the FastAPI monolith's internal modules, and alert routing to on-call. It needs to be usable by a small team without a dedicated observability engineer, and its cost curve matters as much as its capability curve given the constraints in `../25-infrastructure-cloud/cost-optimization-playbook.md`.

## Options Considered

### Managed: Datadog
- **Pros:** Setup time measured in hours, not weeks — agent install plus a handful of integrations (RDS, ElastiCache, ECS/EC2) and logs/metrics/traces are flowing with sensible defaults. Unified UI across logs, metrics, APM traces, and alerting means no time spent stitching correlated views together by hand — directly valuable for a small team debugging a live incident under time pressure. Managed alerting, on-call integration (PagerDuty-compatible), and anomaly detection come out of the box. No infrastructure to run, patch, or scale ourselves — meaningful when the same 2-4 engineers are also running the product.
- **Cons:** Cost scales with hosts, custom metrics, and log volume, and becomes a real budget line at meaningful traffic — by industry reputation, one of the more expensive managed options at scale. Some lock-in to Datadog's query language and dashboard format if we ever migrate off.

### Self-hosted: Prometheus + Grafana + Loki
- **Pros:** No per-host or per-GB licensing cost — spend is just the compute to run the stack itself, which is cheap relative to Datadog at high log/metric volume. Full control over retention, cardinality, and data residency (relevant if a future compliance requirement demands logs never leave our own AWS account). No vendor lock-in; all open source.
- **Cons:** Real setup and ongoing maintenance burden — Prometheus scrape config, Loki log pipeline, Grafana dashboard-as-code, alertmanager routing, and keeping all four patched and scaled is itself an infrastructure project, on top of building the actual product. For a 2-4 person team pre-PMF, that's engineering time diverted from the product with no user-facing payoff. Correlating a trace, a log line, and a metric spike across three separate tools is manual compared to Datadog's unified view — costly exactly during an incident, when speed matters most.

## Recommendation & Rationale

**A managed stack (Datadog) at MVP**, with an explicit trigger to revisit at the cost-crossover point.

At current team size, engineering time spent running Prometheus/Loki/Grafana ourselves is engineering time not spent on trust-score logic, the matching engine, or safety features — the actual product. Datadog's unified correlation view also matters disproportionately for us specifically: when a safety check-in flow degrades, the team needs to go from alert to root cause in minutes, not by pivoting between three separate query languages. The cost premium of a managed stack at MVP scale (low thousands of DAU, a handful of hosts) is modest in absolute terms and small relative to the cost of a slow incident response on a safety-critical path.

**Revisit trigger:** when monthly Datadog spend exceeds roughly 15-20% of total infra spend, or crosses a fixed absolute threshold reviewed quarterly per `../25-infrastructure-cloud/cost-optimization-playbook.md` — at that point, with a larger team and a dedicated DevOps/SRE function, the self-hosted stack's maintenance burden is more affordable and its cost advantage starts to dominate. This is logged as a standing item in `../39-decision-records-rfcs/decision-log.md`, reviewed each quarter alongside the roadmap process in `../38-planning-roadmap-techdebt/quarterly-roadmap-process.md`.

## What This Covers

- **Logs:** Datadog Log Management, ingesting structured JSON logs per `logging-standards.md`, with PII-redaction applied before logs ever leave our VPC (redaction happens in the application's logging middleware, not trusted to Datadog's pipeline — see that document).
- **Metrics:** Datadog Agent on every EC2/ECS host, plus RDS/ElastiCache integrations for managed-service metrics, plus custom application metrics (activity-join success rate, trust-score computation latency) emitted via StatsD/DogStatsD from the FastAPI app.
- **Tracing:** Datadog APM, instrumented via `ddtrace`'s FastAPI integration, tracing requests through the monolith's internal module boundaries (per `../07-architecture/service-boundaries-domain-map.md`) — valuable specifically because module-boundary latency is where a modular monolith's "microservices in waiting" seams show up first.
- **Alerting:** Datadog Monitors feeding the severity tiers and routing defined in `alerting-oncall-policy.md`.

## Related Documents

- [logging-standards.md](logging-standards.md)
- [alerting-oncall-policy.md](alerting-oncall-policy.md)
- [sla-slo-definitions.md](sla-slo-definitions.md)
- [dashboards-spec.md](dashboards-spec.md)
- [../25-infrastructure-cloud/cost-optimization-playbook.md](../25-infrastructure-cloud/cost-optimization-playbook.md)
- [../25-infrastructure-cloud/cloud-provider-evaluation.md](../25-infrastructure-cloud/cloud-provider-evaluation.md)
