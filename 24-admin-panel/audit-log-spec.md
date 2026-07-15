---
title: "Audit Log Spec"
folder: 24-admin-panel
purpose: "Audit logging"
pages_estimate: 3
prerequisites: "rbac-admin-roles"
dependencies: "security"
priority: P0
audience: "SEC"
status: draft-v1
generated: 2026-07-15
---

# Audit Log Spec

## Overview

Every action taken inside the admin panel — by a moderator, support agent, engineer, or Super Admin — is written to an immutable audit log. This exists for two equally important reasons: security accountability (detecting misuse of internal access, including a compromised or malicious insider) and dispute resolution (when a banned or suspended user appeals, the audit log is the authoritative record of what happened, who decided it, and why).

## What Gets Logged

Every write action from every tool covered in this folder:

- **Moderation console** ([moderation-console-spec.md](moderation-console-spec.md)): warn, suspend, ban, dismiss, escalate — including the mandatory justification text.
- **User management tools** ([user-management-tools-spec.md](user-management-tools-spec.md)): verification overrides, ban/unban/suspend/unsuspend, account recovery actions initiated on a user's behalf.
- **RBAC changes** ([rbac-admin-roles.md](rbac-admin-roles.md)): role assignment, role revocation — logged even though the acting party is by definition a Super Admin, since this is the highest-sensitivity action category.
- **Activity management** ([admin-panel-requirements.md](admin-panel-requirements.md)): force-cancel, force-unpublish, roster edits.
- **Data access events** for Tier 3/4 data per [../22-analytics-experimentation/data-governance-access-policy.md](../22-analytics-experimentation/data-governance-access-policy.md) — viewing a government ID image or selfie is itself a logged event, not just the resulting override decision, since access alone (even without action) is sensitive.

Read-only actions outside the Tier 3/4 case above (e.g., a support agent viewing a general profile) are not individually logged at the same granularity — logging every read would create excessive noise without proportionate security value — but session-level access (who logged into the admin panel, when, from where) is logged for every session regardless.

## Log Entry Schema

| Field | Description |
|---|---|
| `log_id` | Unique, immutable identifier |
| `timestamp` | UTC, server-generated, never client-supplied |
| `actor_id` | Internal employee/admin account ID |
| `actor_role` | Role at time of action (per [rbac-admin-roles.md](rbac-admin-roles.md)) — captured at action time, not looked up later, so a subsequent role change doesn't rewrite history |
| `action_type` | e.g., `user_suspended`, `verification_overridden`, `role_assigned` |
| `target_type` / `target_id` | What the action was taken on (user, activity, role assignment) |
| `justification_text` | Required free-text field from the UI, verbatim |
| `previous_state` / `new_state` | Before/after snapshot of the relevant field(s), enabling reconstruction of exactly what changed |
| `session_id` / `ip_address` | For security forensics |

## Immutability & Storage

- **Append-only.** No admin panel role — including Super Admin — has UPDATE or DELETE access to audit log entries through any application-layer interface. Corrections are handled by appending a new "correction" entry referencing the original, never by editing history.
- **Storage:** kept in a separate datastore/table from operational admin-panel data with stricter write permissions, reviewed under [../30-security/secrets-key-management.md](../30-security/secrets-key-management.md) and [../30-security/security-architecture-overview.md](../30-security/security-architecture-overview.md) principles.
- **Retention:** minimum 3 years, aligned with the longer end of dispute-resolution and regulatory retention windows reviewed in [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md).
- **Tamper-evidence:** entries are hash-chained (each entry's hash includes the prior entry's hash) so any attempt to alter or delete a historical entry at the storage layer is detectable, not just prevented by application logic.

## Access to the Audit Log

Audit log read access is itself tightly scoped: Security Lead, CTO, and Head of Trust & Safety by default; any broader access (e.g., an engineer investigating a specific incident) is temporary and itself logged. This mirrors the Tier 3/4 access discipline in [../22-analytics-experimentation/data-governance-access-policy.md](../22-analytics-experimentation/data-governance-access-policy.md) — the audit log is itself sensitive data, since it can reveal patterns about specific users and specific employees' work.

## Review Cadence

Security Lead performs a weekly automated anomaly scan (e.g., a single admin account issuing an unusually high volume of bans, or accessing Tier 4 data outside their normal role pattern) and a monthly manual sample review. Any anomaly is escalated per [../40-incident-disaster-recovery/README.md](../40-incident-disaster-recovery/README.md).

## Related Documents

- [rbac-admin-roles.md](rbac-admin-roles.md) — roles whose actions are logged here
- [moderation-console-spec.md](moderation-console-spec.md), [user-management-tools-spec.md](user-management-tools-spec.md) — primary sources of logged actions
- [../22-analytics-experimentation/data-governance-access-policy.md](../22-analytics-experimentation/data-governance-access-policy.md) — data sensitivity tiering this log respects
- [../30-security/security-architecture-overview.md](../30-security/security-architecture-overview.md)
- [../21-moderation-content-ops/appeals-process.md](../21-moderation-content-ops/appeals-process.md) — consumer of this log during disputes
