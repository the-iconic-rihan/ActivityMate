---
title: "Persona Definitions"
folder: 05-ux
purpose: "Design personas"
pages_estimate: 5
prerequisites: "survey-interview-findings"
dependencies: "none"
priority: P0
audience: "UXR, PM, MK"
status: draft-v1
generated: 2026-07-15
---

# Persona Definitions

## Overview

These three personas are the design and product team's shared reference for every flow, copy decision, and prioritization call. They are synthesized from PRD-001's target user section (§5), [../02-market-research/persona-research-raw-data.md](../02-market-research/persona-research-raw-data.md), and [../03-user-research/survey-interview-findings.md](../03-user-research/survey-interview-findings.md), and correspond to PRD-002 in the root PRD's roadmap. Every feature decision in [../05-ux/user-flows-core-journeys.md](user-flows-core-journeys.md) and every PRD written from [../04-product/prd-template.md](../04-product/prd-template.md) should be checked against at least these three.

## Persona 1: The Relocated Professional

**"Ananya, 27, product manager, moved to Mumbai (Powai) six months ago for a new job."**

- **Situation:** New employer, no pre-existing local social circle. Colleagues are friendly but not yet close friends; weekends are the loneliest part of the week.
- **Goals:** Build a local social circle quickly, without the specific romantic-intent overhead of a dating app. Wants activities that double as low-stakes friend-making — coworking sessions, weekend treks, dinner groups.
- **Trust behavior:** High initial caution — she's meeting total strangers with no mutual-friend backstop. She checks verification badges and trust score before every join decision and disproportionately values the "verified hosts only" filter.
- **Frustrations with alternatives:** WhatsApp groups from expat/relocation forums are noisy and low-accountability; Meetup events feel large and impersonal; dating apps repurposed for "just friends" attract mismatched intent from other users.
- **Product implication:** Her onboarding must get her from install to a real activity fast — she is a high-intent, time-boxed user whose evaluation window for the app is short. Selfie verification completion is disproportionately important for her because it is the single strongest lever on her willingness to join a stranger's activity.

## Persona 2: The Solo Explorer

**"Rohan, 24, backpacking through Mumbai for two weeks before continuing to Goa."**

- **Situation:** Short time horizon in the city, wants authentic local experiences, not tourist-trap group tours. May be traveling entirely alone.
- **Goals:** Find spontaneous, same-day or next-day activities (street food walks, local markets, photography walks) with people who know the city. Values safety information more than any other persona given his complete lack of local context or emergency contacts physically nearby.
- **Trust behavior:** Extremely reliant on structural safety features rather than personal judgment, since he has no ability to independently vet a host's reputation through any local network. The safety check-in and panic button (see [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md)) are not optional nice-to-haves for him — they are the reason he'd choose ActivityMate over an anonymous Instagram-organized meetup.
- **Frustrations with alternatives:** Reddit/Instagram-organized meetups have zero accountability; travel agency tours are expensive, scripted, and not "real" enough.
- **Product implication:** Discovery needs to work well for very short lead times (same-day activity creation and joining), and his emergency-contact setup (Flow 1, step 6 of [user-flows-core-journeys.md](user-flows-core-journeys.md)) may need an international-number-friendly fallback since his emergency contact may not be in India.

## Persona 3: The Weekend Hobbyist

**"Priya, 33, works from home as a freelance designer, has lived in Mumbai (Andheri) for eight years."**

- **Situation:** Established local base and existing friend group, but her existing friends don't share her specific hobbies (badminton, board games) or aren't available on her schedule. Not lonely in the general sense — under-served in a specific interest.
- **Goals:** Find recurring, reliable activity partners for a specific hobby, ideally converging into a stable weekly group rather than one-off meetups with new strangers each time.
- **Trust behavior:** Moderate initial caution on first joins, but her real interest is in the Communities layer — she wants the app to help a good one-off activity turn into a standing Tuesday-badminton group. She is the persona most likely to become a repeat host once she's found her group.
- **Frustrations with alternatives:** Existing WhatsApp badminton groups are hard to discover if you're not already in someone's contacts; organizing recurring meetups over unstructured chat is logistically painful (no shared RSVP, no waitlist).
- **Product implication:** She is the primary validation case for the organic-community-formation flow described in [../04-product/product-strategy-doc.md](../04-product/product-strategy-doc.md) and for RSVP/waitlist mechanics in [../15-activities-communities/rsvp-capacity-waitlist-logic.md](../15-activities-communities/rsvp-capacity-waitlist-logic.md).

## Cross-Persona Design Implications

- All three personas share a low tolerance for friction in the *trust-signal-checking* step but a high tolerance for friction in the *safety-setup* step (Flow 1) — trust verification should feel fast to check, not fast to skip.
- Ananya and Priya are Android-majority, mid-range-device users typical of the Mumbai urban professional segment — performance and battery budget (see [../09-frontend-mobile/performance-battery-budget.md](../09-frontend-mobile/performance-battery-budget.md)) matters more than for a flagship-device-assuming design.
- Rohan is the strongest argument for treating the safety check-in and panic button as default-on, high-visibility UI rather than an opt-in feature buried in settings — see [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md).
- None of the three personas represent a user seeking romantic connection; copy, iconography, and matching logic must consistently avoid dating-app conventions (no "match" language implying romantic pairing, no swipe-based binary accept/reject of individual people) to stay legible to all three as "not a dating app."

## What These Personas Are Not

These are not the full spread of ActivityMate's eventual user base — organizers and community leads (PRD-001's "Secondary" target users) are covered separately as an operational role rather than a design persona, since their needs are administrative (managing a community, moderating a recurring group) rather than journey-based. A fourth persona may be added post-launch once real usage data distinguishes organizer behavior clearly enough to warrant its own design consideration — tracked as an open item for the next revision of this document.

## Related Documents

- [information-architecture.md](information-architecture.md)
- [user-flows-core-journeys.md](user-flows-core-journeys.md)
- [journey-maps.md](journey-maps.md)
- [../02-market-research/persona-research-raw-data.md](../02-market-research/persona-research-raw-data.md)
- [../03-user-research/survey-interview-findings.md](../03-user-research/survey-interview-findings.md)
