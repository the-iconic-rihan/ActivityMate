---
title: "Llm Usage Architecture"
folder: 23-ai-ml-features
purpose: "LLM architecture"
pages_estimate: 5
prerequisites: "ai-feature-roadmap"
dependencies: "architecture"
priority: P2
audience: "BE, ML"
status: draft-v1
generated: 2026-07-15
---

# LLM Usage Architecture

## Overview

This document specifies which ActivityMate features use large language models, the vendor evaluation behind the LLM provider choice, and the cost-control guardrails that keep LLM spend predictable as the roadmap in [ai-feature-roadmap.md](ai-feature-roadmap.md) ships.

## Context / Problem

Several roadmap items — chat-toxicity detection, risk-signal NLP, icebreaker suggestions, auto-generated activity descriptions — need language understanding or generation beyond what deterministic rules can cover. ActivityMate's backend is a FastAPI modular monolith (PRD-001 §12) run by a small engineering team pre-Series A. The question is whether to call a hosted LLM API or run a self-hosted open-weight model.

## Options Considered

### Option A — Hosted LLM API (e.g., a frontier-model vendor's API)
**Pros:**
- No GPU infrastructure to provision, monitor, or scale — a direct fit with the "modular monolith, Docker Compose, minimal ops overhead pre-PMF" philosophy already governing the rest of the stack (PRD-001 §12).
- Best-in-class model quality out of the box, particularly important for toxicity/risk-signal detection where false negatives are a real safety cost, and for Hinglish/code-mixed chat text common in Mumbai, which smaller self-hosted models handle less reliably.
- Fast iteration: prompt changes ship without a retraining or redeployment cycle, letting Tier 1 safety features (per the roadmap) improve quickly as new abuse patterns are observed.
- Usage-based pricing keeps cost near zero at Mumbai-launch message volume, mirroring the same "cheap at low volume" logic used for the warehouse decision in [../22-analytics-experimentation/data-warehouse-architecture.md](../22-analytics-experimentation/data-warehouse-architecture.md).

**Cons:**
- Per-call cost scales with volume; unbounded usage (e.g., every chat message scanned) could become expensive as the platform grows — addressed below under cost-control guardrails.
- Data leaves the self-hosted perimeter on each call, requiring careful PII handling in prompts (never send raw phone/email/government ID; only pseudonymized `user_id` and message content, consistent with [../22-analytics-experimentation/analytics-event-taxonomy.md](../22-analytics-experimentation/analytics-event-taxonomy.md) PII rules).
- Vendor dependency for a safety-critical path (chat-toxicity detection) — mitigated by a fallback rules-based keyword/pattern layer that runs regardless of API availability, so a vendor outage degrades safety coverage rather than removing it entirely.

### Option B — Self-hosted open-weight model
**Pros:**
- No per-call cost ceiling; predictable infrastructure spend at high volume.
- Full data locality, avoiding any third-party data transfer question entirely.

**Cons:**
- Requires GPU infrastructure and MLOps capacity the team does not yet have (this is exactly the operational burden [ml-infra-mlops.md](ml-infra-mlops.md) is scoped to eventually build, but not before there's a proven need).
- Materially lower quality on Hinglish/code-mixed and safety-nuance tasks compared to frontier hosted models, at the model sizes realistically self-hostable pre-Series A.
- Slower to stand up — this is a multi-week infra project competing directly with Tier 1 safety-feature delivery time on the roadmap, the same tradeoff already resolved in favor of "buy" for the warehouse decision.

## Recommendation & Rationale

**Recommend a hosted LLM API pre-scale**, consistent with this folder's own README framing ("a hosted API is recommended pre-scale over a self-hosted model, for cost and operational simplicity"). Revisit self-hosting only when either (a) monthly LLM API spend exceeds a defined threshold ($3,000/month sustained) or (b) a specific feature's latency or data-residency requirement cannot be met by a hosted API — neither condition is expected at Mumbai-launch volume.

## Feature-to-Architecture Mapping

| Feature | LLM usage pattern | Latency requirement | Fallback if API unavailable |
|---|---|---|---|
| Chat-toxicity detection | Classification call per message, async, non-blocking to message delivery | <2s, async | Rules-based keyword/regex layer (always on) |
| Risk-signal NLP (bios/activity text) | Classification call on publish | <3s, blocking on publish | Manual moderation queue holds content until reviewed |
| Icebreaker suggestions | Generation call, on-demand when chat opened | <3s, user-facing | Feature hidden, no fallback needed (non-critical) |
| Auto-generated activity descriptions | Generation call, on-demand during Create Activity | <3s, user-facing | User writes description manually (default flow) |

## Cost-Control Guardrails

1. **Sampling, not universal scanning, for early rollout.** Chat-toxicity detection scans 100% of messages from day one (this is non-negotiable given its safety role), but lower-priority classification (e.g., risk-signal NLP on activity descriptions) can sample or apply only to new/low-trust-score accounts initially to control cost while coverage is validated.
2. **Prompt and response caching** for any repeated or templated calls (e.g., icebreaker suggestions for common activity-category pairs).
3. **Hard monthly budget alert** at 70% and 100% of a pre-set ceiling, alerting BE/ML Lead — mirrors the cost-threshold pattern used for the data warehouse decision.
4. **Model tiering** — cheaper/faster models for high-volume, lower-stakes classification (risk-signal NLP); higher-capability models reserved for chat-toxicity detection where false negatives carry the highest safety cost.
5. **No LLM call on the synchronous critical path of activity creation or join-request flows** — generation/classification features are additive and async so a slow or failed LLM call never blocks a core MVP action.

## Data Handling in Prompts

Per [../22-analytics-experimentation/data-governance-access-policy.md](../22-analytics-experimentation/data-governance-access-policy.md) tiering, only Tier 1/2-equivalent content (message text, activity descriptions, pseudonymous IDs) is sent to the LLM vendor. Government ID images, phone numbers, and email addresses are never included in a prompt. Vendor contract must include a Data Processing Agreement and a no-training-on-customer-data clause, reviewed by Legal per [../31-privacy-compliance-legal/README.md](../31-privacy-compliance-legal/README.md).

## Related Documents

- [ai-feature-roadmap.md](ai-feature-roadmap.md) — which features this architecture serves and in what order
- [ml-infra-mlops.md](ml-infra-mlops.md) — infra for any features that graduate to in-house models
- [ai-safety-bias-policy.md](ai-safety-bias-policy.md) — fairness review applying to LLM-driven classification output
- [../07-architecture/README.md](../07-architecture/README.md) — overall backend architecture this integrates into
- [../22-analytics-experimentation/data-governance-access-policy.md](../22-analytics-experimentation/data-governance-access-policy.md) — data handling tiers referenced above
