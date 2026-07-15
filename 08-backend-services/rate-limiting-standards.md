---
title: "Rate Limiting Standards"
folder: 08-backend-services
purpose: "Abuse throttling"
pages_estimate: 3
prerequisites: "service-catalog"
dependencies: "trust-safety-fraud, security"
priority: P0
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Rate Limiting Standards

## Overview

Flat, global rate limits are a weak defense for a trust-first product: a limit generous enough not to annoy a legitimate power user during a busy activity-planning weekend is also generous enough to let a fake-account operator do real damage before being throttled. ActivityMate's rate limits are instead **per-endpoint and signal-aware** — tied into the same fraud-detection and trust-score signals used elsewhere in the platform, so a low-trust or newly-created account is throttled tighter than a long-standing, highly-rated one.

## Two-Layer Design

1. **Coarse layer (nginx gateway).** A blunt, IP-based limit purely to protect infrastructure from volumetric abuse (e.g., 300 req/min per IP across all endpoints) — not security policy, just a circuit breaker.
2. **Fine layer (FastAPI app, per-endpoint, per-identity).** The actual policy layer, implemented as a FastAPI dependency backed by Redis counters, keyed by user ID (or phone number pre-auth) rather than IP, since IP is a weak identity signal on mobile networks (NAT, shared carrier IPs are common in India).

## Per-Endpoint Limits (Base Tier — Trust Score >= Median)

| Endpoint | Limit | Window | Rationale |
|---|---|---|---|
| `POST /auth/otp/request` | 3 | 10 min per phone number | OTP spam is a direct cost (SMS billing) and a harassment vector |
| `POST /auth/otp/verify` | 5 | 10 min per phone number | Bound brute-force guesses against a 4-6 digit OTP |
| `POST /activities` (create) | 10 | 24h per user | Bound spam/fake activity creation |
| `POST /activities/{id}/rsvp` | 20 | 1h per user | Generous — legitimate users browse and join multiple activities in a session |
| `POST /reports` | 10 | 24h per user | High enough for genuine safety needs, low enough to blunt coordinated false-report abuse |
| `POST /chat/messages` | 60 | 1 min per user | Generous for real conversation, catches spam bots |
| `POST /users/{id}/rate` | 5 | 1h per user | Ratings tied to real completed activities only; this bounds abuse of the rating endpoint itself |

## Signal-Aware Adjustment

Base-tier limits above apply to a user at or above the platform's median trust score. Limits scale down for lower-trust or newly-created accounts, and scale up modestly for consistently high-trust, long-tenured accounts:

```python
# app/core/rate_limit.py
def limit_for(user: User, base_limit: int) -> int:
    if user.account_age_days < 3 or user.trust_score < LOW_TRUST_THRESHOLD:
        return max(1, base_limit // 3)          # new/low-trust: strict
    if user.trust_score >= HIGH_TRUST_THRESHOLD and user.account_age_days > 90:
        return int(base_limit * 1.5)             # established, trusted: relaxed
    return base_limit
```

Concretely: a brand-new, unverified account attempting to create activities is capped far tighter than 10/day — this directly targets the fake-profile and spam risks named in PRD-001 §11, without penalizing the median genuine user.

## Implementation

```python
# app/core/rate_limit.py
from fastapi import Depends, HTTPException

def rate_limit(endpoint_key: str, base_limit: int, window_seconds: int):
    async def dependency(
        current_user: User = Depends(get_current_user),
        redis: Redis = Depends(get_redis),
    ):
        limit = limit_for(current_user, base_limit)
        key = f"ratelimit:{endpoint_key}:{current_user.id}"
        count = await redis.incr(key)
        if count == 1:
            await redis.expire(key, window_seconds)
        if count > limit:
            raise RateLimitedError(details={"retry_after_seconds": await redis.ttl(key)})
    return dependency

@router.post("/activities", dependencies=[Depends(rate_limit("create_activity", 10, 86400))])
async def create_activity(...): ...
```

`RateLimitedError` returns the standard error shape from [error-handling-standards.md](error-handling-standards.md) with `code: "RATE_LIMITED"` and a `retry_after_seconds` detail the Flutter client uses to show a countdown rather than a bare failure.

## Escalation Path

Repeated rate-limit hits are themselves a fraud signal, not just a throttle: exceeding a limit 3+ times in 24 hours across any endpoint pushes an event into the fraud-detection pipeline ([../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md)) for review, closing the loop between throttling and the broader trust engine rather than treating them as unrelated systems.

## Related Documents

- [service-catalog.md](service-catalog.md)
- [error-handling-standards.md](error-handling-standards.md)
- [../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md)
- [../30-security/threat-model-stride.md](../30-security/threat-model-stride.md)
- [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md)
