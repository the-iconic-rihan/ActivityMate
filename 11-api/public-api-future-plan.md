---
title: "Public Api Future Plan"
folder: 11-api
purpose: "Partner API roadmap"
pages_estimate: 3
prerequisites: "api-versioning-strategy"
dependencies: "none"
priority: P3
audience: "BE, F"
status: draft-v1
generated: 2026-07-15
---

# Public API Future Plan

## Overview

This document scopes a future public-facing API that would let external venue partners (cafes, trekking operators, badminton courts, coworking spaces) list bookable slots or promote hosted activities on ActivityMate. It is explicitly deferred past MVP — priority P3, future exploration — and exists now only to record the shape of the eventual plan so present-day API design in [api-design-guidelines.md](api-design-guidelines.md) does not accidentally foreclose it.

## Context / Problem

ActivityMate's MVP is entirely peer-to-peer: individual users host and join activities with each other. There is no venue-partner product surface at launch. However, the founders' roadmap (PRD-001 §13, PRD roadmap items) anticipates venue partnerships as a plausible post-PMF growth and monetization lever — a café could list "Sunday morning coffee meetups welcome here," or a trekking operator could offer verified group slots directly through the platform. Building this prematurely would divert engineering effort the MVP needs and would require a partner-facing authentication and rate-limiting story the team has no evidence it needs yet. Building it with zero forethought later risks an incompatible retrofit onto an API that was never designed to be called by anyone other than the first-party Flutter app.

## Why This Is Deliberately Deferred

Three reasons this stays out of MVP scope: first, PRD-001 explicitly excludes "large event ticketing" as a non-goal, and a venue-partner API adjacent to it risks scope creep into that territory if not carefully bounded. Second, a public API changes the security posture materially — partner API keys, partner-scoped rate limits, and a partner onboarding/support process are all new surface area with no current owner. Third, and most importantly, the product has not yet validated that venue partnerships are a real growth lever in the Mumbai market; building API infrastructure for an unvalidated hypothesis is the kind of premature investment the modular-monolith-first architecture strategy (see [../07-architecture/system-architecture-overview.md](../07-architecture/system-architecture-overview.md)) is explicitly designed to avoid.

## Scoping Principles for When This Is Revisited

When the business case is validated (tracked in [../01-business-strategy/README.md](../01-business-strategy/README.md) and [../32-growth-marketing/README.md](../32-growth-marketing/README.md)), the public API should follow these constraints, decided now so later work has guardrails:

- **Additive on top of the existing REST contract, not a parallel API.** Partner-facing endpoints get their own OpenAPI tag and a distinct auth scope (partner API keys, not user JWTs), but reuse the same versioning scheme from [api-versioning-strategy.md](api-versioning-strategy.md) and the same envelope/error conventions from [api-design-guidelines.md](api-design-guidelines.md), so there is one contract philosophy, not two.
- **Read-heavy, write-narrow.** Partners should be able to push slot availability and read aggregate, anonymized engagement stats about their listings. Partners should never get direct read access to user PII, trust scores, or any data outside their own listing's aggregate performance — the trust graph remains fully internal.
- **Outbound events use the webhook standard already defined** in [webhook-standards.md](webhook-standards.md) — a partner integration should not invent a new signing/retry scheme.
- **Rate limiting and quota per partner**, following the pattern in [../08-backend-services/rate-limiting-standards.md](../08-backend-services/rate-limiting-standards.md), scoped per partner API key rather than per user.
- **Legal and compliance review required before any partner data-sharing agreement** — see [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md) — since even aggregate, anonymized data sharing with a third party has DPDP implications that must be scoped in a partner agreement, not assumed.

## Non-Scope (explicitly out, even in the future plan)

Consumer-facing booking or payment flows initiated *from* a partner's own app or website into ActivityMate are out of scope for the foreseeable roadmap — this plan covers venue partners listing into ActivityMate's own discovery surface, not ActivityMate becoming embeddable infrastructure for third-party apps.

## Related Documents

- [api-versioning-strategy.md](api-versioning-strategy.md)
- [api-design-guidelines.md](api-design-guidelines.md)
- [webhook-standards.md](webhook-standards.md)
- [../19-payments-subscriptions/README.md](../19-payments-subscriptions/README.md)
- [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md)
