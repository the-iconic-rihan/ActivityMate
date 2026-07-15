---
title: "Ai Feature Roadmap"
folder: 23-ai-ml-features
purpose: "Feature roadmap"
pages_estimate: 5
prerequisites: "product"
dependencies: "none"
priority: P2
audience: "PM, ML"
status: draft-v1
generated: 2026-07-15
---

# AI Feature Roadmap

## Overview

This roadmap ranks candidate AI/ML features for ActivityMate and sequences them. It is deliberately scoped P2 and built to be read *after* trust and safety fundamentals ([../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md)) and core matching ([../14-matching-recommendation/README.md](../14-matching-recommendation/README.md)) already exist without AI — AI here is a multiplier on a working core product, never a substitute for the rules-based trust engine described elsewhere in this repo.

## Ranking Methodology

Each candidate feature is scored on two axes:

- **Trust-impact** — does it reduce harm, catch fraud/toxicity earlier, or increase user confidence in who they're meeting? High trust-impact features protect the platform's core differentiator (trust-first, not dating, not anonymous).
- **Novelty** — does it feel delightful/differentiated but carry limited safety leverage? High-novelty, low-trust-impact features are valuable for retention but are explicitly not allowed to jump the queue ahead of safety-relevant ones, per this folder's stated philosophy.

Features are sequenced trust-impact-first. A feature with high novelty but low trust-impact (e.g., icebreaker suggestions) ships later than a feature with high trust-impact even if lower novelty (e.g., chat-toxicity detection), because misallocating scarce ML engineering time toward novelty before safety fundamentals directly contradicts Core Product Principle #3, "Safety Over Growth."

## Prioritized Roadmap

### Tier 1 — Trust & Safety Multipliers (build first, post-PMF)

1. **Chat-toxicity detection.** Real-time classification of in-app chat messages for harassment, coercion, and predatory language patterns, feeding directly into the report queue in [../24-admin-panel/moderation-console-spec.md](../24-admin-panel/moderation-console-spec.md) as auto-flagged, human-reviewed cases rather than auto-actioned ones at launch. Highest trust-impact: chat is the primary surface where a bad actor escalates before an in-person meetup, and Mumbai's explicit safety goal (avoiding solo night activities, PRD-001 §3) makes early detection of concerning chat patterns a direct mitigant against the platform's top-named risk (safety incidents, PRD-001 §11).
2. **Risk-signal NLP on profile and activity text.** Lightweight classifiers scanning bios and activity descriptions for spam patterns, scam language ("send money," "meet at my place first"), and policy-violating content before publish, complementing (not replacing) the rules-based checks in [../21-moderation-content-ops/README.md](../21-moderation-content-ops/README.md).
3. **Fraud/fake-profile risk scoring.** An ML classifier supplementing the deterministic trust score inputs (completed activities, ratings, reports, cancellation rate, response rate, account age — per PRD-001 §9) with pattern-based signals: device fingerprint reuse, image reverse-search hits, unnaturally fast verification-to-activity-creation timing. Feeds a score *alongside* the existing trust engine, not a replacement for it — the trust score stays interpretable and rules-based per [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md); ML risk scoring is an additional signal surfaced to human moderators, not an auto-ban trigger at launch.

### Tier 2 — Retention & Quality Multipliers (build second)

4. **Smart activity recommendations beyond rules-based matching.** An ML ranking layer on top of the existing category/location/trust-based matching engine ([../14-matching-recommendation/README.md](../14-matching-recommendation/README.md)), improving relevance as behavioral data accumulates. Explicitly sequenced after Tier 1 because it improves engagement, not safety — engagement gains are worthless if trust erodes first.
5. **No-show / cancellation prediction.** Predicts likelihood a joiner will no-show based on historical pattern, surfaced to hosts as a soft signal (not a hard block) when accepting join requests. Medium trust-impact (addresses a named PRD risk directly) but lower urgency than chat-toxicity since no-shows are inconvenient, not dangerous.

### Tier 3 — Delight & Novelty (build last, only once Tier 1 and 2 are stable)

6. **Icebreaker / conversation-starter suggestions.** LLM-generated, context-aware prompts ("You're both going to the same Sunday trekking meetup — ask about their gear") shown in chat to reduce first-message friction. Explicitly the lowest-priority Tier-1-adjacent feature named in this folder's README as an example of something that should *not* jump the queue.
7. **Auto-generated activity descriptions.** Helping hosts write better activity posts from a few tapped details, reducing friction in the Create Activity flow. Nice-to-have, no trust-impact.
8. **Personalized re-engagement copy.** LLM-assisted, personalized push/email copy for lapsed users. Growth-adjacent, lowest priority, and must be reviewed against [../32-growth-marketing/README.md](../32-growth-marketing/README.md) messaging guidelines to avoid manipulative dark-pattern re-engagement tactics, which would conflict with the trust-first brand.

## Sequencing Rationale Summary

| Feature | Tier | Trust-impact | Novelty | Depends on |
|---|---|---|---|---|
| Chat-toxicity detection | 1 | High | Low | [llm-usage-architecture.md](llm-usage-architecture.md), [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md) |
| Risk-signal NLP | 1 | High | Low | [../21-moderation-content-ops/README.md](../21-moderation-content-ops/README.md) |
| Fraud/fake-profile risk scoring | 1 | High | Medium | [ml-infra-mlops.md](ml-infra-mlops.md) |
| Smart recommendations | 2 | Medium | Medium | [../14-matching-recommendation/README.md](../14-matching-recommendation/README.md) |
| No-show prediction | 2 | Medium | Low | [ml-infra-mlops.md](ml-infra-mlops.md) |
| Icebreaker suggestions | 3 | Low | High | [llm-usage-architecture.md](llm-usage-architecture.md) |
| Auto-generated descriptions | 3 | Low | Medium | [llm-usage-architecture.md](llm-usage-architecture.md) |
| Personalized re-engagement copy | 3 | Low | Medium | [../32-growth-marketing/README.md](../32-growth-marketing/README.md) |

## Open Questions

- [TBD] — exact trigger threshold at which chat-toxicity detection moves from human-review-required to auto-action-eligible; requires false-positive rate data not available pre-launch.
- [TBD] — whether fraud risk scoring justifies an in-house model (see [ml-infra-mlops.md](ml-infra-mlops.md)) or can run entirely on hosted-API classification calls at Mumbai-launch volume.

## Related Documents

- [llm-usage-architecture.md](llm-usage-architecture.md) — implementation architecture for LLM-backed items on this roadmap
- [ml-infra-mlops.md](ml-infra-mlops.md) — infra for in-house classifiers (toxicity, fraud)
- [ai-safety-bias-policy.md](ai-safety-bias-policy.md) — fairness review gating Tier 2 recommendation work
- [../14-matching-recommendation/README.md](../14-matching-recommendation/README.md) — the rules-based engine this roadmap builds on top of, never replaces
- [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md) — trust score this roadmap augments
