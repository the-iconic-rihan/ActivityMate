---
title: "Load Performance Testing Plan"
folder: 29-testing-qa
purpose: "Load testing"
pages_estimate: 3
prerequisites: "test-strategy"
dependencies: "infrastructure-cloud"
priority: P1
audience: "QA, DO"
status: draft-v1
generated: 2026-07-15
---

# Load & Performance Testing Plan

## Overview

ActivityMate's traffic is not flat — activity discovery and creation cluster sharply around specific windows (weekday evenings, and especially Friday–Sunday, per the PRD's emphasis on avoiding going alone for evening activities). This plan defines when and how we load-test, ahead of both routine releases and, more importantly, each new city launch, where a cold cache and an unproven traffic pattern combine into higher risk than steady-state Mumbai traffic.

## Context / Problem

Two failure modes are common for early-stage social apps: (1) never load-testing until a real spike causes an outage, at which point the cost is user trust and possibly a safety-relevant flow being unavailable at the exact moment (Friday night) it matters most; (2) over-investing in continuous large-scale load infrastructure before there is any traffic to justify it, which is wasted spend for a pre-PMF team. The plan needs to trigger load testing at the moments risk is actually concentrated — pre-launch and pre-spike — rather than running it continuously or not at all.

## Options Considered

1. **Continuous production load-shadowing at all times.** Rejected for MVP — infrastructure and engineering cost is not justified before multi-city scale; revisit post-PMF per [../38-planning-roadmap-techdebt](../38-planning-roadmap-techdebt/README.md).
2. **One-time load test before initial launch only.** Rejected — traffic patterns and data volume shift materially with each new city and each weekend-activity-window peak; a single pre-launch test goes stale fast.
3. **Recurring load test cadence tied to specific triggers: pre-city-launch, pre-major-release, and periodic weekend-peak simulation.** Selected.

## Recommendation & Rationale

**Trigger points for a load test run:**
- **Before every new city launch** (Mumbai first, then each subsequent city per [../41-localization-expansion](../41-localization-expansion/README.md)) — simulating expected signup surge plus steady-state usage at 3x the city's projected Month-1 user count, to catch capacity issues before real users hit them.
- **Before any release touching activity discovery, chat, or check-in services** — these are the services most exposed to weekend concurrency spikes.
- **Weekly synthetic weekend-peak simulation in staging** — replaying a synthetic Friday-6pm-to-Sunday-11pm traffic curve (activity browsing spikes early evening, chat and check-in spike at meetup time, ratings/reports spike late night) against staging, sized to current production peak plus 50% headroom.

**What is tested:**
- **API layer**: FastAPI endpoint latency (p50/p95/p99) and error rate under load, particularly activity search/discovery (Meilisearch-backed) and matching queries, per [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md).
- **Data layer**: PostgreSQL connection pool saturation, Redis cache hit rate under cold-start conditions (relevant specifically to new-city launches, where caches start empty), MongoDB document read/write throughput.
- **Real-time layer**: WebSocket/chat connection concurrency and message delivery latency under simultaneous meetup-hour load, per [../16-realtime-chat-notifications](../16-realtime-chat-notifications/README.md).
- **Safety-critical paths under load**: explicitly, the panic button and check-in flow are load-tested to confirm they do not degrade under peak load — a safety mechanism that works in isolation but times out during Saturday-night peak traffic is a failure, so these paths get a dedicated low-latency SLO target (see [../28-monitoring-observability/sla-slo-definitions.md](../28-monitoring-observability/sla-slo-definitions.md)) that is verified under load, not just at rest.

**Tooling and target environment.** Load tests run against a staging environment sized as a scaled-down replica of production topology (see [../25-infrastructure-cloud/environment-strategy.md](../25-infrastructure-cloud/environment-strategy.md)), using k6 or Locust scripts version-controlled alongside the services they test. Load tests never run against production directly for MVP — synthetic production monitoring (not full load injection) is the production-side signal, per [../28-monitoring-observability/observability-stack-evaluation.md](../28-monitoring-observability/observability-stack-evaluation.md).

**Pass/fail criteria.** A load test run blocks a city launch or release if: p95 latency on any safety-critical endpoint exceeds its SLO target under peak simulation, error rate exceeds 0.5% on any core flow, or any service exhausts its connection pool / memory budget before reaching the target concurrency.

## Related Documents

- [test-strategy.md](test-strategy.md)
- [../25-infrastructure-cloud/cost-optimization-playbook.md](../25-infrastructure-cloud/cost-optimization-playbook.md)
- [../28-monitoring-observability/sla-slo-definitions.md](../28-monitoring-observability/sla-slo-definitions.md)
