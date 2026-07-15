---
title: "Trust Score Transparency Policy"
folder: 13-trust-safety-fraud
purpose: "Scoring transparency"
pages_estimate: 4
prerequisites: "trust-engine-architecture"
dependencies: "none"
priority: P1
audience: "PM, F, UI"
status: draft-v1
generated: 2026-07-15
---

# Trust Score Transparency Policy

## 1. Overview

Once [trust-engine-architecture.md](trust-engine-architecture.md) defines *how* the score is computed, a separate question follows: how much of that should the user ever see? This policy sets the disclosure boundary — what's shown, what's hidden, and why — for the number that gates whether someone can host a dinner or join a night hike.

## 2. Context / Problem

Two established consumer patterns bound this decision. Airbnb-style **full transparency** — showing exact review counts, response rates, and detailed breakdowns — builds user trust in the system itself, because nothing feels hidden. But it also hands bad actors a precise map of exactly which levers to farm (e.g. "post 20 reviews to cross the next threshold"). Uber-style **full opacity** — a single star rating with no visible mechanics — resists this kind of gaming, since users can't reverse-engineer the formula, but it feels arbitrary and erodes trust in the platform when a score moves and nobody understands why, which is corrosive for a platform whose entire premise is "we can be trusted to keep you safe."

## 3. Options Considered

| Option | Builds user trust in the system | Resists gaming | User confusion risk |
|---|---|---|---|
| Full transparency (exact formula, weights, raw score) | High | Low — directly exploitable | Low |
| Full opacity (single star or hidden score) | Low | High | High — "why did my score drop?" support burden |
| Partial transparency (tier + levers, hidden weights) — **recommended** | Medium-high | Medium-high | Low-medium |

## 4. Recommendation & Rationale

**Partial transparency.** The user sees:

- Their current **tier** (New Member / Basic / Verified / Trusted) — never the raw 0–100 number.
- The **list of levers** that improve standing: complete verification steps, maintain a low no-show rate, respond promptly to messages, earn positive post-meetup feedback — described qualitatively ("respond to join requests quickly"), not as exact point values.
- A **progress indicator** toward the next tier (e.g. "2 more completed activities to reach Trusted"), calculated from real thresholds but rounded/abstracted so the exact formula in [trust-engine-architecture.md](trust-engine-architecture.md) §3 stays internal.

What stays hidden: the precise per-signal weights, the exact behavioral-score formula, and any raw fraud-signal data. This mirrors the reasoning in [reporting-blocking-system.md](reporting-blocking-system.md) around not revealing reporter identity — some information is withheld specifically *because* revealing it enables gaming or retaliation, not out of general secrecy.

This resolves the tension directly: users get enough visibility to understand *what behavior earns trust* (satisfying the "why did this happen to me" need that drives opacity's support burden) without enough precision to reverse-engineer a min-max strategy (avoiding transparency's gaming exposure).

## 5. Enforcement

- Product/design must not add any UI surface that exposes the raw numeric score or component weights, including in debug/admin-adjacent user-facing screens.
- Internal admin tooling ([../24-admin-panel/moderation-console-spec.md](../24-admin-panel/moderation-console-spec.md)) is exempt — Trust & Safety staff need the full breakdown to investigate cases, but this data does not flow to any user-facing API response.
- Support scripts for "why is my tier X" inquiries are limited to the same lever list shown in-app — support agents do not have discretion to reveal more, keeping the policy consistent regardless of channel.
- Any future change to what's disclosed (e.g. showing more detail to Tier 3 hosts as a trust-building experiment) requires sign-off from Trust & Safety leadership and a documented update to this policy, not an ad hoc product decision.

## 6. Related Documents

- [trust-engine-architecture.md](trust-engine-architecture.md) — the underlying formula this policy governs disclosure of
- [user-verification-levels.md](user-verification-levels.md) — the tier labels shown to users
- [reporting-blocking-system.md](reporting-blocking-system.md) — related precedent on selective information withholding
