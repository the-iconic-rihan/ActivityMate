# 08-backend-services/

**Phase:** Phase 3 — Architecture & Core Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** VP Engineering  
**Depends on:** architecture  
**Document count:** 6

## Purpose

Engineering standards for the FastAPI service layer: how backend code is written, structured, and operated day to day.

## Why this folder exists

Architecture says what the services are; this folder says how they're built in FastAPI specifically — the difference between a system diagram and code a team can actually maintain consistently.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [service-catalog.md](service-catalog.md) | Service registry | A living list of every module, its owner, and its dependencies — grows as the monolith is modularized. | 3 | service-boundaries-domain-map | none | P0 | BE, DO |
| [backend-coding-guidelines.md](backend-coding-guidelines.md) | Code standards | FastAPI project structure, dependency-injection conventions, naming conventions. | 6 | service-catalog | engineering-standards | P0 | BE |
| [error-handling-standards.md](error-handling-standards.md) | Error contracts | A consistent error response shape across every endpoint so the Flutter client and any future API consumers can handle failures predictably. | 3 | backend-coding-guidelines | api | P0 | BE, FE |
| [background-jobs-queue-architecture.md](background-jobs-queue-architecture.md) | Async processing | Queue choice and patterns for notification fan-out, moderation pipelines, and trust-score recalculation. | 5 | service-catalog | realtime-chat-notifications, moderation-content-ops | P1 | BE, DO |
| [caching-strategy.md](caching-strategy.md) | Cache layers | What is cached in Redis (discovery feed, geo lookups), invalidation rules, and the cache-versus-source-of-truth consistency tradeoffs. | 4 | service-catalog | database | P1 | BE, DO |
| [rate-limiting-standards.md](rate-limiting-standards.md) | Abuse throttling | Per-endpoint rate limits tied into fraud-detection signals rather than flat global limits. | 3 | service-catalog | trust-safety-fraud, security | P0 | BE, SEC |
