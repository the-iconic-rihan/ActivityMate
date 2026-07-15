---
title: "Tech Stack Decision Record"
folder: 07-architecture
purpose: "Monolith vs microservices"
pages_estimate: 8
prerequisites: "system-architecture-overview"
dependencies: "none"
priority: P0
audience: "BE, CTO"
status: draft-v1
generated: 2026-07-15
---

# Tech Stack Decision Record

## Overview

This document ratifies the decision already stated in PRD-001 §12: ActivityMate's backend is a **modular monolith**, written in FastAPI, deployed as a single Docker Compose unit at launch, with domain boundaries pre-drawn so a future split into services is a refactor rather than a rewrite. It records the comparison that led there so the decision isn't relitigated every time a new engineer joins.

## Context / Problem

At the point this decision is made, ActivityMate has: no users yet, a small engineering team (expected single digits through MVP), an unvalidated product (activity categories, matching heuristics, and trust weighting are all expected to change weekly post-launch), and a hard trust/safety requirement that cannot tolerate architecture-induced delivery slowdown. The question is not "what architecture scales to 10 million users" — it is "what architecture lets a 3-6 person team ship a safety-critical MVP fast, and evolve it without a rewrite once it validates."

Two well-documented reference paths exist in industry and both are relevant precedents:

- **Uber's early microservices-first approach.** Uber split into services very early in its life, which gave strong team-level ownership and independent deploys, but is widely documented (including in Uber's own postmortems) as having produced enormous operational overhead — hundreds of services, cross-service debugging complexity, inconsistent standards — that a small team could not have absorbed without a large, dedicated infra org.
- **Shopify's documented modular monolith.** Shopify runs (and has publicly written about) a single Rails codebase with enforced internal module boundaries ("component-based architecture") deployed as one unit, splitting individual components into services only when a specific, measured bottleneck justifies it. This preserved single-team development velocity for years past the scale most companies use as an excuse to fragment early.

ActivityMate's starting conditions (small team, unvalidated product, safety-critical scope) match Shopify's starting conditions far more than Uber's hypergrowth trajectory.

## Options Considered

### Option A — Microservices from day one

Each domain (identity, trust, activities, chat, discovery, moderation) is its own deployable service with its own datastore, from the first commit.

**Pros:**
- Clean team ownership boundaries if the team ever grows to one team per domain.
- Independent scaling and independent deploy cadences per service.
- No risk of "modular monolith drift" (modules silently coupling over time).

**Cons:**
- Requires solving distributed transactions, service discovery, inter-service auth, and distributed tracing before a single MVP feature ships — none of which are product work.
- A 3-6 person team would spend a large fraction of early engineering time on infrastructure rather than the trust engine, activity lifecycle, or matching logic that actually differentiates the product.
- Local development requires running many services simultaneously — directly conflicts with fast MVP iteration.
- Premature service boundaries drawn without production traffic data are frequently wrong, and wrong service boundaries are far more expensive to fix (network-level API contracts, data migration across databases) than wrong module boundaries inside one codebase.

### Option B — Modular monolith (chosen)

One FastAPI deployable, internally split into modules (`identity/`, `trust/`, `activities/`, `chat/`, `discovery/`, `moderation/`, `notifications/`, `admin/`) with enforced import rules: modules talk to each other only through explicit interfaces (service classes / function calls), never by reaching into another module's ORM models directly.

**Pros:**
- One deploy pipeline, one set of logs, one process to debug locally — matches team size and stage.
- Enforced module boundaries (see [service-boundaries-domain-map.md](service-boundaries-domain-map.md)) mean the *seams* for a future split already exist; extraction becomes "move this module to its own repo and put a network call where a function call was," not a redesign.
- Cross-module transactions (e.g., joining an activity touches activities + trust + notifications) can use plain Postgres transactions instead of distributed saga patterns.
- Lower infra cost pre-revenue: one Postgres, one Redis, one deployment target in Docker Compose.

**Cons:**
- Requires discipline: without enforced boundaries (linting import rules, code review), a monolith can degrade into a "big ball of mud" faster than services can, since there's no network boundary forcing separation.
- A bug or resource leak in one module can, in principle, affect the whole process (mitigated by keeping modules stateless and by the background-worker split for heavy async work).
- Eventually, a single hot module (most likely chat/WebSocket or discovery) will need independent scaling that the monolith can't give it without over-provisioning the whole app.

### Option C — Monolith with no internal structure

A single FastAPI app with no enforced module boundaries — fastest to start, since there's no boundary-discipline overhead.

**Pros:**
- Zero upfront design cost.

**Cons:**
- Rejected outright: this is the path that makes a future split effectively a rewrite, which is the exact failure mode this document exists to avoid. The entire value of "modular monolith" over "monolith" is the module discipline; skipping it forfeits the benefit while keeping every monolith downside.

## Recommendation & Rationale

**Modular monolith (Option B)**, per PRD-001 §12, with these enforcement mechanisms from day one:

1. Directory-per-domain structure inside a single FastAPI app (detailed in [../08-backend-services/backend-coding-guidelines.md](../08-backend-services/backend-coding-guidelines.md)).
2. Import-linting (e.g., `import-linter` contracts in CI) that fails the build if module A imports module B's internal models instead of its public interface.
3. Each module owns its own Postgres schema/tables and exposes access only through its service layer — never raw cross-module SQL joins outside a module's own boundary, so a future logical-to-physical database split doesn't require a data model redesign.
4. Domain boundaries are drawn along Identity, Trust, Activities, Chat, Discovery, Moderation — see [service-boundaries-domain-map.md](service-boundaries-domain-map.md) — chosen because they are the seams with the least cross-talk in the product (a trust score change doesn't need to know about chat message content) and the most likely to need independent scaling later (chat/WebSocket load is structurally different from CRUD load).

This is a reversible-in-practice decision precisely because the boundaries are pre-drawn; it is not a bet that the monolith is correct forever, only that it is correct for the 0-to-PMF phase.

## Migration / Evolution Triggers

The monolith is not a permanent architecture. Concrete triggers for extracting a module into its own service (detailed further in [scalability-plan-0-to-10m.md](scalability-plan-0-to-10m.md)):

- A single module (most likely chat/WebSocket) requires a scaling profile (memory-per-connection, concurrent connections) that would force over-provisioning every other module if scaled together.
- A module's deploy cadence needs to decouple from the rest (e.g., the ML-driven matching/discovery module iterating multiple times a day while the identity module is stable).
- Team size crosses a threshold (roughly 2+ engineers dedicated full-time to a single domain) where deploy contention on one codebase becomes a measured velocity cost, not a hypothetical one.
- A specific module's resource profile (e.g., CPU-bound image moderation) is starving other request-serving workers on the same process pool.

None of these are calendar-based; they are measured, not scheduled, and each extraction should be logged as an ADR in [../39-decision-records-rfcs/adr-index.md](../39-decision-records-rfcs/adr-index.md).

## Related Documents

- [system-architecture-overview.md](system-architecture-overview.md)
- [service-boundaries-domain-map.md](service-boundaries-domain-map.md)
- [scalability-plan-0-to-10m.md](scalability-plan-0-to-10m.md)
- [../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md)
- [../08-backend-services/backend-coding-guidelines.md](../08-backend-services/backend-coding-guidelines.md)
- [../39-decision-records-rfcs/adr-index.md](../39-decision-records-rfcs/adr-index.md)
