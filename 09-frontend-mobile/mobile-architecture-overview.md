---
title: "Mobile Architecture Overview"
folder: 09-frontend-mobile
purpose: "App structure"
pages_estimate: 6
prerequisites: "service-boundaries-domain-map"
dependencies: "architecture"
priority: P0
audience: "FE, CTO"
status: draft-v1
generated: 2026-07-15
---

# Mobile Architecture Overview

## Overview

ActivityMate's Flutter client is structured to mirror the backend's domain map — [../07-architecture/service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md) — so a feature that touches "trust" or "activities" on the backend has one obvious corresponding place to touch on the client, and the two sides evolve along the same seams even though they are separate codebases and (per PRD-001) separate deploy cadences.

## Context

A generic Flutter starter structure (organize by `screens/`, `widgets/`, `models/`) works for small apps but fragments a single business feature across three unrelated top-level folders as the app grows — exactly the failure mode the backend's modular monolith design in [../07-architecture/tech-stack-decision-record.md](../07-architecture/tech-stack-decision-record.md) was built to avoid. ActivityMate's client instead organizes **by feature/domain first, by technical layer second**.

## Module Structure

```
lib/
  core/
    network/            # Dio HTTP client, interceptors, error mapping (see error-handling-standards.md)
    storage/              # local persistence (Hive/sqflite) for offline-capable data
    router/                # go_router configuration, deep-link handling
    theme/                  # design tokens from ../06-design-system/
    widgets/                 # shared, domain-agnostic UI primitives (buttons, inputs)
  features/
    identity/                # login (OTP), session, onboarding, profile editing
      data/                    # repositories, DTOs matching backend Pydantic schemas
      domain/                   # entities, use-cases
      presentation/              # screens, Riverpod providers/notifiers, widgets
    trust/                     # verification flow, trust score display, ratings UI
    activities/                 # browse, create, activity detail, RSVP, lifecycle UI
    chat/                        # message list, composer, WebSocket connection handling
    discovery/                    # feed, search, filters
    moderation/                    # report flow, block flow
    notifications/                  # in-app notification center, push permission handling
  app.dart                          # top-level MaterialApp/router wiring
  main.dart                          # entrypoint, DI container bootstrap
```

Each `features/<name>/` folder is the client-side counterpart to the equivalent backend module in [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md) — `features/trust/` calls the backend's Trust module's endpoints and owns no logic belonging to Identity or Activities, matching the backend's own interface discipline.

## Layer Discipline Within a Feature

- **`data/`** — talks to the network (`core/network`) and local storage, converts backend JSON into domain entities. Nothing above this layer knows about HTTP status codes or JSON shape.
- **`domain/`** — pure Dart, no Flutter/UI imports, no network imports. Business rules that are meaningful independent of how they're displayed (e.g., "an activity is joinable if status is open and capacity is not full") live here so they're unit-testable without a widget test harness.
- **`presentation/`** — screens, widgets, and Riverpod providers (see [state-management-strategy.md](state-management-strategy.md)). Depends on `domain/` and `data/`, never the reverse.

This is a lightweight take on clean/hexagonal architecture — not full DDD ceremony, but enough separation that business logic is testable without spinning up widgets, and that a backend API shape change is contained to the `data/` layer of one feature.

## Cross-Feature Communication

Features communicate through Riverpod providers exposed at the feature boundary (e.g., `activities` reads the current user's trust score via a provider exported from `trust/presentation/providers.dart`, never by reaching into `trust/data/` internals) — the same "public interface only" discipline as the backend's service-class boundary in [../07-architecture/service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md).

## Platform Targets

Android-first (per [../07-architecture/third-party-vendor-evaluation.md](../07-architecture/third-party-vendor-evaluation.md) and the Mumbai device-mix reality), with the codebase kept platform-agnostic from day one (no Android-only APIs without an abstraction) so iOS (P1) is a testing and store-submission effort, not a rewrite.

## Related Documents

- [state-management-strategy.md](state-management-strategy.md)
- [offline-first-sync-strategy.md](offline-first-sync-strategy.md)
- [navigation-deep-linking.md](navigation-deep-linking.md)
- [../07-architecture/service-boundaries-domain-map.md](../07-architecture/service-boundaries-domain-map.md)
- [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md)
- [../06-design-system/README.md](../06-design-system/README.md)
