# 19-payments-subscriptions/

**Phase:** Phase 4 — Trust, Safety & Core Product  
**Priority:** P2 (P2 — needed for Series A / multi-city scale)  
**Owner:** BE / Finance  
**Depends on:** database, api  
**Document count:** 5

## Purpose

Monetization infrastructure — deferred but architected for in advance.

## Why this folder exists

The founder brief frames payments as future work, so this folder is marked P2, but the provider and compliance decisions are documented now so that database and api are not designed in a way that blocks adding payments later.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [payment-provider-evaluation.md](payment-provider-evaluation.md) | Provider choice | Compares Razorpay (India-native, strong UPI support, lower integration friction), Stripe (stronger global tooling, weaker India/UPI-era support), and Cashfree. Recommends Razorpay given UPI's dominance among the target demographic. | 4 | business-strategy | none | P2 | F, BE |
| [subscription-tier-design.md](subscription-tier-design.md) | Tier structure | Subscription tier structure tied directly to the monetization strategy defined in business-strategy. | 3 | payment-provider-evaluation | business-strategy | P2 | F, PM |
| [billing-invoicing-architecture.md](billing-invoicing-architecture.md) | Billing architecture | Billing cycle, invoicing, and GST handling. | 4 | payment-provider-evaluation | none | P2 | BE |
| [refund-dispute-policy.md](refund-dispute-policy.md) | Refund policy | Refund and chargeback handling policy. | 2 | subscription-tier-design | privacy-compliance-legal | P2 | F, LG |
| [pci-compliance-scope.md](pci-compliance-scope.md) | PCI scope | Scope reduction via a tokenized provider checkout, so ActivityMate never directly handles raw card data. | 3 | payment-provider-evaluation | security | P2 | SEC, BE |
