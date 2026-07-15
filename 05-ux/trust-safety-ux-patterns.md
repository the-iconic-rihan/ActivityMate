---
title: "Trust Safety Ux Patterns"
folder: 05-ux
purpose: "Trust UI patterns"
pages_estimate: 8
prerequisites: "user-flows-core-journeys, trust-perception-research"
dependencies: "trust-safety-fraud"
priority: P0
audience: "UI, UXR, PM, F"
status: draft-v1
generated: 2026-07-15
---

# Trust & Safety UX Patterns

## Overview

Trust that isn't visible in the interface doesn't function as a differentiator — this is the founding premise of this entire folder, stated in [README.md](README.md). This document is where the trust engine ([../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)) and safety engine ([../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md)) become the visual language a user actually sees and acts on: the verification badge system, the safety check-in widget, panic-button placement, and public-venue-first defaults. This is the single highest-priority UX document in the repo after the core flows, because it is where trust as a business strategy either becomes real or stays a slide in a pitch deck.

## Actors / Personas Involved

All three personas from [persona-definitions.md](persona-definitions.md) interact with these patterns, but with different weight: Rohan (Solo Explorer) relies on them most heavily as his primary trust mechanism; Ananya (Relocated Professional) checks them actively before every join decision; Priya (Weekend Hobbyist) engages with them least once she's in an established community, but still expects them present for any new-to-her participant.

## Design Principle for This System

Every pattern below follows one rule: **legible without being alarming.** A trust/safety UI that looks like a security warning constantly reminds the user they are at risk, which is itself a bad user experience and, per [product-strategy-doc.md](../04-product/product-strategy-doc.md), undermines the "not a dating app, not a risky app, a trustworthy app" positioning. The visual tone throughout is calm, confident, and competent — closer to a well-designed banking app's security indicators than to a red-alert warning system.

## Pattern 1: The Verification Badge System

**States:** Unverified (no badge shown — absence is itself informative, not styled as a negative icon), Phone-Verified (base badge, small check-in-circle icon, neutral gray-blue), Selfie-Verified (upgraded badge, same icon family with a filled variant, primary brand color per [../06-design-system/brand-visual-identity.md](../06-design-system/brand-visual-identity.md)), ID-Verified (a distinct "shield" icon, since government-ID verification is a categorically stronger signal than selfie matching), LinkedIn-Verified (a small secondary badge, professional-context signal, shown only in combination with at least Phone-Verified).

**Composition rule:** badges display as a compact horizontal cluster next to the user's name everywhere a name appears (Discover cards, activity detail participant list, chat header, Profile/Trust tab) — never as a separate screen the user has to navigate to. Maximum three badges shown inline; additional tiers collapse into a "+2 more" tap target that expands to the full verification profile.

**Trust-score band, separate from badges:** the numeric trust score itself is never shown as a raw number to other users (only to the account holder, on their own Profile/Trust tab) — other users see a qualitative band (e.g., "Highly Trusted," "Trusted," "New to ActivityMate") derived from the score, per [../13-trust-safety-fraud/trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md). This avoids the badge system collapsing into a single competitive number users try to game, while still surfacing enough signal to inform a join decision.

**New-host treatment:** an account below a completed-activity threshold gets an explicit "New Host" tag rather than simply showing fewer badges — naming the situation honestly (per [journey-maps.md](journey-maps.md) Stage 2) rather than letting the user infer risk from an absence, which reads as concealment.

## Pattern 2: The Safety Check-In Widget

**Placement:** persistent card at the top of the activity detail screen, active only during the live window (T-minus 30 minutes through estimated activity end — see [user-flows-core-journeys.md](user-flows-core-journeys.md) Flow 4). Outside that window, the card does not render at all — it should never feel like a constant background presence nagging the user before it's relevant.

**Visual states:**
- **Pending** (before T-minus 30 min in the live window opens): not shown.
- **Awaiting check-in** (T-minus 30 min to arrival): a calm, single-action card — "Let us know you've arrived safely" with one large primary-color tap target, plus a smaller secondary link "Who sees this?" that explains the emergency-contact-sharing mechanic inline, since transparency about what a safety feature actually does is itself trust-building.
- **Checked in** (post-tap): collapses to a minimized, dismissible confirmation strip — "Checked in at 7:42 PM ✓" — deliberately unobtrusive once its job is done.
- **Overdue** (no check-in past 20 minutes post-start): the card re-expands with a slightly more prominent but still non-alarming treatment — amber accent (never red — red is reserved exclusively for the panic-button system, to keep color semantics distinct per [../06-design-system/typography-color-system.md](../06-design-system/typography-color-system.md)) and a direct "Are you okay? Tap to check in" prompt.

## Pattern 3: Panic-Button Placement

This is the single most consequential UI decision in the trust/safety system, and it is deliberately over-specified here to avoid ambiguity reaching implementation.

**Placement:** a persistent, fixed-position affordance — not buried in a menu, not requiring a swipe or long-press to reveal — visible on the activity detail screen and the activity's chat screen for the entire live activity window. It does not appear on Discover, My Activities (outside an active window), Communities, or unrelated Chat threads — global omnipresence would dilute its meaning and normalize it as background UI rather than an emergency-specific control.

**Visual treatment:** a small, fixed circular button, bottom-corner position (thumb-reachable, one-handed), using the platform's reserved alert-red token (see [../06-design-system/typography-color-system.md](../06-design-system/typography-color-system.md)) but sized modestly — large enough to find instantly under stress, not so large that it dominates the screen and reads as alarmist during ordinary use. Label on long-press/tooltip: "Emergency" — the icon itself uses a universally recognizable alert glyph, not a novel icon requiring learned association.

**Interaction:** single tap opens a full-screen, high-contrast confirmation sheet within 300ms — no nested menus, no multi-step confirmation that could cost critical seconds — offering three large, clearly labeled actions: "Call emergency services," "Alert my emergency contact + ActivityMate Trust & Safety," and "I'm safe — false alarm" (this cancel path is deliberately equally prominent, not a small dismissible link, since accidental taps must be easy to correct without embarrassment or friction that would discourage genuine use next time).

**Post-trigger state:** once triggered (excluding false alarm), the app maintains a visible "Help is on the way" persistent status bar until the user or Trust & Safety team resolves the incident, so the user never has to wonder whether their trigger registered.

## Pattern 4: Public-Venue-First Defaults

Covered operationally in [user-flows-core-journeys.md](user-flows-core-journeys.md) Flow 2; the UX pattern specifics are:

- The venue picker in activity creation opens to a curated, verified map layer of public venues (cafés, parks, sports complexes, coworking spaces) as the default and visually primary option, sourced from [../17-maps-location/README.md](../17-maps-location/README.md).
- A private-address option exists (folded under "Enter a different address") but triggers a single, non-blocking advisory microcopy: "Public venues are recommended, especially for first-time meetups" — informational, not a hard gate, respecting host autonomy while still nudging the safer default.
- On the joiner side, activity cards visually distinguish public-venue activities (a small map-pin-in-building icon) from private-address activities (a home icon) at a glance in the Discover feed and detail screen, so a joiner's trust calculus can factor venue type without having to read the full description.

## Pattern 5: Trust Signals in the Report/Block Flow

- Reporting is accessible from every surface where a user's name/photo appears (chat, activity detail, Profile/Trust) via a consistent overflow-menu affordance — not hidden differently on different screens, which would make it harder to find under stress.
- The report flow itself uses a structured category list (matching [../13-trust-safety-fraud/reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md)'s taxonomy) rather than a blank free-text box as the first step, since a structured list is faster to complete and produces triageable data for moderation ops.
- Blocking is a separate, always-available action independent of reporting — a user should never have to file a formal report just to stop seeing someone.

## Edge Cases

- **Low/no connectivity at the panic-button moment:** the confirmation sheet's "Alert my emergency contact" action attempts an SMS fallback (via the backend's telephony integration) if the data connection fails, since this is the one interaction in the entire product where a spinner-and-fail state is unacceptable.
- **A verified badge later revoked** (e.g., a government ID later found fraudulent): the badge is removed retroactively and the account's trust band drops; any activities the user is currently part of are flagged for the affected participants without unilaterally cancelling the activity, consistent with the "manual review, not auto-removal" edge case in [user-flows-core-journeys.md](user-flows-core-journeys.md).
- **A user without emergency-contact setup triggers the panic button:** the "Alert my emergency contact" option is replaced with "Alert ActivityMate Trust & Safety directly," so the absence of a configured contact never leaves the user with a dead-end option in a real emergency.
- **False-alarm pattern abuse:** repeated false-alarm triggers from the same account are logged and reviewed by Trust & Safety per [../13-trust-safety-fraud/fraud-detection-system.md](../13-trust-safety-fraud/fraud-detection-system.md), but the UI itself never rate-limits or discourages triggering the button — the cost of one abused trigger is always lower than the cost of a hesitant user in a real emergency.

## Related Documents

- [user-flows-core-journeys.md](user-flows-core-journeys.md)
- [journey-maps.md](journey-maps.md)
- [accessibility-ux-standards.md](accessibility-ux-standards.md)
- [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md)
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md)
- [../06-design-system/typography-color-system.md](../06-design-system/typography-color-system.md)
- [../06-design-system/component-library-spec.md](../06-design-system/component-library-spec.md)
