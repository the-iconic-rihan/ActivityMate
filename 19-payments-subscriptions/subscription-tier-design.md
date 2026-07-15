---
title: "Subscription Tier Design"
folder: 19-payments-subscriptions
purpose: "Tier structure"
pages_estimate: 3
prerequisites: "payment-provider-evaluation"
dependencies: "business-strategy"
priority: P2
audience: "F, PM"
status: draft-v1
generated: 2026-07-15
---

# Subscription Tier Design

## Overview

This document defines ActivityMate's subscription tier structure — what a free user gets versus what a paying user unlocks — designed to activate post-launch, once the core trust and density loop is validated, without requiring any re-architecture of the free product experience already shipped.

## Context / Problem

Monetization is deliberately deferred (PRD-001 treats it as future work; this folder is P2), but tier design has a real dependency on product principles that are already settled: Safety Over Growth and Trust Before Matching (PRD-001 Section 6) mean that ActivityMate cannot monetize by paywalling safety or trust features — a "pay to see verified badges" or "pay for background checks" model would directly contradict the trust-first positioning and create a two-tier safety experience, which is not an option regardless of revenue upside. Monetization has to sit on top of convenience and discovery, not gate safety.

## The Actual Policy

**Free tier (default, permanent — not a trial):** full access to the core loop — creating and joining activities, chat, verification, trust score, ratings, and communities. This is non-negotiable: the free tier must remain a complete, safe, fully-functional product on its own, because local network density (the core growth mechanic) depends on the free tier having no artificial ceiling that suppresses activity creation or participation.

**Paid tier — convenience, discovery, and reach, not safety:** candidate paid features, to be finalized against business-strategy's monetization strategy, cluster around three categories that do not touch trust or safety: (1) **discovery boosts** — increased visibility for a hosted activity in the discovery feed (20-search-discovery), similar in spirit to boosted-listing models on other marketplaces, sold per-activity or bundled monthly; (2) **advanced filters** — richer search/filter capability (20-search-discovery/filters-facets-spec.md) for power users browsing activities, e.g., saved filter sets or expanded radius; (3) **profile enhancements** — non-trust cosmetic or informational profile features (e.g., extended bio sections, activity history highlights) that make a profile more expressive without implying additional verification or trust that hasn't actually been earned.

**Explicitly excluded from any paid tier:** verification levels, trust score visibility, safety reporting/blocking tools, and moderation response times — these remain identical for free and paid users, since making safety a paid feature would be both a trust-model contradiction and a reputational risk if ActivityMate were ever perceived as monetizing user safety.

**Billing model:** monthly and annual recurring subscription (via Razorpay's UPI AutoPay, per payment-provider-evaluation.md), with per-activity one-time boosts as a lower-commitment complementary option for users who want the discovery benefit without a subscription commitment.

**Pricing:** deferred entirely to business-strategy's pricing-strategy.md and unit-economics-cac-ltv.md work — this document defines tier *structure and boundaries*, not price points, since pricing depends on unit economics data ActivityMate does not have pre-launch.

## Enforcement

Any proposed paid feature is checked against the safety/trust exclusion list above before being added to a tier — this check is owned jointly by PM and the Head of Trust & Safety, not PM alone, given the direct conflict-of-interest risk between revenue pressure and trust-model integrity.

## Related Documents

- [payment-provider-evaluation.md](payment-provider-evaluation.md) — provider this billing model is built on
- [billing-invoicing-architecture.md](billing-invoicing-architecture.md) — billing cycle mechanics for these tiers
- [../01-business-strategy/revenue-model-monetization-strategy.md](../01-business-strategy/revenue-model-monetization-strategy.md) — monetization strategy this tier structure implements
- [../01-business-strategy/pricing-strategy.md](../01-business-strategy/pricing-strategy.md) — price points for these tiers
- [../20-search-discovery/filters-facets-spec.md](../20-search-discovery/filters-facets-spec.md) — advanced filter feature referenced above
- [../13-trust-safety-fraud/trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md) — trust score policy this tier structure must not paywall
