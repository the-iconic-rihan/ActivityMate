---
title: "Pci Compliance Scope"
folder: 19-payments-subscriptions
purpose: "PCI scope"
pages_estimate: 3
prerequisites: "payment-provider-evaluation"
dependencies: "security"
priority: P2
audience: "SEC, BE"
status: draft-v1
generated: 2026-07-15
---

# PCI Compliance Scope

## Overview

This document defines how ActivityMate deliberately minimizes its PCI-DSS (Payment Card Industry Data Security Standard) compliance burden by never letting raw cardholder data touch its own infrastructure, using Razorpay's tokenized, hosted checkout instead.

## Context / Problem

PCI-DSS applies to any entity that stores, processes, or transmits cardholder data, and the compliance burden scales sharply with how much of that data flow the entity actually touches directly. Full PCI-DSS Level 1 compliance (self-assessment questionnaires, quarterly network scans, potentially an on-site audit) is a significant ongoing cost that is entirely avoidable for a company at ActivityMate's stage if the architecture is designed correctly from the start — the alternative, handling raw card numbers directly in ActivityMate's own backend, would impose that full burden on a small engineering team for no product benefit, since Razorpay already provides a compliant, tokenized alternative.

## The Actual Policy

**ActivityMate never receives, stores, transmits, or processes raw card data, in any form, at any point.** Card entry happens exclusively inside Razorpay's own hosted checkout UI (rendered via Razorpay's Flutter SDK as an embedded, Razorpay-controlled component, or a redirect/webview to Razorpay-hosted checkout) — the card number, CVV, and expiry never pass through ActivityMate's client code or backend servers in any request payload, log, or database field. What ActivityMate's backend receives back from Razorpay is a payment/subscription reference token and a success/failure status — never the underlying card data.

**UPI is out of PCI scope entirely** — UPI transactions do not involve card data at all (they're authenticated via UPI PIN inside the user's own banking/UPI app), which is a secondary benefit of UPI's dominance among ActivityMate's target demographic (payment-provider-evaluation.md): the majority of expected transaction volume is naturally outside PCI scope by the nature of the payment method itself, not just by architectural choice.

**Resulting PCI scope for ActivityMate: SAQ-A (Self-Assessment Questionnaire A) eligible** — the lightest PCI-DSS compliance tier, applicable specifically to merchants who fully outsource all cardholder data handling to a PCI-DSS-compliant third party (Razorpay carries its own, much heavier PCI compliance burden as the entity actually touching card data) and have no electronic storage, processing, or transmission of cardholder data on their own systems. This is confirmed, not assumed — the checkout integration pattern (hosted/embedded Razorpay UI, token-only backend) is a hard architectural requirement, and any future engineering change that would route card data through ActivityMate's own servers (e.g., a "save card" feature built without Razorpay's own tokenization vault) would move ActivityMate out of SAQ-A scope and must be reviewed by Security before implementation.

**Logging and error handling discipline:** because even inadvertent logging of card data (e.g., a debug log accidentally capturing a raw request payload from a misconfigured integration) can trigger PCI scope expansion, backend error-handling standards (08-backend-services/error-handling-standards.md) explicitly prohibit logging full request/response bodies on payment-related endpoints — only the Razorpay reference token and status are logged.

## Enforcement

Any new payment-related feature (e.g., saved payment methods, one-click renewal) is reviewed by Security against this scope boundary before implementation — the default assumption is that anything touching card data must go through Razorpay's own tokenization/vaulting, never a custom build. SAQ-A self-assessment is completed and refreshed annually once payments go live, owned jointly by Security and Finance.

## Related Documents

- [payment-provider-evaluation.md](payment-provider-evaluation.md) — Razorpay's role in this scope reduction
- [billing-invoicing-architecture.md](billing-invoicing-architecture.md) — token-only backend flow this scope depends on
- [../30-security/security-architecture-overview.md](../30-security/security-architecture-overview.md) — broader security architecture this fits into
- [../08-backend-services/error-handling-standards.md](../08-backend-services/error-handling-standards.md) — logging discipline referenced above
- [../30-security/secure-coding-standards.md](../30-security/secure-coding-standards.md) — coding standards payment endpoints must follow
