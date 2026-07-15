---
title: "Refund Dispute Policy"
folder: 19-payments-subscriptions
purpose: "Refund policy"
pages_estimate: 2
prerequisites: "subscription-tier-design"
dependencies: "privacy-compliance-legal"
priority: P2
audience: "F, LG"
status: draft-v1
generated: 2026-07-15
---

# Refund & Dispute Policy

## Overview

This document sets the concrete rules for when a paid-tier subscriber or boost purchaser is entitled to a refund, and how a payment dispute (a UPI/card chargeback initiated through the user's bank rather than through ActivityMate directly) is handled.

## Context / Problem

Because ActivityMate's paid tier is deliberately scoped to convenience and discovery features rather than safety or trust (subscription-tier-design.md), refund scenarios are lower-stakes than they would be on a platform selling access to safety features — but they still need a clear, published policy before launch, both because Razorpay-integrated checkouts are expected to link a merchant refund policy, and because an undefined refund process becomes an ad hoc, inconsistent support burden the moment the first paying user asks for their money back.

## The Actual Policy

**Subscription refunds:** ActivityMate does not offer pro-rated refunds for mid-cycle cancellation — cancelling a subscription stops future renewal but the current paid period runs to its already-paid-for end date, consistent with standard SaaS subscription norms and avoiding complex proration logic for a low-stakes feature set. A full refund is granted, no questions asked, within a short window (a few days) of initial subscription purchase, covering the case of an accidental or immediately-regretted signup, mirroring app-store-style refund windows users already expect.

**Boost/one-time-purchase refunds:** a discovery boost purchased for a specific activity is refunded in full if the underlying activity is cancelled before the boost period begins or delivers materially reduced value (e.g., the activity itself is removed by moderation before the boost ran) — the user should not pay for visibility on an activity that no longer exists. Boosts are not refunded once they have run and delivered the purchased visibility, since the service was rendered as sold.

**Failed-charge disputes:** if a user disputes a charge directly with their bank (a UPI or card chargeback) rather than contacting ActivityMate support first, the disputed subscription is suspended pending resolution, and the user is notified in-app to resolve the underlying dispute — ActivityMate does not restore access mid-dispute, since Razorpay itself holds funds pending the bank's chargeback decision.

**Fraud/abuse exception:** refund requests tied to an account later found to violate community guidelines (content-moderation-policy.md) or engaged in payment fraud are handled case-by-case by Finance and Legal jointly, not auto-approved under the standard windows above.

## Enforcement

Refund requests within the standard windows are self-service in-app wherever technically feasible (triggering an automated Razorpay refund call), with anything outside the standard windows routed to a support ticket (34-operations-support/customer-support-playbook.md) for manual Finance review. All refund and dispute decisions are logged for GST/accounting reconciliation (billing-invoicing-architecture.md).

## Related Documents

- [subscription-tier-design.md](subscription-tier-design.md) — tiers and boosts this refund policy applies to
- [billing-invoicing-architecture.md](billing-invoicing-architecture.md) — billing state this policy reverses
- [../31-privacy-compliance-legal/terms-of-service-framework.md](../31-privacy-compliance-legal/terms-of-service-framework.md) — ToS this refund policy is published under
- [../34-operations-support/customer-support-playbook.md](../34-operations-support/customer-support-playbook.md) — support handling of out-of-window refund requests
