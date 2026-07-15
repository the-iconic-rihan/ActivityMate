---
title: "Feature Flag Strategy"
folder: 27-cicd-release
purpose: "Feature flags"
pages_estimate: 3
prerequisites: "branching-strategy"
dependencies: "analytics-experimentation"
priority: P1
audience: "BE, PM"
status: draft-v1
generated: 2026-07-15
---

# Feature Flag Strategy

## Overview

Trunk-based development (`branching-strategy.md`) depends on feature flags to keep `main` continuously deployable while incomplete or unproven work stays invisible to real users. This document defines the flag lifecycle — creation, rollout, and mandatory removal — and how flags connect to the experimentation platform in `../22-analytics-experimentation/ab-testing-platform-spec.md`.

## Flag Categories

- **Release flags** — wrap a feature that isn't finished yet (e.g., a new "Weekend Trip" activity-category flow being built over several PRs). Default off in all environments except dev. Short-lived by design: removed the moment the feature ships.
- **Experiment flags** — drive an A/B test (e.g., two variants of the trust-score display copy). Owned jointly with `../22-analytics-experimentation/ab-testing-platform-spec.md`; assignment and metrics collection go through that platform, not a bespoke random-bucketing implementation in application code.
- **Operational flags** — kill switches for expensive or risky subsystems (e.g., disable the ML-based matching ranker and fall back to simple recency ordering; disable a specific verification provider integration). Long-lived, owned by DevOps/BE jointly, tested in game-day drills alongside `../40-incident-disaster-recovery/incident-management-process.md`.
- **Permission/segment flags** — gate a feature to a cohort (e.g., city, verification level) rather than a percentage rollout — used for staged geographic expansion per `../41-localization-expansion/`.

## Lifecycle

1. **Creation:** a flag is created with a required owner, category (above), and a target removal date recorded at creation time — no flag is created without a stated plan for how it stops existing. Flags default to **off** everywhere except dev.
2. **Rollout:** release flags typically move dev(on) → staging(on) → prod(off, then percentage ramp: 5% → 25% → 100%) over days, watched against the dashboards in `../28-monitoring-observability/dashboards-spec.md`. Experiment flags follow the ramp/assignment logic owned by the experimentation platform instead.
3. **Full rollout:** once a release flag reaches 100% in prod and has been stable for 1-2 weeks, the flag is considered "graduated" — the code path it guards becomes the only path.
4. **Removal (mandatory):** graduated release flags are removed — dead conditional branches deleted, flag retired in the flag service — within one sprint of graduation. This is enforced as a checked item in `../38-planning-roadmap-techdebt/technical-debt-register.md`: an ungraduated-but-100%-flag older than 3 weeks is flagged as tech debt automatically. Operational kill switches are the explicit exception — they stay permanently by design.

## Implementation

Flags are evaluated server-side in the FastAPI monolith via a lightweight flag-evaluation client (an open-source flag SDK, e.g. a Unleash- or GrowthBook-compatible client, chosen to avoid building bucketing/targeting logic in-house) reading from a shared flag-config store (Postgres table + Redis cache for low-latency evaluation on every request, avoiding a flag-service network call in the hot path). Mobile clients receive flag state via the `/config` endpoint returned at app-session start, cached client-side for the session — the app does not poll for flag changes mid-session, trading a small propagation delay for simplicity and reduced request volume.

```python
# Example evaluation call, backend
if flags.is_enabled("release.weekend_trip_category", context={"user_id": user.id, "city": "mumbai"}):
    return build_weekend_trip_flow(user)
return legacy_activity_flow(user)
```

## Naming Convention

`<category>.<area>_<short_name>`, lowercase snake_case: `release.activities_weekend_trip`, `experiment.trust_score_copy_v2`, `ops.disable_ml_matching_ranker`, `segment.mumbai_night_activity_checkin`. The category prefix makes a flag's purpose and expected lifetime legible in code review without opening the flag service dashboard.

## Ownership & Review

Every flag has a named owner (usually the PR author who introduced it) tracked in the flag service's metadata, visible to PM for release flags and experiment flags since those affect what users actually see. A monthly flag audit (DevOps-run, PM-attended for release/experiment flags) reviews every active flag against its stated removal date and either closes it out or requires a written justification for the extension, logged in `../39-decision-records-rfcs/decision-log.md`.

## Related Documents

- [branching-strategy.md](branching-strategy.md)
- [release-management-process.md](release-management-process.md)
- [../22-analytics-experimentation/ab-testing-platform-spec.md](../22-analytics-experimentation/ab-testing-platform-spec.md)
- [../38-planning-roadmap-techdebt/technical-debt-register.md](../38-planning-roadmap-techdebt/technical-debt-register.md)
