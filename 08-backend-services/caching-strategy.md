---
title: "Caching Strategy"
folder: 08-backend-services
purpose: "Cache layers"
pages_estimate: 4
prerequisites: "service-catalog"
dependencies: "database"
priority: P1
audience: "BE, DO"
status: draft-v1
generated: 2026-07-15
---

# Caching Strategy

## Overview

Redis serves two distinct roles in ActivityMate — ephemeral infrastructure state (sessions, rate-limit counters, job queues) and a **cache** over data whose source of truth is Postgres, MongoDB, or Meilisearch. This document covers the second role: what is cached, for how long, and how staleness is bounded so caching never becomes a source of trust/safety incorrectness.

## What Gets Cached

| Data | Cache Key Pattern | TTL | Invalidation Trigger |
|---|---|---|---|
| Discovery feed page (per user, per city) | `feed:{user_id}:{city}:{page}` | 60s | New activity created in city (partial invalidation of city-level feed keys), or on TTL expiry |
| Activity detail (public fields) | `activity:{activity_id}` | 30s | Explicit invalidation on any write to that activity (RSVP, edit, cancel) |
| Geo bucket lookup (activities within radius of a geohash cell) | `geo:{geohash}:{category}` | 45s | New activity in that geohash cell, or TTL expiry |
| User's current trust score (denormalized read) | `trust:{user_id}` | 120s | Explicit invalidation on trust recalculation job completion |
| Meilisearch hot query results (common filter combos) | `search:{query_hash}` | 30s | TTL expiry only (search freshness tolerance is higher than feed) |
| Session/auth token validation | `session:{token}` | matches session TTL (see [../12-auth/session-token-management.md](../12-auth/session-token-management.md)) | Explicit invalidation on logout/revoke |

Anything **not** in this table is not cached by default — trust/safety decision data (report status, moderation actions, ban status) is always read live from Postgres, never from cache, because a stale "not banned" read is a safety failure mode this product cannot accept.

## Invalidation Rules

Two invalidation strategies are used, chosen per data type:

1. **TTL-only** for data where a few seconds of staleness is an acceptable UX tradeoff and explicit invalidation would be complex for low benefit (search results, hot feed pages under normal load).
2. **Explicit invalidation + TTL as backstop** for data where staleness has a visible correctness cost (activity detail after a capacity change, trust score after recalculation) — the write path (in the owning module's `service.py`) is responsible for deleting the relevant cache key in the same code path as the DB write, with the TTL as a safety net in case an invalidation call is ever missed.

```python
# app/modules/activities/service.py
async def join_activity(self, activity_id: UUID, user_id: UUID) -> Rsvp:
    async with self.db.begin():
        rsvp = await self.repo.create_rsvp(activity_id, user_id)
    await self.cache.delete(f"activity:{activity_id}")
    await self.cache.delete_pattern(f"geo:*")  # bounded: only affects this geohash cell in practice
    return rsvp
```

## Cache-Versus-Source-of-Truth Consistency Tradeoffs

- **Read-through, not write-through.** ActivityMate does not write to cache and DB simultaneously as a single operation; the DB write commits first, then the cache is invalidated (never updated in place) so the next read repopulates it from source. This avoids the class of bugs where a cache write succeeds but the DB write later fails or rolls back.
- **Never cache the last mile of a safety decision.** Rate-limit counters and fraud signals (see [rate-limiting-standards.md](rate-limiting-standards.md)) are read live from Redis counters themselves (which *are* the source of truth for that specific data), not cached copies of a Postgres value — this distinction matters: Redis-as-source-of-truth for ephemeral counters is different from Redis-as-cache for Postgres-owned data, and this document only governs the latter.
- **Bounded staleness windows are documented per key**, not implicit — any engineer adding a new cached value must add a row to the table above in the same PR, stating the TTL and the accepted staleness window, reviewed for whether that staleness is safe for the specific data.

## Related Documents

- [service-catalog.md](service-catalog.md)
- [background-jobs-queue-architecture.md](background-jobs-queue-architecture.md)
- [rate-limiting-standards.md](rate-limiting-standards.md)
- [../10-database/database-selection-rationale.md](../10-database/database-selection-rationale.md)
- [../20-search-discovery/discovery-feed-algorithm.md](../20-search-discovery/discovery-feed-algorithm.md)
