# 09-frontend-mobile/

**Phase:** Phase 3 — Architecture & Core Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** Mobile / FE Lead  
**Depends on:** architecture, ux, design-system  
**Document count:** 7

## Purpose

Client application architecture for the Flutter app every user touches — corresponds to PRD-013 in the root PRD's own roadmap.

## Why this folder exists

Frontend architecture decisions (state management, offline handling, navigation) are as consequential as backend ones but are routinely left undocumented until the codebase ossifies around ad hoc choices made under deadline pressure. This folder fixes that early, consistent with the Flutter commitment already made in PRD-001.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [mobile-architecture-overview.md](mobile-architecture-overview.md) | App structure | Flutter app module structure, mirroring the backend's domain map (identity, trust, activities, chat) so frontend and backend evolve along the same seams. | 6 | service-boundaries-domain-map | architecture | P0 | FE, CTO |
| [state-management-strategy.md](state-management-strategy.md) | State management choice | Compares Riverpod (compile-safe, testable, steeper learning curve), Bloc (mature and widely documented, more boilerplate), and Provider (simplest, weaker at scale). Recommends Riverpod for compile-time safety and testability, given that real-time chat, location, and trust state must all be live in the UI simultaneously without state bugs. | 5 | mobile-architecture-overview | none | P0 | FE |
| [offline-first-sync-strategy.md](offline-first-sync-strategy.md) | Offline policy | What must work offline (viewing joined activities, drafting messages) versus what requires connectivity (safety check-in, verification). A blanket offline-first approach would be unsafe for check-in-dependent features, so this is a deliberate per-feature policy, not a default. | 5 | mobile-architecture-overview | trust-safety-fraud | P0 | FE, PM |
| [navigation-deep-linking.md](navigation-deep-linking.md) | Navigation spec | App navigation structure and deep-link handling for activity invites and shares. | 3 | mobile-architecture-overview | ux | P1 | FE, UX |
| [push-notification-client-handling.md](push-notification-client-handling.md) | Client push handling | Client-side handling of push payloads and foreground/background behavior. | 3 | mobile-architecture-overview | realtime-chat-notifications | P1 | FE |
| [performance-battery-budget.md](performance-battery-budget.md) | Performance budget | An explicit performance and battery budget given that the target device mix skews toward mid- and low-end Android in India — location tracking and real-time chat are the two biggest risk areas and get explicit budgets. | 4 | mobile-architecture-overview | testing-qa | P1 | FE, QA |
| [ci-mobile-release-pipeline.md](ci-mobile-release-pipeline.md) | Release pipeline | App store and Play Store release pipeline, versioning scheme, and staged rollout policy. | 3 | mobile-architecture-overview | cicd-release | P0 | FE, DO |
