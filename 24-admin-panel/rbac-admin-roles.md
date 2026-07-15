---
title: "Rbac Admin Roles"
folder: 24-admin-panel
purpose: "Internal roles"
pages_estimate: 3
prerequisites: "admin-panel-requirements"
dependencies: "auth"
priority: P0
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# RBAC — Admin Roles

## Overview

This document defines the internal role and permission model for the ActivityMate admin panel described in [admin-panel-requirements.md](admin-panel-requirements.md). It is deliberately a separate model from the user-facing RBAC model in [../12-auth/authorization-rbac-model.md](../12-auth/authorization-rbac-model.md) — end-user roles (member, activity host, community organizer) answer "what can a customer do," while this model answers "what can an ActivityMate employee do inside internal tooling." Conflating the two would create both a security risk (accidentally granting a support employee's account customer-facing privilege escalation) and a modeling mess (the two role sets don't map onto each other at all).

## Roles

| Role | Scope | Typical holder |
|---|---|---|
| **Moderator (L1)** | View and action reports in the moderation queue; issue warnings; apply temporary suspensions up to 7 days; cannot permanently ban or reverse another moderator's decision. | Community Ops reviewers, per [moderation-console-spec.md](moderation-console-spec.md) |
| **Moderator (L2) / Trust & Safety Lead** | Everything L1 can do, plus permanent bans, appeal decisions, and override of L1 decisions; access to Tier 3 sensitive data per [../22-analytics-experimentation/data-governance-access-policy.md](../22-analytics-experimentation/data-governance-access-policy.md). | Senior Trust & Safety staff |
| **Support Agent** | User lookup (read-only for most fields), manual verification override with justification, account recovery assistance; no ban/suspend authority. | Customer support |
| **Engineering (Read)** | Read access to user, activity, and system data for debugging; no write actions. | Backend engineers on-call |
| **Engineering (Admin)** | Full read/write including schema-level and config-level admin tools (e.g., feature flag toggles, force-cancel activities in an incident). | BE Lead, DevOps on-call, restricted set |
| **Founder / PM (Read)** | Dashboard and reporting views across all functional areas; no action authority. | Founders, PM |
| **Super Admin** | Full access including RBAC role assignment itself. Reserved for CTO and Head of Trust & Safety only; every super-admin action is additionally flagged for review in the audit log. | 2-3 named individuals maximum at launch |

## Permission Model

Permissions are additive and role-based (not attribute-based) at launch, for simplicity — a role grants a fixed permission bundle rather than computed per-resource rules. This is intentionally simpler than the user-facing model because the internal user base (employees) is small and role assignment is manually reviewed, unlike the consumer RBAC model which must scale to hundreds of thousands of self-service accounts.

Key permission boundaries:
- **No role can grant itself higher privilege.** Role assignment requires Super Admin action, logged per [audit-log-spec.md](audit-log-spec.md).
- **Ban/suspend authority is tiered**, not binary — this reflects the real operational need for fast L1 response to obvious cases (e.g., an explicit harassment report) while reserving permanent, harder-to-reverse actions for more experienced reviewers, reducing the blast radius of a single reviewer's error or compromised account.
- **Sensitive data access (Tier 3/4 per governance policy) requires named role, not blanket engineering access** — an Engineering (Read) role does not automatically see government ID images or report contents; that requires explicit Trust & Safety role assignment.

## Authentication for Admin Accounts

Admin panel accounts require multi-factor authentication (mandatory, not optional, unlike the consumer app's MFA posture) and use separate credentials from any consumer-facing account an employee might personally hold — an employee is never able to use their personal ActivityMate login to access the admin panel. Session timeout is shorter than the consumer app (30 minutes idle) given the sensitivity of the data accessible. Full technical implementation follows [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md) patterns, applied to a separate credential namespace.

## Role Review Cadence

Role assignments reviewed monthly by Super Admins during the first six months post-launch (when the team is smallest and highest-trust), moving to quarterly thereafter. Any employee departure triggers immediate role revocation as part of offboarding, coordinated with [../36-hiring-people-culture/README.md](../36-hiring-people-culture/README.md) offboarding process.

## Related Documents

- [admin-panel-requirements.md](admin-panel-requirements.md)
- [audit-log-spec.md](audit-log-spec.md) — logs every role assignment and permission use
- [moderation-console-spec.md](moderation-console-spec.md) — primary consumer of the Moderator roles
- [../12-auth/authorization-rbac-model.md](../12-auth/authorization-rbac-model.md) — the distinct user-facing model
- [../30-security/security-architecture-overview.md](../30-security/security-architecture-overview.md) — security review this model falls under
