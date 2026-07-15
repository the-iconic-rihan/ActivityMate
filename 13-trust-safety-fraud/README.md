# 13-trust-safety-fraud/

**Phase:** Phase 4 — Trust, Safety & Core Product  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** Head of Trust & Safety + Engineering  
**Depends on:** ux, database, auth  
**Document count:** 8

## Purpose

Automated systems that compute trust, detect fraud, and keep offline meetups safe — the platform's core moat. Corresponds to PRD-004.

## Why this folder exists

Splitting this from moderation-content-ops is deliberate: this folder is the always-on automated engine (trust score, fraud detection, safety features), owned by engineering; moderation-content-ops is the human policy and workflow side, owned by Trust & Safety operations. Conflating an automated scoring system with a human review queue mixes an engineering roadmap with a policy-governance process and blurs accountability when something goes wrong.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [trust-engine-architecture.md](trust-engine-architecture.md) | Trust score model | Combines verification tier, behavioral signals (no-show rate, report rate), and community feedback (post-meetup ratings) into a single weighted score — weighted rather than binary, since a binary trusted/not-trusted split is too blunt for the many legitimate edge cases (e.g. a first-timer with no history yet). | 8 | identity-verification-kyc | database | P0 | BE, SEC, PM, F |
| [safety-engine-spec.md](safety-engine-spec.md) | Pre-meetup safety features | Public-venue-first suggestions, live check-in/check-out with optional emergency-contact sharing, and an in-app panic button wired to support escalation. Explicitly covers the night-activity risk scenario named in PRD-001 §3 (avoiding solo late-night meetups). | 7 | trust-safety-ux-patterns | maps-location | P0 | BE, PM, UXR |
| [fraud-detection-system.md](fraud-detection-system.md) | Fraud detection approach | Compares rules-based detection (fast to ship, explainable, but brittle against adaptive bad actors) against ML-based anomaly detection (adapts over time, but needs a volume of labeled data the platform won't have at launch). Recommends rules-based detection first — device fingerprinting, velocity checks, duplicate-selfie detection — layering in ML once enough labeled fraud data accumulates. | 8 | authentication-architecture | none | P0 | BE, SEC |
| [user-verification-levels.md](user-verification-levels.md) | Verification ladder | Defines what UI treatment and trust-score weight each verification tier unlocks, referenced directly by identity-verification-kyc. | 3 | identity-verification-kyc | none | P0 | BE, PM |
| [reporting-blocking-system.md](reporting-blocking-system.md) | Report & block mechanics | In-app report and block flows, with report categories split so an immediate-safety report routes to on-call while a policy-violation report routes to the moderation queue. | 5 | trust-engine-architecture | moderation-content-ops | P0 | BE, PM |
| [background-check-partnership-evaluation.md](background-check-partnership-evaluation.md) | Background-check feasibility | Evaluates third-party background-check partnerships in India, which are legally and data-availability constrained compared to the US equivalent Uber relies on for driver verification. Scoped as a P2 exploration, not a launch dependency. | 4 | identity-verification-kyc | privacy-compliance-legal | P2 | F, LG, SEC |
| [incident-response-playbook-trust.md](incident-response-playbook-trust.md) | Safety incident response | What happens when a real-world safety incident is reported: escalation chain, evidence preservation, law-enforcement liaison, and user communication. | 6 | reporting-blocking-system | incident-disaster-recovery | P0 | F, SEC, LG |
| [trust-score-transparency-policy.md](trust-score-transparency-policy.md) | Scoring transparency | Compares full transparency (Airbnb-style — builds trust, but invites gaming) against full opacity (Uber-rating-style — resists gaming, but feels arbitrary to users). Recommends partial transparency: show the score tier and the levers that improve it, hide the exact internal weighting. | 4 | trust-engine-architecture | none | P1 | PM, F, UI |
