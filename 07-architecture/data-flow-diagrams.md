---
title: "Data Flow Diagrams"
folder: 07-architecture
purpose: "Data flow"
pages_estimate: 5
prerequisites: "service-boundaries-domain-map"
dependencies: "database"
priority: P1
audience: "BE, DO"
status: draft-v1
generated: 2026-07-15
---

# Data Flow Diagrams

## Overview

This document traces three high-stakes user actions end to end across ActivityMate's modules, databases, and queues, so any engineer can answer "what actually happens, in what order, and what can fail" without reading the whole codebase. It builds directly on the bounded contexts in [service-boundaries-domain-map.md](service-boundaries-domain-map.md).

## Flow 1: Joining an Activity

A user taps "Join" on an activity created by another user.

```
1. Flutter app -> POST /activities/{id}/rsvp -> nginx -> FastAPI (Activities module)
2. Activities module:
   a. Reads activity row from Postgres (capacity, status, cutoff time) — FOR UPDATE row lock
   b. Checks capacity: if full -> writes to waitlist table, returns 202 Waitlisted
   c. If space available -> INSERT into rsvps (Postgres), same transaction as capacity check
   d. Commits transaction
3. On commit, Activities module publishes an "activity.joined" event to Redis queue
4. Background worker consumes event, fans out to:
   a. Trust module: records a pending-participation entry (used later for
      completion/no-show scoring) -> Postgres trust_events
   b. Chat module: adds the joining user to the activity's group chat room -> MongoDB
      room membership doc
   c. Notifications module: composes "X joined your activity" push -> FCM -> host's device
   d. Discovery module: increments a "popularity" counter used in ranking -> Redis
5. WebSocket gateway pushes a live UI update to the host if they have the activity
   screen open (bypasses push notification if already connected)
6. Response to joining user: 200 OK with updated activity + RSVP status
```

**Failure modes handled:** race condition on the last open spot (row lock in step 2a prevents overbooking); worker failure after commit (event is retried from queue, all downstream effects are idempotent — e.g., Chat room-add is a no-op if already a member).

## Flow 2: Trust Score Recalculation After Activity Completion

Triggered when a host marks an activity "completed," which is one of the core trust-signal-producing events.

```
1. Host taps "Mark Completed" -> Activities module updates activity.status = 'completed'
   in Postgres, records per-attendee outcome (attended / no-show) from host's manual
   check-off
2. Activities module publishes "activity.completed" event with attendee outcomes
3. Background worker (trust-score-recalc queue) consumes event:
   a. For each attendee: reads their current trust_events history from Postgres
   b. Recomputes weighted trust score using signals: completed activities count,
      average rating, report count, cancellation rate, response rate, account age
      (weights defined in ../13-trust-safety-fraud/trust-engine-architecture.md)
   c. Writes new trust_score row (append-only history) + updates the denormalized
      current-score column on the user row for fast reads
4. If a user's score crosses a defined threshold (e.g., into a "low trust" band):
   a. Notifications module alerts the user (transparency requirement, see
      ../13-trust-safety-fraud/trust-score-transparency-policy.md)
   b. Discovery module's ranking cache for that user is invalidated in Redis, so
      future feed requests reflect the new score immediately
5. Rating prompts are queued to send to all attendees 1 hour after completion
   (separate scheduled job, not part of the synchronous flow)
```

**Why async, not synchronous:** trust recalculation reads a user's full event history and applies a scoring function that may change independently of the request path (score weights are expected to be tuned frequently in the first months) — coupling it to the request/response cycle of "mark completed" would make that endpoint's latency depend on scoring-model complexity, which is the wrong coupling.

## Flow 3: Reporting a User (Safety-Critical Path)

This is the one flow in the system that is deliberately **not** fully async, because report ingestion itself must be fast and durable even if downstream processing is queued.

```
1. Flutter app -> POST /reports -> FastAPI (Moderation module)
2. Moderation module:
   a. Validates reporter has interacted with reported user (shared activity/chat)
   b. Writes report row to Postgres SYNCHRONOUSLY (not queued) — a report must never
      be lost to a dropped background job
   c. If report reason is in the high-severity set (e.g., harassment, safety threat)
      -> immediately also writes a MongoDB snapshot of recent chat context for
      investigator review, and sets a priority flag
3. Response returned to reporter immediately: 200 OK, "Report received"
4. Background worker picks up the report for the actual moderation workflow:
   a. Adds to admin moderation queue (../24-admin-panel/)
   b. If high-severity: also triggers an on-call alert per
      ../13-trust-safety-fraud/incident-response-playbook-trust.md
   c. Trust module receives the report as a pending signal (not yet weighted into
      score until an admin confirms it — unconfirmed reports must not be
      weaponizable to tank someone's score via false reports)
5. Admin action (ban, warn, dismiss) writes back to Moderation, which then notifies
   Trust to finalize the signal weighting only after human review
```

**Why the write is synchronous:** a report is evidence in a safety incident; queuing it introduces a window where the report could be lost (worker crash, queue backlog) at exactly the moment it matters most. Everything downstream of the durable write can be async.

## Related Documents

- [service-boundaries-domain-map.md](service-boundaries-domain-map.md)
- [system-architecture-overview.md](system-architecture-overview.md)
- [../08-backend-services/background-jobs-queue-architecture.md](../08-backend-services/background-jobs-queue-architecture.md)
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)
- [../15-activities-communities/activity-lifecycle-spec.md](../15-activities-communities/activity-lifecycle-spec.md)
- [../10-database/schema-design-erd.md](../10-database/schema-design-erd.md)
