---
title: "Api Versioning Strategy"
folder: 11-api
purpose: "Versioning policy"
pages_estimate: 2
prerequisites: "api-design-guidelines"
dependencies: "none"
priority: P1
audience: "BE, FE"
status: draft-v1
generated: 2026-07-15
---

# API Versioning Strategy

## Overview

This document defines how ActivityMate's REST API (see [api-design-guidelines.md](api-design-guidelines.md)) is versioned and how long a deprecated version stays supported once superseded.

## Context / Problem

Mobile clients cannot be force-upgraded the way a web app can — a user on an old Flutter app build may not update for weeks or months, especially on budget Android devices common among the target user base, and India's variable app-store update behavior means the backend cannot assume rapid client churn. The API contract must therefore support multiple client versions simultaneously without breaking users who haven't updated, while still letting the backend evolve.

## Options Considered

**URL-path versioning** (`/v1/activities`, `/v2/activities`) — explicit, visible in every request, trivially routable at the load-balancer or FastAPI-router level, and easy for engineers to reason about which version a given endpoint definition belongs to.

**Header-based versioning** (`Accept: application/vnd.activitymate.v2+json`) — keeps URLs clean, but is easy for API consumers to get wrong (forgotten header defaults silently to the wrong version), harder to test/debug from a browser or simple curl command, and adds friction for a small team without a dedicated API-platform function to enforce header discipline.

**No explicit versioning, additive-only changes forever** — simplest to start, but breaks down the first time a genuinely breaking change is needed (e.g. restructuring the activity-join response shape), forcing either a painful coordinated client+server release or silent breakage for users on old app builds.

## Recommendation & Rationale

URL-path versioning is adopted (`/v1/...` today), chosen for its visibility, routability, and low cognitive overhead for a small team — consistent with the general bias toward simplicity in [api-design-guidelines.md](api-design-guidelines.md). A new major version (`/v2/...`) is only introduced for genuinely breaking changes: removing a field, changing a field's type, or changing required-request-body shape. Additive, backward-compatible changes (new optional fields, new endpoints) ship within the existing version — this mirrors the additive-first migration philosophy in [../10-database/migration-strategy.md](../10-database/migration-strategy.md).

## Deprecation Policy

Once `/v2` ships, `/v1` enters a mandatory minimum **90-day deprecation window** before it can be removed, communicated via a `Deprecation` and `Sunset` HTTP header on every `/v1` response (per RFC 8594 conventions) so the Flutter client can surface an in-app update prompt. The 90-day figure is set specifically against expected mobile update latency in the target market rather than an arbitrary industry default — it is revisited if telemetry shows meaningful `/v1` traffic still present near the sunset date, in which case the window is extended rather than users being cut off. Removal of a deprecated version requires sign-off from both BE Lead and Mobile/FE Lead, confirming app-store rollout of the compatible client build has reached sufficient penetration.

## Related Documents

- [api-design-guidelines.md](api-design-guidelines.md)
- [openapi-spec-index.md](openapi-spec-index.md)
- [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md)
- [../10-database/migration-strategy.md](../10-database/migration-strategy.md)
