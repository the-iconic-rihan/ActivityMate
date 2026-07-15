---
title: "Oauth Social Login Integration"
folder: 12-auth
purpose: "Social login spec"
pages_estimate: 3
prerequisites: "authentication-architecture"
dependencies: "none"
priority: P1
audience: "BE, FE"
status: draft-v1
generated: 2026-07-15
---

# OAuth Social Login Integration

## Overview

This document specifies Google and Apple sign-in integration for ActivityMate, explicitly scoped — per the decision in [authentication-architecture.md](authentication-architecture.md) — as a **secondary convenience layered on top of a mandatory phone-verified account**, never as an independent path to creating or accessing an account.

## Context

Social login is a well-understood UX convenience: fewer taps, no OTP wait, and it can pre-fill profile fields (name, profile photo suggestion) from an identity the user already manages elsewhere. The risk it must not reintroduce is the one [authentication-architecture.md](authentication-architecture.md) already rejected it for as a primary method — social accounts are cheap and fast to create in bulk, so allowing "sign in with Google" to *create* an ActivityMate account on its own would hand fake-account operators a frictionless bypass of the phone-verification cost barrier the whole trust model depends on.

## Integration Scope

**Google Sign-In** (via Google Identity Services / Sign in with Google for Android and cross-platform Flutter support) and **Sign in with Apple** (mandatory on iOS per Apple App Store guidelines whenever any third-party social login is offered — Apple requires Sign in with Apple as an equivalent option if Google or Facebook login is present) are the two providers integrated. Facebook login is deliberately not included: Meta's platform-level fake-account tolerance and the product's explicit non-goal of resembling a dating app (PRD-001 §4) both weigh against a Facebook-sourced identity signal being useful here.

## Flow

1. **New user, no existing ActivityMate account:** Social sign-in button is available on the signup screen but functions only as a *pre-fill assist*, not an account-creation trigger. Tapping "Continue with Google" retrieves the user's Google-verified name and profile photo (with consent) to pre-populate the signup form, then routes directly into the mandatory phone-OTP flow from [authentication-architecture.md](authentication-architecture.md) — phone verification is never skipped, regardless of social-login status. The account is created only once phone-OTP succeeds; the social identity is then linked to the newly created account as metadata, not used as the account's root credential.
2. **Returning user, existing account, social identity already linked:** "Continue with Google/Apple" becomes a genuine quick-login path — the linked social credential is checked against the account's stored `oauth_links` mapping, and if the device also satisfies the device-binding check from [session-token-management.md](session-token-management.md), a session is issued without requiring a fresh OTP. If the device is new/unrecognized, the system still falls back to requiring phone-OTP or the recovery flow in [account-recovery-security.md](account-recovery-security.md), since a social-login token alone does not carry the device-trust signal a bound session does.
3. **Linking/unlinking:** a user may link or unlink a Google/Apple identity to their existing phone-verified account at any time from account settings; unlinking never deletes the underlying ActivityMate account, since the account's root identity is always the verified phone number, not the social link.

## Data Handling

Only the minimum OAuth-provided claims needed for pre-fill and linking are stored: provider (`google`/`apple`), provider user ID (for the link, not the user's raw email unless separately consented), and — for pre-fill only, never re-fetched afterward — display name and profile photo URL at time of linking. No social-graph data (contacts, friends) is requested or stored; ActivityMate's OAuth scope requests are limited to basic profile and verified email, consistent with the data-minimization posture in [../31-privacy-compliance-legal/privacy-policy-framework.md](../31-privacy-compliance-legal/privacy-policy-framework.md).

## Security Considerations

OAuth tokens received from Google/Apple are verified server-side against the provider's public keys before being trusted (never trusting a client-asserted identity without server-side token verification), and the resulting ActivityMate session tokens follow the exact same issuance, device-binding, and revocation mechanics as OTP-originated sessions in [session-token-management.md](session-token-management.md) — social login is a different *front door* into session issuance, not a different *kind* of session.

## Related Documents

- [authentication-architecture.md](authentication-architecture.md)
- [session-token-management.md](session-token-management.md)
- [account-recovery-security.md](account-recovery-security.md)
- [../31-privacy-compliance-legal/privacy-policy-framework.md](../31-privacy-compliance-legal/privacy-policy-framework.md)
- [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md)
