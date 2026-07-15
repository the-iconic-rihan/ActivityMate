---
title: "Survey Interview Findings"
folder: 02-market-research
purpose: "Synthesized findings"
pages_estimate: 9
prerequisites: "persona-research-raw-data"
dependencies: "none"
priority: P1
audience: "PM, UXR"
status: draft-v1
generated: 2026-07-15
---

# Survey & Interview Findings

## Methodology

This document codes the raw fieldwork in [persona-research-raw-data.md](persona-research-raw-data.md) — 18 illustrative interviews and a 240-respondent illustrative survey across the Mumbai wedge geography — into ranked themes. Coding followed two axes simultaneously: **frequency** (how often a theme recurred across respondents) and **safety-relevance** (how directly the theme bears on the trust/safety product surface), since a low-frequency but high-safety-relevance theme (e.g., a specific fear pattern reported by only a few respondents) can outweigh a high-frequency but low-stakes theme (e.g., a UI preference) in product prioritization.

## Findings, Ranked

**1. [Frequency: Very High / Safety-Relevance: High] No trusted way to vet a stranger before meeting them offline.** The single most recurring theme across both interviews (R4, R7, F6) and survey data (58% cite verified ID, 44% cite visible activity history as trust drivers). This is the core problem the product exists to solve and validates the trust score model in [PRD-001](../PRD-001_Master_Product_Requirements.md) Section 9 directly — it is not a nice-to-have feature but the primary unmet need.

**2. [Frequency: High / Safety-Relevance: Very High] Gendered asymmetry in comfort meeting strangers, especially 1:1 and in the evening.** Survey data shows a stark split (14% female vs. 34% male comfortable with 1:1 evening meetups), and this maps directly onto PRD-001's explicit User Goal ("avoid going alone for night activities post 10pm") and the Female Retention success metric. This is ranked above raw frequency would suggest because of its outsized product design implication — nearly every safety-critical default (group-first, public-venue-first, visibility of verification) should be evaluated against this asymmetry specifically, not against an average user.

**3. [Frequency: High / Safety-Relevance: High] Ghosting and no-shows erode trust in existing informal coordination.** 52% of survey respondents report being ghosted via a WhatsApp/Facebook-coordinated meetup; organizer interviews (C1, C4) independently describe ad-hoc workarounds (deposits, manual screening). This directly validates cancellation-rate and response-rate as trust score inputs and suggests these inputs should be weighted meaningfully, not treated as minor signals, since they address a named, high-frequency pain point.

**4. [Frequency: High / Safety-Relevance: Moderate] Existing informal groups (WhatsApp, Facebook, Instagram) are the default and are structurally inadequate at scale.** 74% cite WhatsApp groups as a current method; multiple interviews (R1, R4, R7) describe groups going dead, feeling cliquish to newcomers, or degenerating into noise. This validates the "improve an existing behavior" framing in [india-social-behavior-research.md](india-social-behavior-research.md) rather than "introduce a new behavior," which is a materially easier adoption problem for onboarding design in [../05-ux/](../05-ux/README.md).

**5. [Frequency: Moderate / Safety-Relevance: High] Group settings are strongly preferred over 1:1 for first meetings with strangers.** 71% comfortable with group daytime activities vs. 24% for 1:1 evening; interview evidence (S2, F6) reinforces this as an active preference, not just passive comfort. This should set the default minimum-participant count for new/unverified users' first activities, per implications already logged in [india-social-behavior-research.md](india-social-behavior-research.md).

**6. [Frequency: Moderate / Safety-Relevance: Moderate] Category confusion with dating apps is a real, recurring friction, not a hypothetical brand risk.** F6's explicit account of a Bumble BFF match assuming a date, plus general awareness of dating-app-adjacent stigma across interviews, validates the naming and positioning caution already adopted in [../00-foundation/naming-brand-story.md](../00-foundation/naming-brand-story.md) and the explicit Non-Goals in PRD-001.

**7. [Frequency: Moderate / Safety-Relevance: Low-Moderate] Supply-side (organizer) pain is a distinct and underserved problem from demand-side (participant) pain.** C1 and C4 independently describe manual screening overhead, no-show logistics cost, and ad-hoc trust workarounds (deposits) as organizer-specific burdens the product should solve, not just participant discovery. This elevates host/organizer tooling (see [../15-activities-communities/](../15-activities-communities/README.md)) as a first-class problem, not a secondary feature bolted onto a participant-first product.

**8. [Frequency: Moderate / Safety-Relevance: Low] Family/social-circle skepticism is a distinct onboarding barrier from personal user comfort.** S5's account of needing to convince parents specifically, and the broader pattern noted in [india-social-behavior-research.md](india-social-behavior-research.md), suggests onboarding and marketing materials may need a "legible to a skeptical third party" framing, not just persuasive copy aimed at the primary user.

**9. [Frequency: Low-Moderate / Safety-Relevance: Low] Willingness to pay is real but soft.** 33% yes / 41% maybe / 26% no on a hypothetical subscription question — not strong enough to support an aggressive paywall-first monetization approach, directly supporting the freemium-first staged approach already adopted in [../01-business-strategy/revenue-model-monetization-strategy.md](../01-business-strategy/revenue-model-monetization-strategy.md) rather than relitigating it here.

**10. [Frequency: Low / Safety-Relevance: Low] Category-specific existing culture varies (running/trekking stronger than board games/photography).** Noted in interviews and cross-referenced against [india-social-behavior-research.md](india-social-behavior-research.md) Finding 9 — a minor but useful input to initial category/host-recruitment prioritization in [../01-business-strategy/go-to-market-strategy.md](../01-business-strategy/go-to-market-strategy.md).

## Implications for Product

- Findings 1-3 should be treated as non-negotiable inputs to the trust score design in [../13-trust-safety-fraud/](../13-trust-safety-fraud/README.md) — verification visibility, gendered safety defaults, and no-show/response-rate weighting are validated, not speculative, requirements.
- Finding 5 should directly set the default minimum group size for activities created by new or low-trust-score users, enforced at the product logic layer in [../04-product/](../04-product/README.md).
- Finding 7 elevates organizer/host tooling to co-equal priority with participant-facing discovery in the MVP scope, which should be reflected in [../15-activities-communities/](../15-activities-communities/README.md) resourcing.
- Findings 6 and 8 should inform onboarding copy and the App Store listing description specifically, coordinated with [../00-foundation/naming-brand-story.md](../00-foundation/naming-brand-story.md) and [../32-growth-marketing/](../32-growth-marketing/README.md).

## Risks & Open Questions

- This coding is derived from the illustrative fieldwork in [persona-research-raw-data.md](persona-research-raw-data.md), which is itself explicitly a placeholder for real primary research — all rankings here should be re-validated once real interviews and survey data exist, and this document's status should be revisited at that point per [../00-foundation/documentation-governance.md](../00-foundation/documentation-governance.md).
- Open question: does the gendered safety asymmetry (Finding 2) hold at the same magnitude across the student and solo-traveller personas specifically, or is it more pronounced in the relocated-professional persona sampled most heavily — insufficient sample granularity in the current fieldwork to answer confidently.

## Related Documents

- [persona-research-raw-data.md](persona-research-raw-data.md)
- [india-social-behavior-research.md](india-social-behavior-research.md)
- [../03-user-research/trust-perception-research.md](../03-user-research/trust-perception-research.md)
- [../13-trust-safety-fraud/](../13-trust-safety-fraud/README.md)
