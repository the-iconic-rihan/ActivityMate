---
title: "Coding Style Guides"
folder: 37-engineering-standards
purpose: "Style guides"
pages_estimate: 6
prerequisites: "none"
dependencies: "none"
priority: P0
audience: "BE, FE"
status: draft-v1
generated: 2026-07-15
---

# Coding Style Guides

**Status:** Draft v1.

## Scope

Per-language and per-framework style guides.

## Why one document for both stacks

ActivityMate ships two codebases — a FastAPI/Python backend and a Flutter/Dart mobile app (per [tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md)) — and a mobile or infra engineer should be able to find both style guides in one place rather than digging through backend-specific documentation, per this folder's stated purpose in [README.md](README.md). This document is the single style reference; [backend-coding-guidelines.md](../08-backend-services/backend-coding-guidelines.md) covers backend *architectural* conventions (service boundaries, background jobs) and defers pure style questions here.

---

## Part 1 — Python / FastAPI Style Guide

### 1.1 Baseline

- **PEP 8** is the floor, enforced by `ruff` (lint) and `black` (format) in pre-commit and CI — no manual style debates in review; if `ruff`/`black` don't flag it, it's a judgment call, not a violation.
- **Type hints are mandatory** on all function signatures, including internal helpers. `mypy` (or `pyright`) runs in CI in strict mode for `app/` (application code); test code may be looser.
- Python 3.11+ features (e.g. `match` statements, `X | Y` union syntax) are allowed and encouraged over older equivalents.

### 1.2 Project structure convention

```
app/
  domains/
    activities/
      router.py       # FastAPI route definitions only — no business logic
      service.py       # business logic
      schemas.py        # Pydantic request/response models
      models.py          # SQLAlchemy ORM models
      repository.py     # DB query layer
    trust/
    chat/
    ...
  core/
    config.py
    security.py
    db.py
  main.py
```

Each domain module mirrors the service boundaries in [service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md) — this is deliberate, so that extracting a domain into its own microservice post-PMF (per [scalability-plan-0-to-10m.md](../07-architecture/scalability-plan-0-to-10m.md)) is a lift-and-shift, not a redesign.

### 1.3 Routers stay thin

```python
# Good — router.py delegates to service, returns typed response
@router.post("/activities", response_model=ActivityOut, status_code=201)
async def create_activity(
    payload: ActivityCreate,
    current_user: User = Depends(get_current_user),
    service: ActivityService = Depends(get_activity_service),
) -> ActivityOut:
    activity = await service.create_activity(user=current_user, data=payload)
    return ActivityOut.model_validate(activity)
```

```python
# Bad — business logic, DB access, and error handling inline in the route
@router.post("/activities")
async def create_activity(payload: dict, db: Session = Depends(get_db)):
    if payload["category"] not in ["coffee", "cycling", "trek"]:  # magic strings
        raise HTTPException(400, "bad category")
    activity = Activity(**payload)
    db.add(activity)
    db.commit()
    return activity  # leaks ORM model directly to the client
```

Routers never touch the database session or business rules directly — this keeps trust-relevant logic (e.g. verification-level gating on who can create paid activities) in one testable place (`service.py`), not scattered across route handlers.

### 1.4 Pydantic schemas, not raw dicts, at every boundary

All request and response bodies use Pydantic models (`schemas.py`). Never accept or return a raw `dict` in a route signature — it defeats the type checker and OpenAPI generation that [openapi-spec-index.md](../11-api/openapi-spec-index.md) depends on.

### 1.5 Naming conventions

- Modules and files: `snake_case`
- Classes: `PascalCase` (`ActivityService`, `TrustScoreCalculator`)
- Functions/variables: `snake_case`
- Constants: `UPPER_SNAKE_CASE`
- Pydantic schema suffixes: `*Create`, `*Update`, `*Out` (e.g. `ActivityCreate`, `ActivityOut`) — never reuse a schema for both request and response even if fields overlap today, since they tend to diverge.

### 1.6 Error handling

Domain-specific exceptions (e.g. `ActivityNotFoundError`, `InsufficientTrustLevelError`) are raised in `service.py` and translated to HTTP responses by a single centralized exception handler registered in `main.py` — never `raise HTTPException` from inside a service function, which would couple business logic to the transport layer. Full standard in [error-handling-standards.md](../08-backend-services/error-handling-standards.md).

### 1.7 Async discipline

All I/O-bound code (DB queries, external API calls to verification providers, Redis) uses `async`/`await` consistently — no blocking calls inside an `async def` route or service method. CPU-bound work (e.g. image processing for selfie verification) is offloaded to a background worker, not run inline in the request path.

### 1.8 Docstrings and comments

Public service methods get a one-line docstring stating intent, not restating the function name. Comments explain *why*, not *what* — `# increment attempt counter` is noise; `# capped at 5 to prevent OTP brute-force per rate-limiting-standards.md` earns its place.

---

## Part 2 — Dart / Flutter Style Guide

### 2.1 Baseline

- Follows the official [Effective Dart](https://dart.dev/effective-dart) guidelines, enforced via `dart analyze` with a strict `analysis_options.yaml` (no `implicit-dynamic`, no unused imports) in CI.
- `dart format` runs in pre-commit — same principle as `black` on the backend: no manual formatting debates.

### 2.2 Project structure convention

```
lib/
  features/
    activities/
      presentation/       # widgets, screens
      application/       # state management (Riverpod/Bloc providers)
      domain/              # models, entities
      data/                # repositories, API clients
    trust/
    chat/
  core/
    network/
    theme/
    widgets/            # shared design-system components
```

Feature-first structure (not layer-first at the top level) so a mobile engineer working on "activities" or "trust" touches one folder, mirroring the backend's domain-first structure in Part 1.3 — the two codebases should feel like siblings, not unrelated projects.

### 2.3 Widget conventions

```dart
// Good — small, single-responsibility widget, const where possible
class TrustBadge extends StatelessWidget {
  const TrustBadge({super.key, required this.level});

  final VerificationLevel level;

  @override
  Widget build(BuildContext context) {
    return Semantics(
      label: 'Verification level: ${level.label}',
      child: Chip(
        avatar: Icon(level.icon, size: 16),
        label: Text(level.label),
        backgroundColor: level.color.withOpacity(0.12),
      ),
    );
  }
}
```

```dart
// Bad — logic-heavy build method, no semantics, magic values
Widget build(BuildContext context) {
  Color c;
  if (level == 0) c = Colors.grey;
  else if (level == 1) c = Colors.blue;
  else c = Colors.green;
  return Container(color: c, child: Text(level.toString()));
}
```

Trust-signal widgets (badges, verification indicators, ratings) always include a `Semantics` label — these are safety-relevant UI elements and must be screen-reader accessible, not just visually clear, consistent with the accessibility commitments in [06-design-system](../06-design-system/README.md).

### 2.4 State management

A single state-management approach is used consistently across the app (Riverpod, per [tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md) if adopted there — this document defers the final choice to that ADR and should be updated to match once decided). Widgets are never directly aware of API clients — they read from providers/controllers, which call `data/repository.dart` classes.

### 2.5 Naming conventions

- Files: `snake_case.dart`
- Classes/widgets: `PascalCase`
- Variables/methods: `camelCase`
- Private members: leading underscore (`_activityController`)
- Booleans read as questions or assertions: `isVerified`, `hasCompletedActivity`, never `verified` or `flag1`

### 2.6 Null safety and immutability

All models are immutable (`final` fields, `copyWith` for updates). No use of `!` (null-assertion operator) without a preceding explicit null check or a comment justifying why it's provably non-null — silent force-unwraps are a common crash source in production Flutter apps and are treated as a review blocker.

### 2.7 Testing expectations

Every feature ships with widget tests for its core screens and unit tests for its providers/controllers, per [29-testing-qa](../29-testing-qa/README.md). Golden tests are used for trust-signal components (badges, verification states) specifically, since visual regressions there are a trust-integrity issue, not just a cosmetic one.

---

## Enforcement

Both style guides are enforced automatically in CI (lint + format + type-check gates) per [cicd-pipeline-architecture.md](../27-cicd-release/cicd-pipeline-architecture.md) — a PR that fails these gates cannot be merged regardless of reviewer approval. Style *review comments* in PRs should be rare, since the tooling catches most of what a human would otherwise flag; human review time is reserved for logic, architecture, and trust/safety correctness per [code-review-standards.md](code-review-standards.md).

## Maintenance

Owned by the CTO. Proposed changes go through a lightweight RFC in [39-decision-records-rfcs](../39-decision-records-rfcs/README.md) if they affect enforced lint rules (to avoid every engineer having a different opinion churn the config); pure documentation clarifications can be a direct PR to this file.
