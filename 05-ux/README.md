# 05-ux/

**Phase:** Phase 2 — Product & Design  
**Priority:** P0 (P0 — blocks build start)  
**Owner:** Head of Design (UX)  
**Depends on:** user-research, product  
**Document count:** 6

## Purpose

Information architecture, core journeys, and — critically — the UX patterns that make trust and safety legible to the user in the interface itself.

## Why this folder exists

Trust that isn't visible in the UI doesn't function as a differentiator. A verification system with no on-screen signal is wasted engineering. This folder is where the trust and safety engine becomes something a user can actually see and act on.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [information-architecture.md](information-architecture.md) | App structure | Top-level navigation: Discover, My Activities, Communities, Chat, Profile/Trust — trust surfaced as a first-class tab rather than buried in settings, a deliberate departure from how dating apps typically hide their safety tools. | 4 | persona-research-raw-data | none | P0 | UXR, UI, PM |
| [user-flows-core-journeys.md](user-flows-core-journeys.md) | Core flows | Onboarding and verification, create activity, discover and join, pre-meetup safety check, post-activity feedback and rating. | 10 | information-architecture | trust-safety-fraud, activities-communities | P0 | UXR, UI, FE |
| [persona-definitions.md](persona-definitions.md) | Design personas | 3–4 primary personas synthesized from market and user research (Relocated Professional, Solo Explorer, Weekend Hobbyist). Corresponds to PRD-002 in the root PRD's roadmap. | 5 | survey-interview-findings | none | P0 | UXR, PM, MK |
| [journey-maps.md](journey-maps.md) | Emotional journey | Maps the anxiety-to-trust curve across the first-meetup journey specifically, since that is the highest-drop-off, highest-stakes moment in the product. Corresponds to PRD-003. | 6 | user-flows-core-journeys | user-research | P1 | UXR, PM |
| [accessibility-ux-standards.md](accessibility-ux-standards.md) | Accessibility baseline | WCAG 2.2 AA as the floor; screen-reader flows for safety-critical actions (panic button, check-in) get AAA-level treatment given the stakes involved. | 4 | none | design-system | P1 | UI, FE, QA |
| [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md) | Trust UI patterns | The verification badge system, the safety check-in widget, panic-button placement, and public-venue-first defaults in activity creation — the visual language of the moat. | 8 | user-flows-core-journeys, trust-perception-research | trust-safety-fraud | P0 | UI, UXR, PM, F |
