---
title: "Api Authentication Standards"
folder: 11-api
purpose: "Auth on the wire"
pages_estimate: 3
prerequisites: "api-design-guidelines"
dependencies: "auth"
priority: P0
audience: "BE, FE, SEC"
status: draft-v1
generated: 2026-07-15
---

# API Authentication Standards

## Overview

This document specifies the wire-level mechanics of authentication on ActivityMate's REST API — token format, required headers, and the refresh flow — as the API-layer counterpart to the identity decisions made in [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md) and [../12-auth/session-token-management.md](../12-auth/session-token-management.md). Those documents decide *what* proves identity and *how long* a session lasts; this document specifies *how that proof travels on every request*.

## Token Format

Every authenticated request carries a **JWT access token** in the `Authorization: Bearer <token>` header. The access token is short-lived (15 minutes, per [../12-auth/session-token-management.md](../12-auth/session-token-management.md)) and signed with an asymmetric key (RS256) so that any backend module in the modular monolith can verify it locally without a round trip to the auth module — important for keeping request latency low across a service that may later split into microservices. The JWT payload carries `sub` (user ID), `role` (per [../12-auth/authorization-rbac-model.md](../12-auth/authorization-rbac-model.md)), `trust_tier` (a coarse bucket, not the raw score, to keep the token small and avoid leaking a precise score value to client-side inspection), `device_id`, and standard `iat`/`exp` claims. It never carries phone number, email, or other PII — the token is a capability credential, not a data payload.

## Required Headers

| Header | Required on | Purpose |
|---|---|---|
| `Authorization: Bearer <access_token>` | All authenticated endpoints | Primary auth proof |
| `X-Device-Id` | All authenticated endpoints | Device-binding check per [../12-auth/session-token-management.md](../12-auth/session-token-management.md); mismatch against the token's `device_id` claim triggers step-up verification |
| `X-App-Version` | All endpoints | Enables version-aware behavior and the deprecation signaling described in [api-versioning-strategy.md](api-versioning-strategy.md) |
| `Idempotency-Key` | State-changing POSTs with side effects | Safe client retries, per [api-design-guidelines.md](api-design-guidelines.md) |

## Refresh Flow

The access token's 15-minute lifetime is intentionally short; the **refresh token** (30-day sliding expiry, stored hashed server-side and bound to the issuing device) is exchanged for a new access token via `POST /v1/auth/refresh`, which itself requires the refresh token plus the matching `X-Device-Id`. A refresh-token exchange rotates the refresh token (issues a new one, invalidates the old), so a stolen refresh token that gets used once by an attacker and once by the legitimate device produces a detectable double-use signal — the second use of an already-rotated token immediately revokes the entire token family and forces re-authentication, per the forced-logout mechanics in [../12-auth/session-token-management.md](../12-auth/session-token-management.md). This flow is invisible to the end user under normal use; the Flutter client's HTTP layer handles refresh transparently on a 401 response before retrying the original request once.

## Unauthenticated Endpoints

A small explicit allowlist bypasses the `Authorization` header requirement: `POST /v1/auth/otp/request`, `POST /v1/auth/otp/verify`, `POST /v1/auth/refresh`, and read-only public discovery endpoints intentionally scoped to require no login (browsing a limited public activity feed, per product decisions in [../15-activities-communities/README.md](../15-activities-communities/README.md)). Every other endpoint defaults to authenticated — new endpoints must opt into the public allowlist explicitly in code review rather than defaulting open, so a forgotten auth decorator cannot silently expose a private endpoint.

## Rate Limiting Interaction

Authentication endpoints are the most abused surface on any consumer API and are rate-limited more aggressively than authenticated traffic — OTP request/verify endpoints are limited per phone number and per IP independently, backed by Redis counters, per [../08-backend-services/rate-limiting-standards.md](../08-backend-services/rate-limiting-standards.md). This is a direct fraud-prevention control: unrestricted OTP requests are a common vector for SMS-pumping fraud against the SMS provider and for account-enumeration attacks.

## Related Documents

- [api-design-guidelines.md](api-design-guidelines.md)
- [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md)
- [../12-auth/session-token-management.md](../12-auth/session-token-management.md)
- [../08-backend-services/rate-limiting-standards.md](../08-backend-services/rate-limiting-standards.md)
