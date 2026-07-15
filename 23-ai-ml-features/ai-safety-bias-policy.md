---
title: "Ai Safety Bias Policy"
folder: 23-ai-ml-features
purpose: "Fairness review"
pages_estimate: 3
prerequisites: "matching-recommendation"
dependencies: "none"
priority: P1
audience: "ML, LG"
status: draft-v1
generated: 2026-07-15
---

# AI Safety & Bias Policy

## Overview

This policy defines the fairness review process ActivityMate applies to any ML or LLM-influenced output that affects who a user sees, matches with, or is flagged to a moderator. It is scoped P1 despite living in a P2 folder because discriminatory pattern risk is a launch-blocking concern the moment matching or recommendation logic incorporates any learned (as opposed to purely rules-based) component — even before AI features formally launch, this policy governs the matching engine described in [../14-matching-recommendation/README.md](../14-matching-recommendation/README.md).

## Why This Matters for ActivityMate Specifically

ActivityMate is explicitly not a dating platform (PRD-001 §1) and is built around trust and activity-fit, not attraction. This is a meaningful fairness constraint: a matching or recommendation system that inadvertently learns to under-surface activities to certain genders, deprioritize certain neighborhoods (a proxy for socioeconomic or religious composition in a city as diverse as Mumbai), or under-rank users with certain names (a proxy for caste or community) would both violate the platform's stated mission and create real legal and reputational exposure. Because Female Retention is a named success metric (PRD-001 §10), any pattern that disproportionately reduces match quality or visibility for women is treated as both a fairness failure and a core product failure simultaneously.

## Protected Dimensions

Fairness review evaluates model and ranking output across:
- **Gender** — given the explicit female-retention success metric and the platform's safety-first design goal for solo participation.
- **Neighborhood / locality** (a real proxy risk in Mumbai for religion, caste, and class) — reviewed even though location is a legitimate, necessary matching input (proximity matters for real-world activities); the review checks for *disproportionate* outcome disparities beyond what proximity alone explains.
- **Name-inferred ethnicity/religion/caste signals** — no feature is deliberately engineered on these, but downstream model behavior is audited for it emerging incidentally.
- **Account age / verification level** — a legitimate trust signal, but reviewed to ensure it does not become a proxy that systematically excludes recent migrants to Mumbai (a core target persona per PRD-001 §5 — new employees, students, and recently relocated families are, definitionally, low-account-age users).
- **Language** — English-first at launch (per [../41-localization-expansion/multi-language-strategy.md](../41-localization-expansion/multi-language-strategy.md)) creates a real risk of underserving non-English-primary users in matching quality; tracked even though full multi-language support is a later-stage roadmap item.

## Review Process

1. **Pre-launch review for any new model or ranking feature.** Before a new ML-influenced feature (from [../ai-feature-roadmap.md](ai-feature-roadmap.md) Tier 1 or 2) ships to more than an internal test cohort, ML Lead and Legal jointly review: what protected-dimension data (or proxies) the model has access to, and outcome distributions on a held-out evaluation set sliced by each protected dimension above.
2. **Quantitative thresholds.** A feature fails review if any protected-dimension slice shows an outcome disparity (e.g., match rate, activity visibility, moderation flag rate) exceeding 20% relative difference from the platform-wide baseline without a legitimate, documented explanation (e.g., genuine geographic sparsity is a legitimate explanation; unexplained gender disparity is not).
3. **Ongoing monitoring, not just pre-launch.** Every retraining event logged in [ml-infra-mlops.md](ml-infra-mlops.md) triggers a re-check of the same slices — a model that passed review at launch can drift into an unfair state as behavioral data accumulates.
4. **Escalation path.** A failed review blocks launch or triggers an immediate rollback for a live feature, escalated to CTO and Legal jointly; this mirrors the auto-abort severity used for guardrail-metric breaches in [../22-analytics-experimentation/ab-testing-platform-spec.md](../22-analytics-experimentation/ab-testing-platform-spec.md), and safety-relevant fairness failures (e.g., a toxicity classifier under-flagging harassment directed at women) are treated with the same urgency as a live safety incident under [../40-incident-disaster-recovery/README.md](../40-incident-disaster-recovery/README.md).

## What This Policy Does Not Cover

Deliberate, disclosed personalization (e.g., a user filtering activities by category preference) is not a fairness concern — this policy addresses *unintended* disparate outcomes from learned system behavior, not user-controlled filtering. It also does not cover the moderation-decision fairness of individual human reviewers, which is addressed operationally in [../21-moderation-content-ops/README.md](../21-moderation-content-ops/README.md).

## Related Documents

- [ai-feature-roadmap.md](ai-feature-roadmap.md) — features subject to this review
- [ml-infra-mlops.md](ml-infra-mlops.md) — model versioning this review is anchored to
- [../14-matching-recommendation/README.md](../14-matching-recommendation/README.md) — primary system under review
- [../22-analytics-experimentation/ab-testing-platform-spec.md](../22-analytics-experimentation/ab-testing-platform-spec.md) — guardrail-metric philosophy this policy mirrors
- [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md) — legal basis and coordination point
