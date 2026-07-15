# 11-api/

**Phase:** Phase 3 — Architecture & Core Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** BE Lead  
**Depends on:** backend-services, database  
**Document count:** 6

## Purpose

The FastAPI contract between backend and every client — the Flutter app today, partner integrations later.

## Why this folder exists

A documented, versioned API contract is what lets frontend and backend teams build in parallel without constant coordination, and what makes a future partner API possible without a rewrite. Corresponds to PRD-012.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [api-design-guidelines.md](api-design-guidelines.md) | REST vs GraphQL | Compares REST with OpenAPI (simpler caching and tooling, faster to ship an MVP, and FastAPI generates OpenAPI specs natively) against GraphQL (better mobile bandwidth efficiency, but adds resolver complexity and a steeper learning curve for a small team). Recommends REST with OpenAPI for the MVP, revisiting GraphQL as a mobile backend-for-frontend layer only once client query patterns stabilize. | 5 | service-catalog | none | P0 | BE, FE |
| [api-versioning-strategy.md](api-versioning-strategy.md) | Versioning policy | URL-path versioning with defined deprecation-window commitments. | 2 | api-design-guidelines | none | P1 | BE, FE |
| [openapi-spec-index.md](openapi-spec-index.md) | Spec registry | Index into the machine-readable OpenAPI specs FastAPI generates per service. | 2 | api-design-guidelines | none | P0 | BE, FE, QA |
| [api-authentication-standards.md](api-authentication-standards.md) | Auth on the wire | Token format, refresh flow, and required headers — the API-layer half of the auth folder. | 3 | api-design-guidelines | auth | P0 | BE, FE, SEC |
| [webhook-standards.md](webhook-standards.md) | Outbound events | Payload signing and retry policy for future partner webhooks — venue partners, payment provider callbacks. | 3 | api-design-guidelines | payments-subscriptions | P2 | BE |
| [public-api-future-plan.md](public-api-future-plan.md) | Partner API roadmap | Scope for a future public API allowing venue partners to list activities — deliberately deferred past MVP. | 3 | api-versioning-strategy | none | P3 | BE, F |
