---
title: "Payment Provider Evaluation"
folder: 19-payments-subscriptions
purpose: "Provider choice"
pages_estimate: 4
prerequisites: "business-strategy"
dependencies: "none"
priority: P2
audience: "F, BE"
status: draft-v1
generated: 2026-07-15
---

# Payment Provider Evaluation

## Overview

Payments are explicitly deferred for MVP (PRD-001 frames monetization as post-launch), but the provider decision is made now so that the database and API layers are not designed in a way that blocks adding payments later. This document evaluates Razorpay, Stripe, and Cashfree for ActivityMate's eventual monetization needs (subscriptions, and potentially paid/premium activities) and recommends one.

## Context / Problem

ActivityMate's monetization, whenever it activates, will be paid for almost entirely by Indian users on Indian payment rails. The dominant payment method for this demographic is UPI (Unified Payments Interface) — not cards, not wallets. A provider evaluation for this product is therefore not a generic "which payment gateway is best" question; it is specifically "which provider gives the least-friction, most-trusted UPI experience to a 21-40 year old urban Indian user," because a checkout flow that defaults to card entry when the user expects to scan a UPI QR code or approve a UPI intent will suppress conversion regardless of how good the rest of the product is.

## Options Considered

**Razorpay.** Pros: India-native payment gateway, built from the ground up around UPI, cards, netbanking, and wallets as first-class equal options rather than UPI being a bolted-on afterthought; strong developer documentation and SDKs with straightforward FastAPI/Python integration; lower integration friction for India-specific needs (GST-compliant invoicing support, India-specific KYC/compliance handling already built in); UPI AutoPay support relevant to recurring subscription billing specifically. Cons: limited utility outside India if ActivityMate ever expands internationally, though that is not a near-term consideration for a Mumbai-launch, city-by-city India expansion strategy.

**Stripe.** Pros: best-in-class global developer tooling, documentation, and ecosystem; the default choice for most Western SaaS products and well understood by engineers. Cons: Stripe's UPI and India-specific payment method support has historically lagged India-native providers and has shifted over time as Stripe's India strategy has evolved — a real risk for a provider whose core competency (India rails) is exactly what ActivityMate needs most; global-first design means India-specific compliance conveniences (GST invoicing, local KYC) are less native than a India-first competitor's.

**Cashfree.** Pros: also India-native with solid UPI support, competitive pricing, and a reasonable feature set comparable to Razorpay. Cons: smaller ecosystem and community/developer mindshare than Razorpay, meaning less third-party tooling, fewer readily-available integration examples, and a thinner support/documentation surface for a small engineering team to lean on when something goes wrong.

## Recommendation & Rationale

**Recommend Razorpay**, given UPI's dominance among the target demographic and Razorpay's maturity as the India-native default choice with the deepest UPI-specific feature set (including UPI AutoPay, directly relevant if subscription-tier-design.md lands on a recurring-billing model). Razorpay's larger ecosystem also means better answers to integration questions when the (currently small) BE team hits an edge case, which matters more for a lean team than marginal fee differences between providers at this stage.

**Decision is provisional on business-strategy's monetization shape:** if the eventual model leans toward one-time payments only (e.g., paid premium activities) rather than recurring subscriptions, Razorpay remains the right choice, but the specific integration surface (Payment Links / Orders API vs. Subscriptions API) depends on that decision, tracked in subscription-tier-design.md.

## Related Documents

- [subscription-tier-design.md](subscription-tier-design.md) — tier structure this provider integration must support
- [billing-invoicing-architecture.md](billing-invoicing-architecture.md) — billing cycle built on this provider's APIs
- [pci-compliance-scope.md](pci-compliance-scope.md) — scope reduction enabled by this provider's tokenized checkout
- [../01-business-strategy/revenue-model-monetization-strategy.md](../01-business-strategy/revenue-model-monetization-strategy.md) — monetization strategy this provider choice serves
- [../01-business-strategy/pricing-strategy.md](../01-business-strategy/pricing-strategy.md) — pricing this provider must support
