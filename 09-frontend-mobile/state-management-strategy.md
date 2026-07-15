---
title: "State Management Strategy"
folder: 09-frontend-mobile
purpose: "State management choice"
pages_estimate: 5
prerequisites: "mobile-architecture-overview"
dependencies: "none"
priority: P0
audience: "FE"
status: draft-v1
generated: 2026-07-15
---

# State Management Strategy

## Overview

ActivityMate's Flutter client uses **Riverpod** for state management. This document records the comparison behind that choice, since state management is one of the highest-blast-radius decisions in a Flutter codebase — retrofitting a different approach after dozens of screens exist is a significant rewrite.

## Context / Problem

ActivityMate's UI has an unusually demanding concurrency profile for a "social app": a single screen can need trust score, live chat, and location state simultaneously and correctly. Example — the activity detail screen shows the host's live trust score badge (can change while viewing), a live "who's joined" list (WebSocket-driven), and a "how far away" distance (device location-driven), all on screen at once, all needing to update independently without triggering unrelated rebuilds or getting into a stale/inconsistent state relative to each other. Getting this wrong produces exactly the class of bug that erodes trust in a trust-first product: a chat message that appears sent but wasn't, a trust badge that's one state behind reality.

## Options Considered

### Option A — Provider

Flutter's original recommended lightweight state management approach, built on `InheritedWidget`.

**Pros:** Simplest mental model, minimal boilerplate, easiest onboarding for a new Flutter developer.
**Cons:** No compile-time safety on provider lookup (`Provider.of<T>` fails at runtime if `T` isn't found, not at compile time); testing requires widget-tree scaffolding since providers are tied to `BuildContext`; documented and generally acknowledged (including by Provider's own maintainer, who created Riverpod as its successor) to weaken at scale — specifically around avoiding unnecessary rebuilds and managing many interdependent providers, which is exactly ActivityMate's activity-detail-screen problem above.

### Option B — Bloc

A mature, widely-adopted pattern built around explicit `Event -> Bloc -> State` streams, with strong community tooling and documentation.

**Pros:** Very mature ecosystem, strict unidirectional data flow is easy to reason about and easy to onboard engineers who've used it elsewhere, excellent for complex multi-step flows (e.g., the verification/KYC flow in [../13-trust-safety-fraud/user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md) maps naturally to Bloc's event-driven state machine style).
**Cons:** Meaningfully more boilerplate per feature (event classes, state classes, bloc classes, all before any UI code) — a real velocity cost for a small team building many CRUD-shaped screens (activity create/edit, profile edit) that don't need explicit event-sourcing-style modeling. Combining multiple Blocs' state in one screen (the activity-detail multi-source problem) requires `BlocListener`/`MultiBlocProvider` composition that gets verbose fast.

### Option C — Riverpod (chosen)

A compile-safe evolution of Provider, decoupled from `BuildContext`, with first-class support for combining/depending on multiple providers.

**Pros:**
- **Compile-time safety.** Provider lookups are checked by the Dart analyzer, not discovered as runtime crashes — directly reduces a class of bug in a codebase with many interdependent state sources.
- **Testability without widget scaffolding.** Providers can be instantiated and tested with `ProviderContainer` in plain Dart tests, no `BuildContext` required — matches the domain-layer testability goal in [mobile-architecture-overview.md](mobile-architecture-overview.md).
- **Native support for combining state sources.** `ref.watch()` across multiple providers is exactly the pattern the activity-detail screen needs: watch trust score provider, chat/presence provider, and location provider independently, each rebuilding only the widget subtree that depends on it (fine-grained rebuilds via `ProviderScope`/`Consumer` boundaries), avoiding both stale state and wasteful full-screen rebuilds.
- **`AsyncNotifier`/`StreamProvider`** map cleanly onto the two async state shapes ActivityMate needs everywhere: request/response (REST calls) and live streams (WebSocket chat/presence).

**Cons:**
- Steeper learning curve than Provider for developers new to it; more concepts (`Provider`, `NotifierProvider`, `AsyncNotifierProvider`, `StreamProvider`, `ref.watch` vs `ref.read` vs `ref.listen`) to internalize up front.
- Code-generation variant (`riverpod_generator`) adds a build_runner step to the dev loop, which is a minor friction cost weighed against the type-safety benefit.

## Recommendation & Rationale

**Riverpod**, specifically the code-generation (`@riverpod`) annotation style for new code, because:

1. The compile-time safety directly reduces the runtime-crash risk class that matters most for a trust product — a crash on the chat or check-in screen is a safety-adjacent failure, not just an annoyance.
2. The multi-source-state problem (trust + chat + location live on one screen) is Riverpod's core design strength, and is a real, everyday pattern in this app, not a hypothetical.
3. Testability without `BuildContext` matches the layered architecture in [mobile-architecture-overview.md](mobile-architecture-overview.md), where `domain/` logic must be unit-testable in isolation.
4. Bloc's boilerplate cost is real but was judged less important than the above three factors for a small team that needs to move fast across many CRUD-shaped screens, not just complex state machines.

## Conventions

- One `@riverpod` provider file per feature, exported from `features/<name>/presentation/providers.dart` as the feature's public state interface (see cross-feature communication rules in [mobile-architecture-overview.md](mobile-architecture-overview.md)).
- `AsyncNotifier` for anything backed by a REST call with loading/error/data states (mapped directly onto the error shape in [../08-backend-services/error-handling-standards.md](../08-backend-services/error-handling-standards.md)).
- `StreamProvider` for WebSocket-driven state (chat messages, live presence, live activity status).
- `ref.watch` in build methods (reactive), `ref.read` in callbacks/event handlers (one-shot), `ref.listen` for side effects (navigation, snackbars) — this distinction is enforced in code review per [../37-engineering-standards/code-review-standards.md](../37-engineering-standards/code-review-standards.md).

## Migration/Evolution Triggers

Re-evaluate only if Riverpod itself becomes unmaintained (no realistic near-term signal of this) or if a specific, measured performance problem is traced to Riverpod's rebuild model rather than to app-level misuse (e.g., overly broad `ref.watch` scopes) — the first response to a rebuild-performance issue is fixing provider scoping, not switching frameworks.

## Related Documents

- [mobile-architecture-overview.md](mobile-architecture-overview.md)
- [offline-first-sync-strategy.md](offline-first-sync-strategy.md)
- [../08-backend-services/error-handling-standards.md](../08-backend-services/error-handling-standards.md)
- [../16-realtime-chat-notifications/chat-architecture.md](../16-realtime-chat-notifications/chat-architecture.md)
