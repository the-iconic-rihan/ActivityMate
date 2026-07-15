---
title: "Error Handling Standards"
folder: 08-backend-services
purpose: "Error contracts"
pages_estimate: 3
prerequisites: "backend-coding-guidelines"
dependencies: "api"
priority: P0
audience: "BE, FE"
status: draft-v1
generated: 2026-07-15
---

# Error Handling Standards

## Overview

Every endpoint in the ActivityMate API returns errors in one shape, so the Flutter client can handle failures predictably without per-endpoint special-casing, and so future API consumers (partner integrations, internal tools) inherit the same contract for free.

## The Error Response Shape

```json
{
  "error": {
    "code": "ACTIVITY_FULL",
    "message": "This activity has reached its capacity.",
    "details": {
      "activity_id": "b3f1...",
      "capacity": 6,
      "current_rsvps": 6
    },
    "request_id": "req_7f3a9c2e"
  }
}
```

- `code`: a stable, machine-readable SCREAMING_SNAKE_CASE string. The Flutter client switches on this, never on `message`.
- `message`: a human-readable, English default string. Not localized server-side — the client maps `code` to a localized string where a translation exists, and falls back to `message` otherwise (see [../41-localization-expansion/README.md](../41-localization-expansion/README.md)).
- `details`: optional, structured, endpoint-specific context. Never contains secrets, stack traces, or internal identifiers beyond what the client legitimately needs.
- `request_id`: always present, correlates to backend logs for support/debugging — surfaced in bug reports and support tickets per [../34-operations-support/README.md](../34-operations-support/README.md).

## HTTP Status Code Mapping

| Status | Meaning | Example `code` values |
|---|---|---|
| 400 | Malformed request / validation failure | `VALIDATION_ERROR`, `INVALID_PHONE_FORMAT` |
| 401 | Missing/invalid/expired auth | `UNAUTHENTICATED`, `SESSION_EXPIRED` |
| 403 | Authenticated but not permitted | `FORBIDDEN`, `TRUST_SCORE_TOO_LOW`, `NOT_ACTIVITY_HOST` |
| 404 | Resource doesn't exist | `ACTIVITY_NOT_FOUND`, `USER_NOT_FOUND` |
| 409 | State conflict | `ACTIVITY_FULL`, `ALREADY_JOINED`, `DUPLICATE_REPORT` |
| 422 | Semantically invalid but well-formed | `RSVP_CUTOFF_PASSED` |
| 429 | Rate limited | `RATE_LIMITED` (see [rate-limiting-standards.md](rate-limiting-standards.md)) |
| 5xx | Server fault | `INTERNAL_ERROR` (message never leaks internals) |

## Implementation Pattern

A shared exception hierarchy in `app/core/exceptions.py`, caught by one global FastAPI exception handler — individual routes never build the error JSON by hand:

```python
# app/core/exceptions.py
class AppError(Exception):
    code: str = "INTERNAL_ERROR"
    status_code: int = 500
    message: str = "Something went wrong."

    def __init__(self, message: str | None = None, details: dict | None = None):
        self.message = message or self.message
        self.details = details or {}
        super().__init__(self.message)

class ActivityFullError(AppError):
    code = "ACTIVITY_FULL"
    status_code = 409
    message = "This activity has reached its capacity."

# app/main.py
@app.exception_handler(AppError)
async def app_error_handler(request: Request, exc: AppError):
    return JSONResponse(
        status_code=exc.status_code,
        content={"error": {
            "code": exc.code,
            "message": exc.message,
            "details": exc.details,
            "request_id": request.state.request_id,
        }},
    )
```

Every module's `exceptions.py` (per [backend-coding-guidelines.md](backend-coding-guidelines.md) file layout) defines its own `AppError` subclasses. A raw unhandled exception is caught by a catch-all handler, logged with full detail server-side, and returned to the client as a generic `INTERNAL_ERROR` — **the client never sees a Python traceback or SQL error text**, which is both a security requirement (no schema/implementation leakage) and a trust-brand requirement (a safety-first product cannot look broken to users mid-incident).

## Client-Side Contract (Flutter)

The Flutter client's networking layer (see [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md)) parses this shape into a typed `AppException` with a `code` enum, and UI code switches on the enum — never on raw strings — so a backend `message` wording change never silently breaks client behavior.

## Related Documents

- [backend-coding-guidelines.md](backend-coding-guidelines.md)
- [rate-limiting-standards.md](rate-limiting-standards.md)
- [../11-api/api-design-guidelines.md](../11-api/api-design-guidelines.md)
- [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md)
