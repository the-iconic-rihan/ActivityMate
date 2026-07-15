---
title: "Authorization Rbac Model"
folder: 12-auth
purpose: "Permission model"
pages_estimate: 5
prerequisites: "authentication-architecture"
dependencies: "trust-safety-fraud"
priority: P0
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Authorization RBAC Model

## Overview

This document defines ActivityMate's role-based access control model: the roles that exist, what each can do, and — the distinguishing design choice — how the two most consequential roles (activity host, community admin) are *earned* through trust-score thresholds rather than self-assigned or granted by simple account age. Authentication (proven by [authentication-architecture.md](authentication-architecture.md)) answers "who is this," authorization answers "what are they allowed to do," and for a trust-first product those two questions cannot be fully separated from the trust engine.

## Context / Problem

A conventional RBAC model treats roles as static grants: an admin assigns a role, or a user self-selects one ("I want to be a host") at signup. That model is a poor fit for ActivityMate because the platform's central risk is exactly the failure mode a self-assigned host role would enable — an unverified, low-trust, or malicious account declaring itself a host and inviting strangers to a real-world meetup. Roles that carry real-world safety exposure must be gated by demonstrated trustworthiness, not by a checkbox at signup.

## Roles

| Role | Grant mechanism | Core capabilities |
|---|---|---|
| `user` | Default on account creation (post phone-verification) | Browse activities, request to join, message within joined activity conversations, rate/report other users |
| `activity_host` | Automatically unlocked once `trust_scores.score` crosses the hosting threshold (see below) — not a separate signup step | Everything `user` can do, plus: create activities, accept/decline join requests, set `min_trust_score_required` on their own activities, remove a participant pre-activity |
| `community_admin` | Granted by a community's creator/owner, but only to members who themselves meet the hosting trust threshold — a low-trust member cannot be promoted even by the community owner | Manage community membership, moderate community-scoped content, feature/pin community activities |
| `moderator` | Platform-granted, internal staff/trusted volunteer role, not user-earned | Review reports, apply account actions (warn, suspend), access moderation console per [../24-admin-panel/moderation-console-spec.md](../24-admin-panel/moderation-console-spec.md) |
| `platform_admin` | Platform-granted, internal staff only, smallest population | Full administrative access, including role overrides and account-status changes; every action audit-logged per [../24-admin-panel/audit-log-spec.md](../24-admin-panel/audit-log-spec.md) |

## Trust-Score-Gated Role Elevation

The `activity_host` capability is the model's centerpiece: it is not a role a user requests or self-assigns. It activates automatically once a user's `trust_scores.score` (see [../10-database/schema-design-erd.md](../10-database/schema-design-erd.md)) crosses a minimum threshold, calibrated initially at a level reachable through phone + selfie-liveness verification plus a clean account-age floor (no reports, no cancellation history yet, since a brand-new account has none) — meaning a fully verified, brand-new account *can* host, but an account carrying negative trust signals (reports, no-shows) cannot, regardless of verification tier. The exact numeric threshold is owned and tunable by the trust engine, not hardcoded in the authorization layer — this document specifies the *mechanism* (score-gated capability unlock), while [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md) owns the *threshold value* and its recalibration process. This separation matters because trust thresholds will need tuning based on real fraud/safety data post-launch, and that tuning should not require an authorization-layer code change.

`community_admin` elevation follows the same principle at one remove: a community owner can promote a member to admin, but the authorization layer rejects the promotion if the target member's trust score is below the hosting threshold — preventing a compromised or malicious community owner from handing moderation-adjacent power to a low-trust account.

## Enforcement Architecture

Role and trust-tier are both present in the JWT access token issued at login (per [../11-api/api-authentication-standards.md](../11-api/api-authentication-standards.md)), but the token's `trust_tier` claim is a coarse, cached bucket refreshed at token-issue time — it is **not** re-checked live on every request. Capability checks that must reflect the *current* score in real time (e.g. did this user's score just drop below the hosting floor due to a report filed five minutes ago) query the authoritative `trust_scores` table directly rather than trusting a potentially stale token claim, specifically for the host-creation and participant-accept code paths where staleness has real safety consequences. Lower-stakes read paths tolerate the token's cached tier.

Authorization checks are implemented as FastAPI dependency injections applied per-route, never as ad hoc `if` checks scattered through handler bodies — this keeps the permission model auditable in one place per route definition and reviewable independently of business logic, consistent with the coding conventions in [../08-backend-services/backend-coding-guidelines.md](../08-backend-services/backend-coding-guidelines.md).

## Role Revocation

A role can be lost as automatically as it was gained: if a hosting-tier user's trust score drops below the threshold (a report is upheld against them, they accumulate no-shows), `activity_host` capability is revoked immediately on next score recalculation — existing activities they are currently hosting are flagged for moderator review rather than auto-cancelled, since abrupt cancellation itself has a safety cost for already-committed participants. `moderator` and `platform_admin` revocation is manual, staff-only, and logged.

## Related Documents

- [authentication-architecture.md](authentication-architecture.md)
- [../10-database/schema-design-erd.md](../10-database/schema-design-erd.md)
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)
- [../24-admin-panel/rbac-admin-roles.md](../24-admin-panel/rbac-admin-roles.md)
- [../24-admin-panel/audit-log-spec.md](../24-admin-panel/audit-log-spec.md)
