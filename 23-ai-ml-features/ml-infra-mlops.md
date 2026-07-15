---
title: "Ml Infra Mlops"
folder: 23-ai-ml-features
purpose: "MLOps infra"
pages_estimate: 4
prerequisites: "llm-usage-architecture"
dependencies: "infrastructure-cloud"
priority: P2
audience: "ML, DO"
status: draft-v1
generated: 2026-07-15
---

# ML Infrastructure & MLOps

## Overview

This document specifies training and serving infrastructure for any model ActivityMate trains and hosts in-house, as opposed to calling a hosted LLM API (see [llm-usage-architecture.md](llm-usage-architecture.md)). It applies primarily to fraud/fake-profile risk scoring and, if it graduates beyond a hosted-API classification call, no-show prediction — both named in [ai-feature-roadmap.md](ai-feature-roadmap.md).

## When In-House Models Are Justified

Per the roadmap and the LLM architecture decision, the default is to lean on hosted APIs and simple rules-based logic as long as possible. In-house model training is justified only when:
- The signal is structured/tabular (device fingerprints, timing patterns, behavioral sequences) rather than natural language — a poor fit for an LLM API and a good fit for a lightweight classical ML model (gradient-boosted trees are the expected default, not deep learning, given data volume at Mumbai scale).
- The feature needs to run synchronously and cheaply at high call volume where per-call hosted-API pricing would be uneconomical (e.g., risk-scoring every join request).
- Data cannot leave the self-hosted perimeter for a specific, named reason.

This keeps the MLOps footprint minimal and avoids building generalized ML infrastructure speculatively — consistent with this folder's README framing that AI/ML work is P2, deferred, and must not compete with trust-and-safety fundamentals for engineering time.

## Training Infrastructure

- **Data source:** feature-engineered tables built from the analytics warehouse ([../22-analytics-experimentation/data-warehouse-architecture.md](../22-analytics-experimentation/data-warehouse-architecture.md)) and operational PostgreSQL tables (trust score components, activity/report history), joined via pseudonymous `user_id` per the governance rules in [../22-analytics-experimentation/data-governance-access-policy.md](../22-analytics-experimentation/data-governance-access-policy.md).
- **Training compute:** on-demand cloud GPU/CPU instances provisioned only for training runs (not a standing cluster), consistent with the "avoid a standing self-hosted heavy footprint pre-scale" logic already applied to the warehouse and LLM decisions.
- **Experiment tracking:** a lightweight open-source tracker (e.g., MLflow) logging model version, training data snapshot, feature set, and evaluation metrics for every training run — required for both reproducibility and for the fairness review in [ai-safety-bias-policy.md](ai-safety-bias-policy.md), which needs to audit a specific model version's behavior.
- **Retraining cadence:** monthly at most for fraud/risk models in the first year — fraud patterns evolve, but Mumbai-launch data volume does not justify more frequent retraining, and infrequent retraining keeps the fairness review process (which must review each new model version) tractable.

## Serving Infrastructure

- **Serving pattern:** models are served as a lightweight internal inference service within the existing Docker Compose / modular monolith deployment (per [../25-infrastructure-cloud/README.md](../25-infrastructure-cloud/README.md) and [../26-docker-containers/README.md](../26-docker-containers/README.md)), not a separate standing ML platform — one more instance of the "don't build microservices infrastructure before PMF" principle.
- **Latency budget:** fraud/risk scoring must return within the same request cycle as trust-score-dependent actions (join-request accept/reject, activity publish) — target p99 under 200ms, achievable with gradient-boosted tree inference, which is one of the reasons deep learning / hosted-LLM approaches are not used for this specific model.
- **Model versioning and rollback:** every deployed model is tagged with a version; the serving layer supports instant rollback to the previous version if a new model regresses a monitored metric (see below). No model ships directly to 100% traffic — new versions shadow-score against the live model for at least one week before cutover.

## Monitoring

- **Input drift:** monitor feature distributions weekly against the training-data baseline; significant drift (e.g., a shift in device-fingerprint patterns after an app update) triggers a retraining review.
- **Output monitoring:** track the model's score distribution and its downstream effect on real outcomes — specifically, does a high fraud-risk score actually correlate with subsequent reports or bans? This closes the loop between the model and ground truth from [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md).
- **Fairness monitoring:** score distributions sliced by gender, verification level, and account age reviewed against [ai-safety-bias-policy.md](ai-safety-bias-policy.md) thresholds before and after every retraining.

## Human-in-the-Loop Requirement

No in-house model output directly bans, suspends, or auto-rejects a user at launch. Every model output (fraud risk score, no-show likelihood) is surfaced as a signal to either the user (a soft warning to a host) or a human moderator in [../24-admin-panel/moderation-console-spec.md](../24-admin-panel/moderation-console-spec.md) — consistent with the roadmap's explicit statement that ML risk scoring augments, never replaces, the rules-based trust engine.

## Related Documents

- [ai-feature-roadmap.md](ai-feature-roadmap.md) — which features require this infrastructure
- [llm-usage-architecture.md](llm-usage-architecture.md) — the hosted-API alternative this infra supplements, not replaces
- [ai-safety-bias-policy.md](ai-safety-bias-policy.md) — fairness review gating every model version
- [../22-analytics-experimentation/data-warehouse-architecture.md](../22-analytics-experimentation/data-warehouse-architecture.md) — training data source
- [../25-infrastructure-cloud/README.md](../25-infrastructure-cloud/README.md) — deployment environment
- [../13-trust-safety-fraud/README.md](../13-trust-safety-fraud/README.md) — the rules-based trust engine models augment
