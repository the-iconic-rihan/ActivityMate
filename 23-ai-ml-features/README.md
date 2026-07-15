# 23-ai-ml-features/

**Phase:** Phase 5 — Data, AI & Admin  
**Priority:** P2 (P2 — needed for Series A / multi-city scale)  
**Owner:** AI / ML Lead  
**Depends on:** database, matching-recommendation, analytics-experimentation  
**Document count:** 4

## Purpose

AI-powered features beyond core recommendation — icebreakers, chat-toxicity detection, risk scoring.

## Why this folder exists

Scoped as P2 and deliberately deferred — AI features are a multiplier on an already-working core product, not a substitute for it. Building this before trust and safety fundamentals exist would misallocate scarce engineering time.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [ai-feature-roadmap.md](ai-feature-roadmap.md) | Feature roadmap | Candidate features ranked by trust-impact versus novelty — chat-toxicity detection and risk-signal NLP are prioritized ahead of icebreaker-suggestion features. | 5 | product | none | P2 | PM, ML |
| [llm-usage-architecture.md](llm-usage-architecture.md) | LLM architecture | Which features use LLMs, vendor evaluation (a hosted API is recommended pre-scale over a self-hosted model, for cost and operational simplicity), and cost-control guardrails. | 5 | ai-feature-roadmap | architecture | P2 | BE, ML |
| [ml-infra-mlops.md](ml-infra-mlops.md) | MLOps infra | Training and serving infrastructure for any in-house models, such as fraud or toxicity classifiers. | 4 | llm-usage-architecture | infrastructure-cloud | P2 | ML, DO |
| [ai-safety-bias-policy.md](ai-safety-bias-policy.md) | Fairness review | A fairness review process to prevent discriminatory patterns from emerging in matching or recommendation output. | 3 | matching-recommendation | none | P1 | ML, LG |
