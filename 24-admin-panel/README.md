# 24-admin-panel/

**Phase:** Phase 5 — Data, AI & Admin  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** BE / Internal Tools  
**Depends on:** auth, trust-safety-fraud, moderation-content-ops  
**Document count:** 5

## Purpose

Internal tools for support, moderation, and platform operations. Corresponds to PRD-014.

## Why this folder exists

An admin panel is normally deprioritized at MVP, but here trust and safety operations cannot function without a moderation console from day one — it is promoted to P0 for that reason alone.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [admin-panel-requirements.md](admin-panel-requirements.md) | Functional scope | User lookup, moderation console, and activity management. | 5 | moderation-content-ops | none | P0 | BE, CM |
| [rbac-admin-roles.md](rbac-admin-roles.md) | Internal roles | Internal role permissions, kept separate from the user-facing RBAC model in auth. | 3 | admin-panel-requirements | auth | P0 | BE, SEC |
| [moderation-console-spec.md](moderation-console-spec.md) | Moderation console UI | The UI spec for the tool moderation-content-ops's human reviewers use daily. | 6 | automated-moderation-tooling-spec | moderation-content-ops | P0 | BE, UI |
| [user-management-tools-spec.md](user-management-tools-spec.md) | User management tools | Account lookup, manual verification override, and ban/suspend tooling. | 4 | admin-panel-requirements | auth | P0 | BE |
| [audit-log-spec.md](audit-log-spec.md) | Audit logging | Every admin action logged immutably — required for both security accountability and dispute resolution. | 3 | rbac-admin-roles | security | P0 | SEC |
