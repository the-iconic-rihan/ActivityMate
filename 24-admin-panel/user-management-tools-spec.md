---
title: "User Management Tools Spec"
folder: 24-admin-panel
purpose: "User management tools"
pages_estimate: 4
prerequisites: "admin-panel-requirements"
dependencies: "auth"
priority: P0
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# User Management Tools Spec

## Overview

This spec covers the account lookup, manual verification override, and ban/suspend tooling inside the admin panel described in [admin-panel-requirements.md](admin-panel-requirements.md). It is the tool support agents and Trust & Safety staff use to resolve individual user issues — distinct from the moderation console ([moderation-console-spec.md](moderation-console-spec.md)), which is case/report-driven, whereas this tool is user-driven (start from a person, not a case).

## Account Lookup

- **Search:** single field accepting phone number, email, user ID, or display name; fuzzy name matching via Meilisearch (the platform's existing search engine, per PRD-001 §12). Exact match on phone/email is prioritized in result ranking since support tickets typically arrive with one of those identifiers.
- **Result card:** shows account status (active, suspended, banned, pending verification), trust score, verification level (phone/email/selfie/government ID/LinkedIn, per PRD-001 §9), and account age at a glance before a reviewer opens the full profile.
- **Full profile view:** verification history and current level per method, trust score breakdown by component (completed activities, ratings, reports, cancellation rate, response rate, account age), activity history (hosted and joined, with completion/no-show/cancellation outcomes), report history (both reports filed against the user and reports the user has filed against others — both directions matter for calibrating credibility), and community memberships.

## Manual Verification Override

Used when an automated verification step (selfie liveness, government ID OCR) fails for a legitimate reason — poor lighting, an ID format the automated pipeline doesn't recognize well, etc. — and a human reviewer confirms the document/selfie is valid.

- **Requires:** reviewer views the actual submitted artifact (selfie image, ID image) inline; this is Tier 3/4 sensitive data per [../22-analytics-experimentation/data-governance-access-policy.md](../22-analytics-experimentation/data-governance-access-policy.md) and access is scoped accordingly.
- **Action:** override sets verification status for that specific method to "manually verified," tagged distinctly from "auto-verified" in the trust score breakdown so the trust engine and any later audit can distinguish the two paths.
- **Mandatory justification field**, same pattern as the moderation console, logged immutably per [audit-log-spec.md](audit-log-spec.md).
- **Cannot be self-applied:** a reviewer cannot override their own account's verification, enforced at the permission level, not just policy.
- **Government ID override requires L2/Trust & Safety role** (per [rbac-admin-roles.md](rbac-admin-roles.md)); phone/email overrides can be handled by Support Agent role since the risk profile is lower.

## Ban / Suspend Tooling

- **Suspend (temporary):** reviewer selects duration (24h, 72h, 7 days, or custom), reason category (matches the report taxonomy: harassment, no-show pattern, fake profile, unsafe conduct, other), and justification text. Suspended users cannot create or join activities but retain read access to past activity history and can still file an appeal.
- **Ban (permanent):** requires L2/Trust & Safety Lead role. Same reason-category and justification requirements, plus a mandatory secondary confirmation step given the irreversibility (technically reversible by Super Admin only, but operationally treated as permanent).
- **Device/fingerprint-level ban:** for confirmed fraud or repeat-offender patterns where a simple account ban would be trivially circumvented by resignup — bans the underlying device fingerprint or contact method (phone/email) in addition to the account, coordinated with fraud risk scoring in [../23-ai-ml-features/ml-infra-mlops.md](../23-ai-ml-features/ml-infra-mlops.md).
- **Unsuspend/unban:** requires the same or higher role than the original action; always logged with a reason (e.g., "appeal upheld," "false positive confirmed").

## Account Recovery Assistance

Support Agents can initiate account recovery flows (password/OTP reset assistance, email/phone change verification) for users locked out of their accounts, following the security patterns in [../12-auth/account-recovery-security.md](../12-auth/account-recovery-security.md). This tool never allows a support agent to directly view or set a user's password/credential — recovery always routes through the same OTP/verification channel a user would use themselves, to avoid creating an account-takeover vector via a compromised support credential.

## Appeals Integration

Ban/suspend actions taken here surface directly in the user-facing appeals flow described in [../21-moderation-content-ops/appeals-process.md](../21-moderation-content-ops/appeals-process.md) — an appealed decision opens a linked case in the moderation console for L2 review, rather than requiring a reviewer to manually cross-reference two separate systems.

## Related Documents

- [admin-panel-requirements.md](admin-panel-requirements.md)
- [moderation-console-spec.md](moderation-console-spec.md) — case-driven counterpart to this user-driven tool
- [rbac-admin-roles.md](rbac-admin-roles.md) — permission gating for override and ban actions
- [audit-log-spec.md](audit-log-spec.md)
- [../12-auth/account-recovery-security.md](../12-auth/account-recovery-security.md)
- [../21-moderation-content-ops/appeals-process.md](../21-moderation-content-ops/appeals-process.md)
