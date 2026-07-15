---
title: "Feature Prioritization Framework"
folder: 04-product
purpose: "Prioritization method"
pages_estimate: 5
prerequisites: "product-strategy-doc"
dependencies: "analytics-experimentation"
priority: P0
audience: "PM"
status: draft-v1
generated: 2026-07-15
---

# Feature Prioritization Framework

## Overview

ActivityMate's roadmap will fill up faster than it can be built — every launch document from PRD-001 onward already lists more features than an MVP team can ship. This document sets the method by which competing feature requests get ranked, so prioritization is a repeatable process rather than whoever argues loudest in planning. It also names the point at which the method itself must change, since the right framework pre-launch is not the right framework post-launch.

## Context / Problem

Standard prioritization frameworks assume you have something none of them account for well at once: usage data, low gaming risk, and the ability to separate "must exist for the product to be trustworthy" from "would delight users if it existed." ActivityMate pre-launch has none of the first (no usage data yet) and an unusually strong version of the second problem — trust-and-safety features (verification tiers, panic button, report flows) are not typical "nice to have" features that trade off cleanly against growth features. Treating them as directly comparable on a single score would be a category error: a feature that prevents a safety incident is not fungible with a feature that raises signup conversion by two points, even if their naive scores were close.

## Options Considered

**RICE (Reach × Impact × Confidence ÷ Effort).** Produces a clean, comparable numeric score across a large backlog and is the industry default at growth-stage companies. Its weakness is total dependence on Reach and Confidence inputs, both of which require real usage data ActivityMate does not have pre-launch — every RICE score pre-launch is a guess dressed as a number, which creates false precision and lets confident-sounding estimates dominate decisions that should be treated as uncertain.

**ICE (Impact × Confidence ÷ Ease).** Faster to run than RICE — good for quick triage in a small team — but its simplicity is also its failure mode: three single-digit scores multiplied together are trivially gameable by whoever writes the ticket, and it has no mechanism to distinguish a "must-be" feature (phone verification) from a "delighter" feature (streaks, badges). Everything looks like a number on the same scale, which is exactly the category error described above.

**Kano Model.** Classifies each feature into Must-Be (its absence causes dissatisfaction; its presence is simply expected, not exciting — e.g., working phone-OTP verification), Performance (satisfaction scales linearly with how well it's done — e.g., activity-discovery relevance), Attractive/Delighter (unexpected, disproportionately delights — e.g., a trust-score badge that feels rewarding to earn), and Indifferent (users don't care either way). Kano's strength here is exactly ActivityMate's structural problem: it gives Must-Be trust/safety features a protected category that cannot be starved by a Delighter growth feature scoring higher on a blended index. Its weakness is that it traditionally requires user surveys (a Kano questionnaire) to classify features, which is slow and — pre-launch — has a thin respondent pool to draw on.

## Recommendation & Rationale

**Use a lightweight Kano classification pre-launch; transition to RICE once real usage and cohort data exist (target: 90 days post-Mumbai-launch, contingent on reaching statistically meaningful weekly active cohorts).**

Pre-launch, every feature proposal is first tagged Must-Be, Performance, Attractive, or Indifferent by the PM + Head of Trust & Safety jointly (not survey-driven at this stage — informed by [../03-user-research/trust-perception-research.md](../03-user-research/trust-perception-research.md) and [../02-market-research/india-social-behavior-research.md](../02-market-research/india-social-behavior-research.md) instead of a formal questionnaire, given the small pre-launch user pool). Must-Be features are non-negotiable for launch scope regardless of effort. Only within the Performance and Attractive buckets does the team then do lightweight ICE-style effort/impact triage to sequence order — ICE's speed is useful once the Kano gate has already protected the trust floor.

Post-launch, once activity-completion, rating, and cohort-retention data exist in volume, the team switches to RICE as the primary backlog-ranking tool, because RICE's Reach and Confidence inputs become real numbers instead of guesses, and the marketplace of feature ideas grows past what Kano's four-bucket coarseness can usefully differentiate. Kano is retained permanently, however, as the mandatory *first pass* for anything touching trust, safety, or verification — even post-PMF, a high-RICE-score growth feature cannot bypass Must-Be protection for safety features. This is the direct operational implementation of [product-principles.md](product-principles.md) Principle 3 (Safety Over Growth).

## Risks & Open Questions

- Kano classification without survey data pre-launch relies on team judgment, which is a real bias risk — mitigate by anchoring every Must-Be classification to a specific research citation, not intuition.
- The RICE transition trigger ("90 days post-launch, statistically meaningful cohorts") needs a concrete numeric threshold before launch, to be defined jointly with [../22-analytics-experimentation/north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md) rather than decided ad hoc later.
- Risk of Kano-bucket gaming: a team member could mislabel a growth feature as Must-Be to jump the queue. Mitigation is joint PM + Trust & Safety sign-off on all Must-Be tags, logged in the roadmap.

## Related Documents

- [product-strategy-doc.md](product-strategy-doc.md)
- [product-principles.md](product-principles.md)
- [experimentation-framework.md](experimentation-framework.md)
- [../22-analytics-experimentation/README.md](../22-analytics-experimentation/README.md)
- [../38-planning-roadmap-techdebt/quarterly-roadmap-process.md](../38-planning-roadmap-techdebt/quarterly-roadmap-process.md)
