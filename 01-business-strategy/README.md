# 01-business-strategy/

**Phase:** Phase 1 — Foundation & Business  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** CEO / CPO  
**Depends on:** foundation  
**Document count:** 7

## Purpose

How ActivityMate makes money, competes, and wins Mumbai before anywhere else.

## Why this folder exists

A trust-first positioning is a UX and safety commitment, not a revenue model. This folder forces the monetization question early because it changes downstream architecture decisions — e.g. commission-based revenue needs payments infrastructure sooner than a pure subscription model would.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [business-model-canvas.md](business-model-canvas.md) | One-page model | Standard 9-block canvas as the compressed reference every other business document expands on. | 1 | foundation | none | P0 | ALL |
| [revenue-model-monetization-strategy.md](revenue-model-monetization-strategy.md) | Monetization decision | Compares subscription (predictable revenue, but adds friction at a zero-trust stage), freemium+boosts (proven Bumble-style model, risks a visibility arms race), commission-per-activity (aligns revenue with real usage, hard to price cleanly), and advertising (rejected outright — incompatible with the trust positioning). Recommends a staged path: freemium at launch, subscription introduced once retention data justifies it, commission reserved for paid/ticketed activities only. | 8 | business-model-canvas | payments-subscriptions | P0 | F, I, PM |
| [unit-economics-cac-ltv.md](unit-economics-cac-ltv.md) | CAC / LTV model | Projected CAC by channel (organic host-led vs paid) and LTV under each monetization path, with payback-period targets set against Series A readiness benchmarks. | 5 | revenue-model-monetization-strategy | analytics-experimentation | P0 | F, I |
| [competitive-landscape.md](competitive-landscape.md) | Competitor map | Direct competitors (Bumble BFF, Meetup), the real incumbent (WhatsApp groups and Facebook communities — informal but dominant), and indirect competitors (dating apps absorbing 'let's just meet up' intent). Positions trust infrastructure as the moat none of them have built. | 7 | business-model-canvas | market-research | P0 | F, I, PM, MK |
| [go-to-market-strategy.md](go-to-market-strategy.md) | Launch wedge | A single-city, single-persona wedge (relocated professionals, 25–34, South Mumbai / BKC / Powai) before horizontal expansion — mirroring Tinder's campus wedge and Uber's single-city playbook rather than a broad simultaneous launch, which would starve supply-side liquidity everywhere at once. | 6 | competitive-landscape | growth-marketing | P0 | F, G, MK |
| [pricing-strategy.md](pricing-strategy.md) | Price points | India price-sensitivity research applied to subscription tiers and freemium activity caps. | 4 | revenue-model-monetization-strategy | market-research | P1 | F, PM, G |
| [five-year-strategic-roadmap.md](five-year-strategic-roadmap.md) | Long-range plan | Mumbai → 5-city → national arc, monetization maturity curve, and staged AI investment. | 6 | all business-strategy docs | planning-roadmap-techdebt | P1 | F, I |
