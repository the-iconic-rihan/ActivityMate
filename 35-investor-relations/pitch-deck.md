---
title: "Pitch Deck"
folder: 35-investor-relations
purpose: "Pitch deck"
pages_estimate: 12
prerequisites: "business-strategy"
dependencies: "none"
priority: P0
audience: "F, I"
status: draft-v1
generated: 2026-07-15
---

# Pitch Deck

**Status:** Draft v1 — narrative deck outline, slide-by-slide, tied directly to the founding thesis in [company-strategy-thesis.md](../00-foundation/company-strategy-thesis.md).

## Scope

The narrative deck, tied directly to the founding thesis in business-strategy.

## How to use this document

This is the slide-by-slide content outline for the seed and pre-seed pitch deck. Each section below corresponds to one slide (or two, where noted). The visual design system is owned by [branding-identity](../33-branding-identity/README.md); this document owns only the narrative and the numbers. Any figure marked *illustrative* is a placeholder structure the founders must replace with real numbers before this deck is shown to an investor — do not present illustrative figures as real traction.

---

## Slide 1 — Cover

**ActivityMate.** *Never do it alone.*

A trust-first, activity-first offline social network — launching in Mumbai.

Sub-line: "We are not a dating app. We help people who have free time but no one to spend it with — new arrivals, students, solo travellers, freelancers — find real people for real activities: coffee, cycling, treks, badminton, board games, weekend trips."

Founders: [Name, title, one-line background — TBD, to be supplied by founding team]. Contact and deck date in footer.

## Slide 2 — The Problem

Every day, people move to Mumbai for work, study, or a fresh start. They have free time and disposable income but no trusted circle yet. The current options are all broken for this specific need:

- **Waiting for office colleagues** — slow, and colleagues aren't always available outside work hours.
- **WhatsApp / Facebook / Instagram groups** — high noise, no verification, no accountability if someone doesn't show up or behaves badly.
- **Meetup-style apps** — event-first, not people-first; poor for spontaneous 1:1 or small-group plans.
- **Dating apps** — force a romantic/sexual frame onto what is often just "I want to go trekking with someone this weekend," and carry safety baggage and stigma that make many people (especially women) avoid using them for non-romantic plans at all.

The result: millions of person-hours of missed activities every month in a single city, and a widespread, quiet loneliness that shows up as "I wanted to go but didn't have anyone to go with."

## Slide 3 — The Insight

The unmet need is not romantic matching — it's **trusted activity partnership**. The gating factor isn't willingness, it's trust: can I believe this stranger will actually show up, behave safely, and be who they say they are?

Nobody has built infrastructure for that. Dating apps built trust infrastructure around romantic intent (photos, prompts, matching), which is the wrong shape for "join me for a 7am cycling ride." ActivityMate builds trust infrastructure around **activity intent** instead — verification, trust scores, and reputation earned through completed real-world meetups, not swipes.

## Slide 4 — The Solution

ActivityMate is a mobile app (Flutter, iOS + Android) where any user can:

1. **Create an activity** — pick a category (coffee, breakfast, cycling, trekking, badminton, movie, board games, weekend trip, photography walk, coworking, networking, and more), time, place, and group size.
2. **Discover and join activities** near them, filtered by trust score, verification level, and mutual interests.
3. **Chat** with confirmed participants only, inside the app — no phone numbers exchanged until both sides opt in.
4. **Rate and be rated** after each completed activity, feeding a visible, gameable-resistant trust score.
5. **Build a local reputation** over time — the more completed, well-rated activities, the more access and visibility a user earns.

Every one of these five loops is designed around the same principle: **trust before matching, safety over growth.**

## Slide 5 — Why Now

- **Post-pandemic normalization of stranger-meetup apps** (Bumble BFF, Meetup) has made "meet a verified stranger for an activity" culturally legible in urban India, but none of the incumbents are trust-infrastructure-first or India-calibrated.
- **Verification infrastructure has matured**: Aadhaar-linked ID checks, low-cost selfie-liveness APIs, and LinkedIn OAuth make a credible multi-level trust system buildable by a small team today, where five years ago it required a much larger compliance and ops function.
- **Urban migration into Mumbai continues to grow** (professionals, students, remote workers relocating from tier-2 cities), continuously refilling the target user pool that current alternatives serve worst.
- **Smartphone and UPI penetration** removes the two biggest friction points (device access, payment) for a freemium-to-paid mobile product in this segment.

## Slide 6 — Market Size

Structure (see [tam-sam-som.md](../02-market-research/tam-sam-som.md) for the full build):

- **TAM**: Urban Indians aged 21–40 in metro cities who report unmet demand for social/activity companionship — sized off Census urban-population and social-app-usage benchmarks.
- **SAM**: The same population in ActivityMate's initial launch cities (Mumbai first, then Tier-1 metros), reachable via the app's target channels.
- **SOM (Year 1–3)**: Mumbai's relocated-professional and student segments in the initial launch neighborhoods (South Mumbai, BKC, Powai, and student clusters), sized against realistic penetration rates for a new trust-network category.

*Note: precise TAM/SAM/SOM figures live in the market-research folder and should be pulled into this slide verbatim once finalized — this deck should never carry a second, drifting copy of that number.*

## Slide 7 — Product Walkthrough

Three or four annotated app screenshots (once available) showing: (1) the activity feed/discovery screen, (2) the create-activity flow, (3) a participant's trust-score and verification badges, (4) the post-activity rating screen. Caption each screenshot with the specific trust signal it surfaces — this slide's job is to make "trust is the product, not a footnote" visually obvious in under ten seconds of investor attention.

## Slide 8 — Traction

*Placeholder structure — to be populated post-launch. Do not fill with fabricated numbers before real data exists.*

Metrics to track and show once available, mirroring the [north-star-metric.md](../00-foundation/north-star-metric.md) definition:
- Weekly completed trusted meetups per active user (the north star)
- WAU / DAU and week-over-week growth
- Activity completion rate and host acceptance rate
- Female retention (a specific trust proof point, not a vanity metric)
- Average trust score trend and report rate (inverse safety indicator)

## Slide 9 — Business Model

Staged monetization path (full reasoning in [revenue-model-monetization-strategy.md](../01-business-strategy/revenue-model-monetization-strategy.md)):

1. **Launch**: Freemium — free activity creation/joining up to a monthly cap, verification tiers free.
2. **Growth**: Subscription tier introduced once retention data justifies it (unlimited activities, priority visibility, advanced filters, verified-only activity access).
3. **Maturity**: Commission on paid/ticketed activities (guided treks, workshops, curated experiences run by power hosts or partner organizers).

Advertising is explicitly rejected — incompatible with a trust-first positioning where the user must trust every element of the surface they're shown.

## Slide 10 — Go-to-Market

Single-city, single-persona wedge strategy (full detail in [go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md)): launch dense in South Mumbai / BKC / Powai targeting recently-relocated professionals aged 25–34, using host-led organic growth (seed a small number of highly-rated "power hosts" who run frequent, well-attended activities) rather than broad city-wide paid acquisition that would starve supply-side liquidity everywhere at once.

## Slide 11 — Competition & Moat

| | Bumble BFF / Meetup | WhatsApp & FB groups | Dating apps | **ActivityMate** |
|---|---|---|---|---|
| Activity-first, not profile-first | Partial | No structure | No | **Yes** |
| Verified, earned trust score | No | No | Weak | **Yes — core product** |
| India-calibrated trust stack (Aadhaar-class ID, selfie liveness) | No | No | No | **Yes** |
| Safety-over-growth as a stated product principle | No | No | No | **Yes** |

The moat is not the activity-discovery UI — that is replicable in a quarter. The moat is the **trust graph**: verified identities, completed-activity history, and ratings compound over time and are expensive and slow for a competitor to fake or replicate at the same integrity bar.

## Slide 12 — Team

Founding team bios and relevant prior experience. *[TBD — to be completed by the founding team with real names, backgrounds, and prior outcomes; do not populate with fabricated credentials.]* Advisors and any named domain experts (trust & safety, India consumer growth) belong here once confirmed.

## Slide 13 — Financials Snapshot

Pull the headline 3-year revenue and burn trajectory directly from [financial-model.md](financial-model.md) — this slide should never contain a number that model doesn't also show. Include: current monthly burn, runway at close, and the 18-month milestone the round is meant to buy (typically: launch, reach a defined WAU and activity-completion-rate bar in Mumbai, and demonstrate female retention parity as a safety proof point).

## Slide 14 — The Ask

- **Raising**: [amount — TBD, set by founders against the 18–24 month runway target in the financial model]
- **Use of funds** (illustrative split, to be finalized): ~45% engineering & product (backend, mobile, trust engine), ~20% trust & safety operations (verification review, moderation), ~20% Mumbai go-to-market (host seeding, local partnerships), ~15% G&A/runway buffer.
- **Milestone this round buys**: Mumbai PMF signal — sustained weekly completed trusted meetups per active user growth, activity completion rate above target, and no material safety incident — sufficient to raise a Series A for multi-city expansion per [five-year-strategic-roadmap.md](../01-business-strategy/five-year-strategic-roadmap.md).

**Close**: "We're not building another dating app. We're building the trust layer for real life in Indian cities — starting with the simple, universal problem of not having anyone to do things with."
