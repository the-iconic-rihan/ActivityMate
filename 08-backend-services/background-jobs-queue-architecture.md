---
title: "Background Jobs Queue Architecture"
folder: 08-backend-services
purpose: "Async processing"
pages_estimate: 5
prerequisites: "service-catalog"
dependencies: "realtime-chat-notifications, moderation-content-ops"
priority: P1
audience: "BE, DO"
status: draft-v1
generated: 2026-07-15
---

# Background Jobs Queue Architecture

## Overview

Several ActivityMate flows (notification fan-out, trust score recalculation, moderation pipeline steps, search reindexing) must not run inline in a request/response cycle — per [../07-architecture/data-flow-diagrams.md](../07-architecture/data-flow-diagrams.md). This document specifies the queue technology, job patterns, and reliability guarantees for that async layer.

## Queue Choice

**Redis-backed queue via RQ (Redis Queue)** for MVP, not Celery, not a dedicated broker like RabbitMQ/Kafka.

**Reasoning:**
- Redis is already a required dependency (sessions, cache) — RQ adds zero new infrastructure, which matters directly for the modular-monolith, minimal-ops-overhead posture set in [../07-architecture/tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md).
- Celery is more feature-rich (complex routing, scheduling, multiple broker backends) but that complexity is unneeded at MVP scale and adds operational surface (a Celery beat scheduler, broker configuration tuning) a small team doesn't need yet.
- Kafka-class log-based streaming is the right tool once event volume and multi-consumer fan-out justify it (likely around [../07-architecture/scalability-plan-0-to-10m.md](../07-architecture/scalability-plan-0-to-10m.md) Stage 2-3) — evaluated again at that trigger, not before.
- RQ's job model (plain Python functions, simple retry/failure semantics, a lightweight dashboard) matches the team's FastAPI/Python-only stack with no new language or DSL to learn.

## Queue Topology

Named queues, not one shared queue, so priority and worker allocation can differ:

| Queue | Consumes | Priority | Worker Concurrency |
|---|---|---|---|
| `safety_critical` | Report high-severity alerts, moderation escalations | Highest — dedicated workers | 4+ always-on |
| `trust_recalc` | Trust score recalculation events | High | 2-4 |
| `notifications` | Push/in-app notification fan-out | Medium | 4-8 (bursty around evening peak) |
| `search_index` | Meilisearch reindex on activity/profile change | Medium | 2 |
| `moderation_pipeline` | Content scan, image moderation calls | Medium | 2-4 |
| `default` | Everything else (low-frequency housekeeping) | Low | 1-2 |

`safety_critical` is physically separated from `default` so a backlog of low-priority housekeeping jobs can never delay a safety escalation — a direct consequence of the trust-first product principle in PRD-001 §6.

## Job Pattern

```python
# app/modules/trust/tasks.py
from app.core.queue import job

@job(queue="trust_recalc", retry=3, retry_backoff=True)
def recalculate_trust_score(user_id: str) -> None:
    service = get_trust_service_sync()
    service.recalculate(user_id)

# app/modules/activities/service.py
async def mark_completed(self, activity_id: UUID) -> None:
    async with self.db.begin():
        activity = await self.repo.mark_completed(activity_id)
        attendee_ids = await self.repo.get_attendees(activity_id)
    for user_id in attendee_ids:
        recalculate_trust_score.enqueue(user_id=str(user_id))
```

- Jobs are enqueued only **after** the triggering database transaction commits — never inside it — to avoid a job running against data that gets rolled back.
- Every job is idempotent by design (safe to run twice): trust recalculation recomputes from source event history rather than incrementing, notification fan-out checks a delivery log before resending.
- Retries use exponential backoff, capped at 3 attempts for `trust_recalc`/`notifications`, capped at 5 for `safety_critical` with an additional dead-letter alert to on-call if all retries fail — a dropped safety job must always surface to a human.

## Reliability Guarantees

- **At-least-once delivery.** RQ jobs may run more than once (e.g., a worker crash after execution but before ack); idempotency is the mitigation, not exactly-once delivery infrastructure.
- **Dead-letter queue.** Failed jobs after max retries move to a `failed` registry (RQ built-in), monitored per [../28-monitoring-observability/README.md](../28-monitoring-observability/README.md), with `safety_critical` failures paging on-call directly.
- **No job loss on deploy.** Workers finish in-flight jobs before shutdown (graceful worker termination with a configured timeout) during CI/CD rolling deploys — see [../27-cicd-release/README.md](../27-cicd-release/README.md).

## Scheduled (Cron-like) Jobs

A small number of time-based jobs (rating-prompt send 1 hour post-activity, daily trust-score decay check for inactive accounts, nightly Meilisearch full reindex) run via RQ-Scheduler on top of the same Redis instance — not a separate cron infrastructure.

## Related Documents

- [service-catalog.md](service-catalog.md)
- [caching-strategy.md](caching-strategy.md)
- [../07-architecture/data-flow-diagrams.md](../07-architecture/data-flow-diagrams.md)
- [../16-realtime-chat-notifications/notification-system-architecture.md](../16-realtime-chat-notifications/notification-system-architecture.md)
- [../21-moderation-content-ops/README.md](../21-moderation-content-ops/README.md)
- [../28-monitoring-observability/README.md](../28-monitoring-observability/README.md)
