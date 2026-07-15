# 12-auth/

**Phase:** Phase 3 — Architecture & Core Engineering  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** Security Lead / BE  
**Depends on:** database, api  
**Document count:** 6

## Purpose

Identity: how users prove who they are, stay signed in, and recover access.

## Why this folder exists

Authentication is the entry point to the trust graph. A weak identity layer undermines every downstream trust and safety claim, since verification badges mean nothing if accounts are trivially fakeable.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [authentication-architecture.md](authentication-architecture.md) | Login method | Compares phone plus OTP (India-standard, low friction, and a natural fraud signal via carrier/porting checks) against email plus password (higher friction, more fake-account-friendly) and social login (fastest, but imports platform-level fake accounts). Recommends phone OTP as the mandatory primary identity, with social login layered on only as a secondary convenience, never a substitute for phone verification. | 6 | api-authentication-standards | none | P0 | BE, SEC |
| [authorization-rbac-model.md](authorization-rbac-model.md) | Permission model | Roles — user, activity host, community admin, moderator, platform admin — with host and admin roles earned via trust-score thresholds, not self-assigned. | 5 | authentication-architecture | trust-safety-fraud | P0 | BE, SEC |
| [session-token-management.md](session-token-management.md) | Session lifecycle | JWT/refresh-token strategy, device binding, and forced logout on suspicious-device detection. | 4 | authentication-architecture | security | P0 | BE, SEC |
| [identity-verification-kyc.md](identity-verification-kyc.md) | Verification tiers | Compares Aadhaar-based verification (strongest signal, but real regulatory and consent complexity under the DPDP Act), selfie-liveness matching (moderate signal, low friction, no government-ID dependency), and uploaded government-ID review (strong signal, high manual-review cost). Recommends selfie-liveness as the default tier, government-ID review as an optional 'Verified+' tier, and Aadhaar deferred pending legal review. Directly implements PRD-001's verification-level list. | 8 | authorization-rbac-model | trust-safety-fraud, privacy-compliance-legal | P0 | BE, SEC, LG, PM |
| [account-recovery-security.md](account-recovery-security.md) | Recovery flow | Account recovery designed to resist SIM-swap-based takeover rather than reopen the fraud vector authentication closes. | 3 | authentication-architecture | none | P1 | BE, SEC |
| [oauth-social-login-integration.md](oauth-social-login-integration.md) | Social login spec | Google and Apple sign-in integration, explicitly scoped as secondary per authentication-architecture. | 3 | authentication-architecture | none | P1 | BE, FE |
