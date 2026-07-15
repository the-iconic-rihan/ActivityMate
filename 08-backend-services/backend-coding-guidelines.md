---
title: "Backend Coding Guidelines"
folder: 08-backend-services
purpose: "Code standards"
pages_estimate: 6
prerequisites: "service-catalog"
dependencies: "engineering-standards"
priority: P0
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Backend Coding Guidelines

## Overview

This is the concrete standard for how ActivityMate's FastAPI backend is structured and written, so the modular monolith decision in [../07-architecture/tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md) survives contact with a growing codebase instead of degrading into a "big ball of mud." It complements the language-agnostic conventions in [../37-engineering-standards/coding-style-guides.md](../37-engineering-standards/coding-style-guides.md) with FastAPI/Python specifics.

## Project Structure

Each module in [service-catalog.md](service-catalog.md) follows the same internal layout:

```
app/
  modules/
    activities/
      __init__.py
      router.py          # FastAPI APIRouter, HTTP layer only
      service.py          # ActivitiesService — the module's public interface
      models.py           # SQLAlchemy ORM models (private to this module)
      schemas.py          # Pydantic request/response models
      repository.py       # DB query layer, used only by service.py
      exceptions.py        # module-specific exception classes
      tasks.py            # background job task definitions (if any)
      tests/
        test_service.py
        test_router.py
  core/
    config.py              # Settings via pydantic-settings, env-driven
    database.py             # SQLAlchemy engine/session setup
    security.py              # shared auth dependency (JWT/session validation)
    redis.py
    exceptions.py             # base exception classes, shared error shape
  main.py                     # FastAPI app assembly, router registration
```

**Rule:** `router.py` never talks to `repository.py` or `models.py` directly — it calls `service.py`. `service.py` is the only file other modules are allowed to import from. This is what the import-linter contract enforces in CI.

## Dependency Injection Conventions

FastAPI's `Depends()` system is used consistently, not ad hoc:

```python
# core/database.py
async def get_db() -> AsyncIterator[AsyncSession]:
    async with async_session_factory() as session:
        yield session

# modules/activities/router.py
from fastapi import APIRouter, Depends
from app.core.security import get_current_user
from app.modules.activities.service import ActivitiesService, get_activities_service

router = APIRouter(prefix="/activities", tags=["activities"])

@router.post("/{activity_id}/rsvp", response_model=RsvpResponse)
async def join_activity(
    activity_id: UUID,
    current_user: User = Depends(get_current_user),
    service: ActivitiesService = Depends(get_activities_service),
) -> RsvpResponse:
    return await service.join_activity(activity_id, current_user.id)
```

- Every service class is provided via a `get_<name>_service()` factory function, never instantiated directly in a route — this makes tests trivial to write by overriding the dependency.
- Cross-module calls go through the *other* module's service factory the same way: `activities/service.py` depends on `Depends(get_trust_service)`, never on `trust/models.py`.

## Naming Conventions

- **Files/modules:** `snake_case`. **Classes:** `PascalCase`. **Functions/variables:** `snake_case`. Pydantic schema classes end in `Request`/`Response` (`CreateActivityRequest`, `ActivityResponse`), never bare nouns that collide with ORM model names.
- **ORM models** are named as singular nouns (`Activity`, `Rsvp`, `TrustEvent`) and live only in `models.py`; **Pydantic schemas** are the DTOs crossing the HTTP boundary and never leak ORM instances directly into a response — always map explicitly.
- **Service methods** are verb-first and describe intent, not CRUD mechanics: `join_activity()` not `create_rsvp()`, `recalculate_trust_score()` not `update_score()`.
- **Async everywhere.** Every route handler, service method, and repository method touching the DB, Redis, or network is `async def`. Sync code is only acceptable for pure in-memory computation (e.g., a scoring formula given already-fetched data).

## Configuration

All configuration is environment-variable driven via `pydantic-settings`, never hardcoded, never read from `os.environ` directly outside `core/config.py`:

```python
class Settings(BaseSettings):
    postgres_dsn: PostgresDsn
    redis_url: RedisDsn
    mongo_uri: str
    minio_endpoint: str
    meilisearch_url: str
    otp_provider_api_key: SecretStr

    model_config = SettingsConfigDict(env_file=".env", extra="forbid")
```

`extra="forbid"` is deliberate — an unrecognized env var should fail fast in development rather than be silently ignored.

## Testing Expectations

- Every module ships `tests/test_service.py` (business logic, mocked repository) and `tests/test_router.py` (HTTP contract, using FastAPI's `TestClient` against a test database). Full standard in [../29-testing-qa/automated-testing-standards.md](../29-testing-qa/automated-testing-standards.md).
- Service-layer tests must not hit a real Postgres/Redis/Mongo — use the repository interface as the mock boundary. Router-layer tests may use a real test-database transaction rolled back after each test.

## Migrations

- Alembic, one migration file per module's schema changes, autogenerated then hand-reviewed — never trust autogenerate blindly for column drops or type changes. Full policy in [../10-database/migration-strategy.md](../10-database/migration-strategy.md).

## Related Documents

- [service-catalog.md](service-catalog.md)
- [error-handling-standards.md](error-handling-standards.md)
- [../07-architecture/tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md)
- [../37-engineering-standards/coding-style-guides.md](../37-engineering-standards/coding-style-guides.md)
- [../37-engineering-standards/code-review-standards.md](../37-engineering-standards/code-review-standards.md)
- [../29-testing-qa/automated-testing-standards.md](../29-testing-qa/automated-testing-standards.md)
