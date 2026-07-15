---
title: "Billing Invoicing Architecture"
folder: 19-payments-subscriptions
purpose: "Billing architecture"
pages_estimate: 4
prerequisites: "payment-provider-evaluation"
dependencies: "none"
priority: P2
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Billing & Invoicing Architecture

## Overview

This document defines the backend architecture for billing cycles, invoice generation, and GST handling once subscriptions activate — built on Razorpay (payment-provider-evaluation.md) and designed to slot into the existing modular monolith without requiring a separate billing service at MVP monetization scale.

## Context / Problem

Billing is deceptively simple to prototype and genuinely hard to get right at the edges: prorated upgrades/downgrades mid-cycle, failed UPI AutoPay renewals, GST-compliant invoice generation (a legal requirement for any B2C service transaction in India, not optional polish), and reconciling ActivityMate's internal subscription state with Razorpay's own billing state when the two can drift apart (a webhook delivery failure, a user disputing a charge directly with their bank). This document defines the architecture that keeps those two sources of truth reconciled and keeps invoicing compliant from day one of monetization, rather than retrofitted after a GST compliance gap is discovered.

## Pipeline / Architecture (Ordered Flow)

**1. Subscription creation.** A user selecting a paid tier (subscription-tier-design.md) triggers a backend call to Razorpay's Subscriptions API, creating a Razorpay-side subscription linked to a UPI AutoPay mandate (or card, as fallback) the user authorizes client-side via Razorpay's checkout SDK. ActivityMate's own database records a corresponding `subscription` row (user, tier, Razorpay subscription ID, status, current period start/end) — Razorpay is the source of truth for payment execution; ActivityMate's database is the source of truth for what access that subscription grants.

**2. Webhook-driven state sync.** Razorpay emits webhooks for the events that matter: `subscription.charged` (successful renewal), `subscription.pending`/`payment.failed` (a renewal attempt failed — common with UPI AutoPay mandate limits or insufficient balance), `subscription.cancelled`. ActivityMate's backend exposes a webhook endpoint that verifies Razorpay's signature on every event (never trusts an unsigned or unverifiable payload) and updates the local `subscription` row accordingly. This webhook-first design means the app's access-control logic never calls out to Razorpay synchronously to check subscription status — it reads its own, webhook-kept-current local state, keeping feature-gating fast and Razorpay-outage-resilient.

**3. Grace period on failed renewal.** A failed UPI AutoPay renewal does not immediately revoke paid-tier access — a short grace period (a few days) is granted, during which the user is prompted in-app to update their payment method or approve a fresh mandate, consistent with standard subscription UX and avoiding an abrupt loss of a boosted activity listing mid-cycle over a transient payment hiccup. Access is revoked only if the grace period lapses without a successful retry.

**4. Invoice generation.** On every successful charge event, an invoice record is generated server-side (not merely relying on Razorpay's own invoice artifact, since ActivityMate needs India GST-compliant invoices under its own GSTIN, itemized per the subscription tier or boost purchased). Invoices are stored as immutable records (PDF generation) linked to the subscription/charge event, retained per statutory record-keeping requirements for tax purposes, and made available to the user in-app and via email.

**5. GST handling.** ActivityMate's invoices apply GST at the applicable rate for the digital service category, itemized separately from the subscription/boost price on the invoice (not baked silently into a single total), consistent with India tax-invoice requirements. GST collected is tracked in a dedicated ledger for finance/compliance reconciliation, kept separate from ActivityMate's own revenue recognition to simplify periodic GST filing.

**6. Reconciliation job.** A scheduled job periodically cross-checks ActivityMate's local subscription/charge records against Razorpay's own reporting API, flagging any drift (a webhook that was never received, a charge recorded on one side but not the other) for manual finance review — webhooks are reliable but not guaranteed, and this job is the safety net.

## Enforcement

Webhook signature verification is mandatory and cannot be bypassed even in staging/test environments, to keep the verification code path itself tested before go-live. Finance owns the reconciliation job's output; unexplained drift beyond a small tolerance blocks that billing cycle's close until investigated.

## Related Documents

- [payment-provider-evaluation.md](payment-provider-evaluation.md) — Razorpay integration this architecture is built on
- [subscription-tier-design.md](subscription-tier-design.md) — tiers this billing cycle charges for
- [refund-dispute-policy.md](refund-dispute-policy.md) — refund flow that reverses charges recorded here
- [pci-compliance-scope.md](pci-compliance-scope.md) — scope boundary this architecture respects (no raw card data touches ActivityMate's backend)
- [../11-api/webhook-standards.md](../11-api/webhook-standards.md) — general webhook handling standards this Razorpay integration follows
