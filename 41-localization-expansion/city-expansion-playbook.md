---
title: "City Expansion Playbook"
folder: 41-localization-expansion
purpose: "Expansion playbook"
pages_estimate: 5
prerequisites: "city-expansion-readiness-scoring"
dependencies: "growth-marketing"
priority: P2
audience: "G, PM"
status: draft-v1
generated: 2026-07-15
---

# City Expansion Playbook

## Overview

This playbook turns the Mumbai launch into a repeatable process for entering the next city. It applies the readiness scoring methodology defined in [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md) and is deliberately written to be *derived from what actually happens in Mumbai* rather than theorized in advance — sections marked with lessons-pending are placeholders until Mumbai launch data exists, consistent with this folder's stated purpose: capture the thinking now, act on it only once Mumbai validates demand.

## Why City Expansion Is Sequenced Where It Is

Business Goal #4 in the PRD is "Expand city by city" (§3), but explicitly *after* "Reach product-market fit" and "Build trust-first community" (§3) — expansion is not a parallel track to Mumbai launch, it is the next stage gated by Mumbai's success. This playbook exists so expansion doesn't stall on a blank page when that gate opens, without pulling engineering or growth attention away from Mumban launch execution today.

## Gating Criteria for Triggering Expansion

A city is not added to the roadmap until Mumbai clears defined thresholds on the north-star dashboard ([../22-analytics-experimentation/north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md)):

1. Completed Trusted Activities per WAU stable or growing for 3 consecutive months.
2. Female retention (30-day) at or above the platform's internally set safety-confidence bar — expansion into a new city with an unresolved safety/trust problem in the first city would compound risk, not diversify it.
3. Report rate per 1,000 completed activities within guardrail bounds with no unresolved systemic trust issue.
4. Local network density reached in at least 2-3 Mumbai neighborhoods/categories (evidence that the "local network density" principle, PRD-001 §6, actually works before assuming it transfers).

## City Selection Framework

Inherits the scoring dimensions from [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md); this playbook adds the operational lens on top:

- **Persona density** — concentration of the primary target persona (urban professionals, students, solo travellers, recently relocated people, PRD-001 §5) relative to city population; Bangalore and Pune score structurally similar to Mumbai on this dimension given comparable IT/startup professional density and high in-migration.
- **Safety baseline** — existing perception of after-dark safety for solo activity participation (directly tied to the PRD's explicit goal of enabling post-10pm activity participation, §3); this varies materially by city and shapes both trust-model calibration and marketing messaging.
- **Language fit** — cross-referenced with [multi-language-strategy.md](multi-language-strategy.md)'s phased rollout; a city requiring a language phase not yet shipped adds lead time to the launch plan.
- **Competitive landscape** — informed by [../02-market-research/competitor-teardown-reports.md](../02-market-research/competitor-teardown-reports.md), assessing whether existing meetup/social apps have already captured the local network-density advantage in that city.

## Repeatable Launch Sequence (derived from Mumbai)

1. **Pre-launch density seeding** — before public launch, recruit an initial cohort of high-trust hosts (skewing toward the categories that proved highest completion-rate in Mumbai — see [Mumbai learnings, below]) via targeted outreach rather than broad marketing spend, mirroring how Mumbai avoided a cold-start empty-activity-list problem.
2. **Verification and trust-score bootstrapping** — new-city users start at the same verification bar as Mumbai (phone, email, selfie mandatory; government ID and LinkedIn optional per PRD-001 §9) — trust standards are not relaxed to accelerate a new city's growth curve, consistent with Core Principle #3, Safety Over Growth.
3. **Meetup-point safety localization** — venue and public-space safety norms are re-validated per city per [cultural-localization-guidelines.md](cultural-localization-guidelines.md) and [../17-maps-location/meetup-point-safety-recommendations.md](../17-maps-location/meetup-point-safety-recommendations.md) before the app actively suggests venues in the new city.
4. **Local moderation staffing** — the moderation console ([../24-admin-panel/moderation-console-spec.md](../24-admin-panel/moderation-console-spec.md)) queue capacity scales with new-city report volume *before* public launch marketing begins, not reactively after.
5. **Growth loop activation** — referral and community-seeding tactics from [../32-growth-marketing/growth-strategy-loops.md](../32-growth-marketing/growth-strategy-loops.md) and [../32-growth-marketing/referral-program-design.md](../32-growth-marketing/referral-program-design.md), adapted using Mumbai's actual loop performance data rather than re-derived from scratch.
6. **North-star tracking from day one** — the new city is a `city` dimension in the existing dashboard ([../22-analytics-experimentation/north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md)) rather than a separate reporting effort, since the event taxonomy already carries a `city` property.

## Mumbai Learnings [TBD — populate post-launch]

This section is intentionally left as an open question until real Mumbai launch data exists: which activity categories converted best, which trust-verification friction points caused drop-off, which neighborhoods reached density fastest, and what the actual female-retention curve looked like. Populating this section is a prerequisite for this playbook being usable, not merely a nice-to-have — expansion decisions should not be made on Mumbai's *plan* but on Mumbai's *results*.

## Related Documents

- [../02-market-research/city-expansion-readiness-scoring.md](../02-market-research/city-expansion-readiness-scoring.md) — scoring methodology this playbook applies
- [multi-language-strategy.md](multi-language-strategy.md) — language readiness input to city selection
- [cultural-localization-guidelines.md](cultural-localization-guidelines.md) — venue/trust norm adaptation per city
- [../32-growth-marketing/launch-city-playbook-mumbai.md](../32-growth-marketing/launch-city-playbook-mumbai.md) — the Mumbai launch this playbook generalizes from
- [../22-analytics-experimentation/north-star-kpi-dashboard-spec.md](../22-analytics-experimentation/north-star-kpi-dashboard-spec.md) — gating metrics
