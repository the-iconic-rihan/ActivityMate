---
title: "Api Design Guidelines"
folder: 11-api
purpose: "REST vs GraphQL"
pages_estimate: 5
prerequisites: "service-catalog"
dependencies: "none"
priority: P0
audience: "BE, FE"
status: draft-v1
generated: 2026-07-15
---

# API Design Guidelines

## Overview

This document sets the API paradigm and general design conventions for ActivityMate's backend, built as a FastAPI modular monolith per [../07-architecture/system-architecture-overview.md](../07-architecture/system-architecture-overview.md) and [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md). It compares REST and GraphQL as the wire-level contract paradigm and lays out the conventions every endpoint must follow regardless of paradigm.

## Context / Problem

The Flutter mobile client is the only consumer of this API at launch, with partner integrations a post-PMF possibility (see [public-api-future-plan.md](public-api-future-plan.md)). The team is small, timelines are tight, and the API needs to let backend and frontend engineers build against a stable, documented contract in parallel without one blocking the other. The choice of paradigm affects tooling, caching, mobile bandwidth efficiency, and how steep the learning curve is for a team that has not previously built a GraphQL resolver layer.

## Options Considered

### Option A — REST with OpenAPI (adopted)

**Pros:** FastAPI generates OpenAPI 3.x specs natively from route and Pydantic-model definitions with zero extra tooling — see [openapi-spec-index.md](openapi-spec-index.md). HTTP-level caching (ETags, `Cache-Control`) works out of the box on well-designed REST endpoints, which matters for read-heavy views like the activity discovery feed. REST's request/response shape maps directly onto standard HTTP tooling — load balancers, API gateways, CDNs, monitoring dashboards — all of which already understand REST semantics without extra configuration. The mental model (resource + verb) is familiar to every engineer on the team from day one, which matters when the team is optimizing for shipping speed over architectural novelty.

**Cons:** Over-fetching and under-fetching are real costs — a mobile screen that needs an activity's summary plus its host's trust score plus the first three participants may require either multiple round trips or a bespoke aggregation endpoint. Versioning a changing resource shape requires deliberate discipline (see [api-versioning-strategy.md](api-versioning-strategy.md)).

### Option B — GraphQL

**Pros:** A single query can fetch exactly the nested fields a mobile screen needs in one round trip, which is meaningfully better for a mobile app on variable Indian mobile network conditions where every extra round trip has real latency cost. Strongly typed schema with introspection gives frontend engineers a self-documenting contract.

**Cons:** Adds a resolver layer and N+1 query risk that the team must actively manage (dataloader-style batching), which is nontrivial complexity to introduce well in a small team's first schema. HTTP-level caching mostly stops working — GraphQL typically serves everything over POST to a single endpoint, so the caching wins REST gets for free from browsers, CDNs, and Meilisearch-adjacent read paths have to be rebuilt at the application layer. Steeper learning curve with no team members having shipped a production GraphQL API before — a nontrivial ramp-up cost exactly during the phase where shipping speed matters most.

### Option C — gRPC / other RPC framework

**Pros:** Excellent performance, strong typing via protobuf, good for internal service-to-service calls if the monolith later splits into microservices.

**Cons:** Poor fit for a public-facing mobile client — browser/mobile HTTP tooling, debugging proxies, and most third-party SDKs assume REST or GraphQL, not gRPC, over the public internet. Rejected for the client-facing API; may be reconsidered for internal service-to-service calls only after the microservices migration described in [../07-architecture/scalability-plan-0-to-10m.md](../07-architecture/scalability-plan-0-to-10m.md), which is out of scope for this document.

## Recommendation & Rationale

REST with OpenAPI is adopted for the MVP and the foreseeable roadmap through PMF. The deciding factors are speed of delivery for a small team, native FastAPI/OpenAPI generation with no added tooling, and HTTP-level caching that meaningfully helps the highest-traffic read path (the activity discovery feed) for free. The over-fetching cost GraphQL would solve is judged smaller in practice than the resolver complexity and caching rebuild GraphQL would introduce, given the mobile screens in the current MVP scope are not deeply nested enough to make REST's round-trip cost prohibitive — most screens resolve to one or two well-designed endpoints (e.g. a `GET /activities/{id}` response that embeds a lightweight host summary rather than requiring a second call).

GraphQL is not rejected permanently. It is explicitly deferred as a possible **backend-for-frontend layer sitting in front of the REST services**, to be reconsidered once mobile client query patterns stabilize post-launch and if profiling shows over-fetching is a real, measured cost on the metered mobile connections common among the target user base — not before.

## Design Conventions (apply regardless of paradigm choice, binding now)

- **Resource-oriented URLs:** `/activities`, `/activities/{id}/participants`, `/users/{id}/trust-score` — nouns, not verbs; the HTTP method carries the verb.
- **Pagination:** cursor-based (`?cursor=...&limit=...`) for all list endpoints, not offset-based — offset pagination degrades under concurrent writes to a fast-changing resource like the activity feed, producing skipped or duplicated rows for the user.
- **Consistent envelope:** every response uses a fixed top-level shape (`data`, `meta`, `error`) so client-side parsing code does not special-case per endpoint.
- **Idempotency:** all POST endpoints that create a resource with side effects (joining an activity, submitting a report) accept an `Idempotency-Key` header, checked against a short-lived Redis key, to make client retries on flaky mobile networks safe.
- **Error format:** structured error responses with a stable machine-readable `error.code` plus a human-readable `error.message`, never a bare HTTP status with no body — the Flutter client needs to branch on error type, not just status code.
- **PII minimization in responses:** endpoints return only the fields the specific caller relationship justifies — consistent with the location-fuzzing precedent in [../10-database/geospatial-data-strategy.md](../10-database/geospatial-data-strategy.md), applied generally: a browse-view activity response never includes a host's phone number, for instance.

## Related Documents

- [openapi-spec-index.md](openapi-spec-index.md)
- [api-versioning-strategy.md](api-versioning-strategy.md)
- [api-authentication-standards.md](api-authentication-standards.md)
- [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md)
- [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md)
