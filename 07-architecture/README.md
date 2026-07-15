# 07-architecture/

**Phase:** Phase 3 — Architecture & Core Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** CTO / Principal Architect  
**Depends on:** product  
**Document count:** 7

## Purpose

System-level shape of the platform: service boundaries, scalability plan, vendor choices.

## Why this folder exists

Architecture decisions are the most expensive to reverse. This folder is where they are resolved once, in writing, with reasoning attached, ratifying and elaborating the stack already declared in PRD-001 §12 (FastAPI, Postgres, modular monolith, Docker Compose) rather than re-deciding it from a blank page.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [system-architecture-overview.md](system-architecture-overview.md) | Top-level diagram | The single reference diagram every engineer starts from: client apps, API gateway, core services, data stores, third parties. | 4 | product-strategy-doc | none | P0 | BE, FE, DO, SEC |
| [tech-stack-decision-record.md](tech-stack-decision-record.md) | Monolith vs microservices | Ratifies PRD-001's modular-monolith decision with the full comparison behind it: microservices-first (Uber's early approach — high velocity cost pre-PMF, ops overhead a small team can't absorb) versus a modular monolith (Shopify's documented approach — one deploy unit with enforced internal module boundaries, split into services only once a specific bottleneck proves it). Domain boundaries are pre-drawn along service-boundaries-domain-map so a future extraction is a refactor, not a rewrite. | 8 | system-architecture-overview | none | P0 | BE, CTO |
| [scalability-plan-0-to-10m.md](scalability-plan-0-to-10m.md) | Growth-stage plan | Explicit triggers for splitting the monolith, adding read replicas, introducing caching layers, and sharding — tied to concrete user/QPS thresholds rather than a fixed calendar. | 6 | tech-stack-decision-record | infrastructure-cloud | P1 | BE, DO, CTO |
| [multi-region-strategy.md](multi-region-strategy.md) | Region plan | A single India region at launch, since data residency and latency both favor it; documents the triggers and cost for expanding beyond India if the platform ever does. | 3 | scalability-plan-0-to-10m | infrastructure-cloud | P2 | DO, CTO |
| [service-boundaries-domain-map.md](service-boundaries-domain-map.md) | DDD bounded contexts | A domain-driven design map — Identity, Trust, Activities, Chat, Discovery — as the seams the monolith is modularized along, and the future microservice boundaries if it ever splits. | 6 | tech-stack-decision-record | backend-services | P0 | BE, CTO |
| [data-flow-diagrams.md](data-flow-diagrams.md) | Data flow | How a single user action, e.g. joining an activity, propagates across modules, the database, and queues. | 5 | service-boundaries-domain-map | database | P1 | BE, DO |
| [third-party-vendor-evaluation.md](third-party-vendor-evaluation.md) | Build vs buy | Maps provider (Google Maps vs Mapbox), SMS/OTP provider (India DLT-compliant options), and push notification provider, each scored on India coverage, cost at scale, and vendor lock-in risk. | 6 | system-architecture-overview | maps-location | P0 | BE, DO, F |
