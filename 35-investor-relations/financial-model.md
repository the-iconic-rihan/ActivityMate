---
title: "Financial Model"
folder: 35-investor-relations
purpose: "Financial model"
pages_estimate: 6
prerequisites: "business-strategy"
dependencies: "none"
priority: P0
audience: "F, I"
status: draft-v1
generated: 2026-07-15
---

# Financial Model

**Status:** Draft v1 — structural model with illustrative figures, tied to [unit-economics-cac-ltv.md](../01-business-strategy/unit-economics-cac-ltv.md).

## Scope

A 3-to-5-year model tied to unit-economics-cac-ltv.

## 1. Purpose and how to read this document

This document defines the **structure** of ActivityMate's financial model — the line items, the assumptions that drive them, and how they connect — so that when real numbers exist (post-launch cohort data, actual CAC, actual conversion) they can be dropped into this same skeleton without redesigning it under fundraising pressure. Every dollar figure below is **illustrative**: a placeholder shaped like a plausible early-stage Mumbai consumer-app number, not a forecast the founders are committing to. Replace the assumptions table first; the P&L below recalculates from it.

This model should stay consistent with [revenue-model-monetization-strategy.md](../01-business-strategy/revenue-model-monetization-strategy.md) (the staged freemium → subscription → commission path) and [unit-economics-cac-ltv.md](../01-business-strategy/unit-economics-cac-ltv.md) (CAC/LTV by channel). If those documents change their assumptions, this model must be re-run, not patched in isolation.

## 2. Core assumptions table (illustrative)

| Assumption | Year 1 | Year 2 | Year 3 | Year 4 | Year 5 |
|---|---|---|---|---|---|
| Cities live | 1 (Mumbai) | 1–2 | 3–4 | 6–8 | 10–12 |
| Registered users (cumulative, thousands) | 40 | 180 | 550 | 1,400 | 3,000 |
| Monthly active users (% of registered) | 35% | 32% | 30% | 30% | 30% |
| % of MAU completing ≥1 activity/month | 25% | 30% | 32% | 33% | 34% |
| Free-to-paid subscription conversion | — (freemium only) | 3% | 6% | 8% | 9% |
| Average subscription price (₹/month) | — | 249 | 279 | 299 | 329 |
| % of activities that are commission-eligible (paid/ticketed) | 0% | 2% | 5% | 8% | 10% |
| Average commission per paid activity (₹) | — | 60 | 70 | 75 | 80 |
| Blended CAC (₹/registered user) | 120 | 95 | 80 | 70 | 65 |
| Monthly churn (subscribers) | — | 9% | 7% | 6% | 5% |

*These figures are placeholders sized to be internally consistent (e.g. CAC declining as organic/host-led growth compounds, churn improving as trust-score-gated retention matures) — founders must replace them with figures grounded in actual Mumbai pilot data before this model is shown to any investor as fact.*

## 3. Revenue build

Revenue = Subscription revenue + Commission revenue, per the staged model:

- **Subscription revenue** = MAU × subscription conversion % × average price × 12
- **Commission revenue** = Completed activities/year × % commission-eligible × average commission per activity

| (₹, illustrative, lakhs) | Year 1 | Year 2 | Year 3 | Year 4 | Year 5 |
|---|---|---|---|---|---|
| Subscription revenue | 0 | 43 | 231 | 799 | 1,977 |
| Commission revenue | 0 | 8 | 51 | 179 | 448 |
| **Total revenue** | **0** | **51** | **282** | **978** | **2,425** |

Year 1 is intentionally revenue-zero: the model assumes a pure trust- and liquidity-building freemium phase in Mumbai before introducing paid tiers, consistent with the "safety and density before monetization" sequencing in [company-strategy-thesis.md](../00-foundation/company-strategy-thesis.md).

## 4. Cost structure

| (₹, illustrative, lakhs) | Year 1 | Year 2 | Year 3 | Year 4 | Year 5 |
|---|---|---|---|---|---|
| Engineering & product (salaries) | 180 | 240 | 340 | 480 | 620 |
| Trust & safety operations (verification review, moderation, ops) | 60 | 110 | 190 | 320 | 480 |
| Infrastructure & third-party (cloud, verification APIs, maps, storage) | 15 | 35 | 90 | 210 | 420 |
| Go-to-market & host-seeding | 45 | 90 | 160 | 300 | 480 |
| G&A (legal, finance, office) | 40 | 55 | 80 | 120 | 180 |
| **Total costs** | **340** | **530** | **860** | **1,430** | **2,180** |

Note the deliberate ordering: trust & safety operations cost grows roughly in line with engineering cost from Year 1 onward, not as an afterthought bolted on after a safety incident — this mirrors the hiring sequencing decision in [hiring-plan-org-chart.md](../36-hiring-people-culture/hiring-plan-org-chart.md).

## 5. P&L summary (illustrative)

| (₹, lakhs) | Year 1 | Year 2 | Year 3 | Year 4 | Year 5 |
|---|---|---|---|---|---|
| Revenue | 0 | 51 | 282 | 978 | 2,425 |
| Costs | 340 | 530 | 860 | 1,430 | 2,180 |
| **EBITDA** | **(340)** | **(479)** | **(578)** | **(452)** | **245** |
| Cumulative burn | (340) | (819) | (1,397) | (1,849) | (1,604) |

This illustrative shape — five years to approach breakeven, with the steepest loss in Year 3 as trust & safety and infra scale ahead of multi-city commission revenue — is typical for a trust-infrastructure-heavy consumer marketplace and should be stress-tested against real Mumbai pilot data as soon as it exists, not treated as guidance.

## 6. Unit economics cross-check

Every year's blended CAC and subscriber LTV in this model must reconcile against [unit-economics-cac-ltv.md](../01-business-strategy/unit-economics-cac-ltv.md). If that document's payback-period targets change, the CAC row in Section 2 and the go-to-market cost row in Section 4 must be updated together — they are not independent levers.

## 7. Funding requirement (illustrative)

Based on the cumulative burn curve above, ActivityMate requires roughly ₹8–10 crore across a pre-seed and seed round to reach the Year 3 checkpoint (multi-city expansion signal), before a Series A funds the Year 4–5 scale-up. Exact round sizing and timing depend on real burn data from the first two quarters post-launch and should be finalized in [pitch-deck.md](pitch-deck.md)'s ask slide, not decided independently here.

## 8. Open questions

- Real CAC by channel post-launch (organic host-led vs. any paid acquisition test).
- Actual conversion-to-paid rate once the subscription tier ships — the 3–9% range above is an industry-typical freemium placeholder, not Mumbai-specific data.
- Whether commission-eligible "paid/ticketed activities" reach meaningful volume before Year 2, which depends on power-host supply built out per [go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md).
