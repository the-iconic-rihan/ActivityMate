---
title: "Authentication Architecture"
folder: 12-auth
purpose: "Login method"
pages_estimate: 6
prerequisites: "api-authentication-standards"
dependencies: "none"
priority: P0
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Authentication Architecture

## Overview

This document decides ActivityMate's primary identity mechanism and explains why phone-number OTP is the mandatory entry point for every account, with social login permitted only as a secondary convenience layered on top — never a substitute. Authentication is the entry point to the trust graph described throughout [../10-database/schema-design-erd.md](../10-database/schema-design-erd.md); every downstream trust and safety claim the product makes depends on this layer being hard to fake.

## Context / Problem

ActivityMate exists to connect strangers for real-world, sometimes-nighttime activities in Mumbai. The single biggest platform risk named in PRD-001 is fake profiles, and the single biggest lever against fake profiles is how expensive it is to create an account in the first place. An authentication method that is free and instant to repeat (disposable email, freely creatable social accounts) makes every other trust and safety investment — verification tiers, trust scores, moderation — fight an uphill battle against an unlimited supply of fresh fake identities. The identity layer has to make account creation cost *something* real before any other trust signal is layered on top.

## Options Considered

### Option A — Phone number + OTP (adopted as mandatory primary)

**Pros:** Phone-OTP is the default expectation for Indian consumer apps — near-universal comprehension, no password to forget, no email-verification friction. A phone number tied to a SIM card carries real-world cost and identity linkage: acquiring a new number for fraud requires either a new SIM (subject to India's mandatory SIM-registration KYC via telecom operators) or a virtual-number service, both of which are more expensive and traceable than creating a free email address. SMS delivery through an India-focused OTP/SMS provider also gives natural fraud signals for free — carrier lookups can flag freshly ported numbers, VoIP/virtual numbers, and numbers with unusually high OTP-request velocity, all of which correlate with fraud rings. Phone number additionally doubles as the primary channel for safety-relevant real-time notifications (a "your host is 5 minutes late" SMS fallback when push fails).

**Cons:** SMS delivery reliability varies by carrier and can be delayed during network congestion; OTP-request abuse (SMS pumping fraud, where an attacker triggers OTP sends to premium-rate or attacker-controlled numbers to generate telecom revenue at the platform's cost) must be actively rate-limited, per [../11-api/api-authentication-standards.md](../11-api/api-authentication-standards.md) and [../08-backend-services/rate-limiting-standards.md](../08-backend-services/rate-limiting-standards.md). SIM-swap fraud remains a real account-takeover vector, addressed separately in [account-recovery-security.md](account-recovery-security.md).

### Option B — Email + password

**Pros:** Familiar, no telecom dependency, no per-message delivery cost, works identically for any country if the product ever expands beyond India-specific SMS infrastructure.

**Cons:** Email addresses are free and instantly creatable in unlimited quantity — Gmail alone permits effectively unlimited alias creation — making this the weakest possible barrier against exactly the fake-profile risk the product is most worried about. Password-based auth also imports a whole class of security burden (breach-credential stuffing, weak-password risk, password-reset flow complexity) that phone-OTP sidesteps entirely by having no password to steal or reset.

### Option C — Social login only (Google/Apple as primary)

**Pros:** Fastest possible onboarding — one tap, no OTP wait, leverages an identity the user already trusts and has verified elsewhere.

**Cons:** Imports whatever fake-account tolerance the underlying platform (Google, Apple) has, which is not tuned to ActivityMate's safety bar — a throwaway Gmail-linked Google account can be created in seconds and used to sign into ActivityMate with zero additional friction, defeating the cost-to-create barrier Option A provides. Social login also gives the platform no independent, telecom-anchored signal for its own fraud detection — it fully outsources identity assurance to a third party the platform does not control and cannot tune. Rejected as a primary mechanism for this reason; retained as a secondary convenience, detailed in [oauth-social-login-integration.md](oauth-social-login-integration.md).

## Recommendation & Rationale

Phone number plus OTP is the **mandatory primary identity** for every ActivityMate account — no account exists without a verified phone number, full stop. Social login (Google, Apple) may be offered as a secondary, post-phone-verification convenience — e.g. to pre-fill profile fields or provide an alternate quick-login path for a device already carrying an active session — but it is never accepted as a substitute for phone verification and never allowed to create an account on its own. This ordering is deliberate: phone-OTP first establishes the real-world-cost identity anchor; social login afterward is pure UX convenience layered on an already-anchored account, not a second door into the platform.

### OTP Delivery Architecture

OTP codes are generated server-side (6-digit numeric, single-use, 5-minute expiry) and delivered via an India-focused SMS/OTP provider selected for DLT (Distributed Ledger Technology) template registration compliance — mandatory under TRAI regulations for any commercial SMS sent to Indian numbers — and for delivery-receipt visibility so the backend can detect and alert on delivery failures rather than silently stranding users. OTP codes are stored hashed in Redis with a TTL matching the expiry window, never in plaintext, and never logged. Verification attempts are rate-limited per phone number (max 5 attempts per code, code invalidated after) and per originating IP (to blunt distributed brute-force), per [../08-backend-services/rate-limiting-standards.md](../08-backend-services/rate-limiting-standards.md).

### Interaction with Trust and Fraud Systems

A freshly phone-verified number is not automatically trusted — it is the *floor*, not the ceiling, of the verification model. Carrier metadata available at OTP-send time (line type, port history where obtainable from the SMS provider) feeds into the fraud-detection signals described in [../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md), and phone verification is the first of the verification-tier ladder detailed in [identity-verification-kyc.md](identity-verification-kyc.md).

## Related Documents

- [../11-api/api-authentication-standards.md](../11-api/api-authentication-standards.md)
- [session-token-management.md](session-token-management.md)
- [identity-verification-kyc.md](identity-verification-kyc.md)
- [account-recovery-security.md](account-recovery-security.md)
- [oauth-social-login-integration.md](oauth-social-login-integration.md)
- [../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md)
- [../08-backend-services/rate-limiting-standards.md](../08-backend-services/rate-limiting-standards.md)
