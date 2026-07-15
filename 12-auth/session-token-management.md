---
title: "Session Token Management"
folder: 12-auth
purpose: "Session lifecycle"
pages_estimate: 4
prerequisites: "authentication-architecture"
dependencies: "security"
priority: P0
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Session Token Management

## Overview

This document specifies the JWT/refresh-token lifecycle, device-binding mechanics, and forced-logout behavior that keep an ActivityMate session both convenient for a returning mobile user and hard to hijack. It is the session-lifetime counterpart to [authentication-architecture.md](authentication-architecture.md), which decides how identity is first proven, and to [../11-api/api-authentication-standards.md](../11-api/api-authentication-standards.md), which specifies how the resulting tokens travel on the wire.

## Context / Problem

Mobile users expect to stay logged in indefinitely — re-entering an OTP on every app open would be an immediate retention killer. But a long-lived credential is also a long-lived liability if stolen. The session model has to resolve this tension without relying on the user to behave securely (using a screen lock, noticing a phishing attempt), since that assumption fails often enough in practice that the system must degrade gracefully around it.

## Token Structure

Two token types, as introduced in [../11-api/api-authentication-standards.md](../11-api/api-authentication-standards.md):

- **Access token:** JWT, RS256-signed, 15-minute expiry, stateless (verifiable by any backend module without a database round trip). Carries `sub`, `role`, `trust_tier`, `device_id`.
- **Refresh token:** opaque random token (not a JWT — no need for the client to introspect it), 30-day sliding expiry, stored server-side in Postgres/Redis hashed (never plaintext), bound to a specific `device_id` and issued one per device, not one per account. A user logged in on two devices holds two independent refresh-token records, each independently revocable.

## Device Binding

At login, the client generates and persists a stable, app-install-scoped `device_id` (not tied to hardware identifiers that raise their own privacy concerns, but a random UUID generated on first app launch and persisted in secure device storage). This `device_id` is embedded in both the access token claim and the refresh-token record. Every authenticated request's `X-Device-Id` header (per [../11-api/api-authentication-standards.md](../11-api/api-authentication-standards.md)) is checked against the access token's `device_id` claim; a mismatch is treated as a signal of possible token theft (a stolen access token replayed from a different device) and the request is rejected, forcing re-authentication rather than silently accepting a device-inconsistent token.

## Refresh Flow and Rotation

Refresh-token exchange (`POST /v1/auth/refresh`) rotates the refresh token on every use — the old one is invalidated the moment a new one is issued for that device. This makes refresh-token theft self-limiting: if an attacker steals a refresh token and uses it, the legitimate device's next refresh attempt will fail (its token was already rotated by the attacker's use), which is treated as a compromise signal. Detecting this reuse-after-rotation pattern immediately revokes the entire token family for that device and requires full OTP re-authentication — the user is not silently logged out with no explanation; the Flutter client surfaces a "we logged you out for your security" message with a path straight back into the OTP flow.

## Suspicious-Device Detection and Forced Logout

Beyond device-ID mismatch, the following signals trigger forced logout of the affected session(s), enforced by invalidating the relevant refresh-token record(s) server-side (access tokens simply expire naturally within 15 minutes, so forced logout is effectively immediate at the refresh layer and complete within one access-token lifetime):

- Refresh-token reuse-after-rotation (described above).
- A new device login for an account combined with a fraud-signal spike from [../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md) (e.g. impossible-travel geolocation between two near-simultaneous sessions).
- Account-status transition to `suspended` or `banned` — all active sessions for that account are revoked immediately as part of the same transaction that changes account status, so a moderation action takes effect instantly rather than waiting for natural token expiry.
- User-initiated "log out of all devices," surfaced in account settings, which revokes every refresh token on the account at once.

## Storage and Revocation Infrastructure

Active refresh-token records and a short-lived denylist of recently revoked access-token JTIs (JWT IDs) live in Redis for fast lookup, backed by a durable Postgres table as the source of truth for audit purposes — Redis is a performance cache over this data, not its sole store, consistent with Redis's role as defined in [../10-database/database-selection-rationale.md](../10-database/database-selection-rationale.md). The access-token denylist only needs to cover the 15-minute access-token window, keeping it small and cheap to check on every request. Key management for the RS256 signing keypair follows [../30-security/secrets-key-management.md](../30-security/secrets-key-management.md), with scheduled key rotation and the old public key retained temporarily to validate tokens issued just before rotation.

## Related Documents

- [authentication-architecture.md](authentication-architecture.md)
- [account-recovery-security.md](account-recovery-security.md)
- [../11-api/api-authentication-standards.md](../11-api/api-authentication-standards.md)
- [../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md)
- [../30-security/secrets-key-management.md](../30-security/secrets-key-management.md)
- [../10-database/database-selection-rationale.md](../10-database/database-selection-rationale.md)
