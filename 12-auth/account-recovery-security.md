---
title: "Account Recovery Security"
folder: 12-auth
purpose: "Recovery flow"
pages_estimate: 3
prerequisites: "authentication-architecture"
dependencies: "none"
priority: P1
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Account Recovery Security

## Overview

This document specifies how a user regains access to their ActivityMate account when they lose access to their verified phone number — lost device, changed SIM, or a genuinely new number — while being explicitly designed to resist SIM-swap-based account takeover, the specific fraud vector that a naive recovery flow would reopen right after phone-OTP authentication closes it.

## Context / Problem

Phone-OTP was adopted in [authentication-architecture.md](authentication-architecture.md) precisely because it is expensive and traceable to fake at scale. But that strength creates a specific weakness at the recovery boundary: SIM-swap fraud, where an attacker social-engineers or bribes a telecom retailer into porting a victim's phone number onto an attacker-controlled SIM, is a well-documented account-takeover technique against exactly this kind of phone-anchored identity system across the Indian fintech and consumer-app landscape. A recovery flow that simply says "prove you control this phone number" is trivially satisfiable by a successful SIM-swap attacker — it does not actually re-verify the *person*, only the *number*, which is exactly the asset the attacker just stole.

## Design Principle

Recovery must never be *weaker* than the original authentication bar. Since phone-OTP alone is the primary authentication method, recovery cannot rely on phone-OTP alone as its *sole* factor when the recovery scenario itself might involve a compromised phone number — recovery needs an additional signal that a SIM-swap attacker would not also possess.

## Recovery Flow

**Scenario 1 — user has lost their device but retains the same phone number.** Standard phone-OTP re-authentication is sufficient; this is not a recovery scenario in the security-risk sense, since the number itself was never compromised. New device triggers the device-binding and suspicious-device checks in [session-token-management.md](session-token-management.md) as normal.

**Scenario 2 — user's phone number itself changed (lost SIM, number reassigned, genuine new number) or a SIM-swap is suspected.** This is the higher-risk path and requires a layered check before the account's phone number of record is changed:

1. OTP verification of the *new* number (proves control of the new number, standard first step).
2. A secondary identity signal independent of any phone number — at minimum, matching against the user's existing selfie-liveness verification data from [identity-verification-kyc.md](identity-verification-kyc.md): the user performs a fresh liveness capture, matched against their originally verified profile face. This is the control that specifically defeats a SIM-swap attacker, who possesses the victim's phone number but not their face.
3. A mandatory cool-down / hold period (24–72 hours, tunable) between the recovery request being approved and the account's phone number of record actually changing, during which the *original* number receives an SMS notification of the pending change with a one-tap "this wasn't me" cancellation link — giving the legitimate owner a window to detect and stop an attacker's recovery attempt even if the attacker cleared steps 1 and 2 through some other means.
4. During the hold period, the account is placed in a restricted state: existing sessions on the original device remain valid (so a legitimate user mid-transition is not locked out), but new high-risk actions (creating activities, changing payment details once payments exist) are blocked until the hold clears.

**Scenario 3 — user cannot pass the selfie-liveness match** (e.g. never completed selfie verification, or a genuine appearance change makes automated matching fail). Falls through to manual review by the trust and safety team, following the escalation path in [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md), which may request alternate evidence (e.g. a matching government-ID upload for Verified+ users) rather than approving recovery on phone-OTP alone.

## Rate Limiting and Abuse Prevention

Recovery-flow initiation is rate-limited per account and per device fingerprint, following the same Redis-backed pattern as OTP requests in [../08-backend-services/rate-limiting-standards.md](../08-backend-services/rate-limiting-standards.md), since the recovery flow is itself an attractive target for automated attack attempts. Every recovery attempt — successful or not — is logged as a trust-relevant event, feeding [../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md), since a pattern of recovery attempts across many accounts is a strong fraud-ring signal.

## Related Documents

- [authentication-architecture.md](authentication-architecture.md)
- [session-token-management.md](session-token-management.md)
- [identity-verification-kyc.md](identity-verification-kyc.md)
- [../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md)
- [../13-trust-safety-fraud/incident-response-playbook-trust.md](../13-trust-safety-fraud/incident-response-playbook-trust.md)
