---
title: "Accessibility Ux Standards"
folder: 05-ux
purpose: "Accessibility baseline"
pages_estimate: 4
prerequisites: "none"
dependencies: "design-system"
priority: P1
audience: "UI, FE, QA"
status: draft-v1
generated: 2026-07-15
---

# Accessibility UX Standards

## Overview

Accessibility is treated as a floor, not a checklist to satisfy after design is done. This document sets that floor at WCAG 2.2 AA across the entire product, with one deliberate exception raised to AAA: any flow involved in physical safety. This is a strategy/decision document — it names the standard, why it was chosen over the alternatives, and where ActivityMate intentionally exceeds it.

## Context / Problem

Accessibility standards exist on a spectrum from WCAG 2.2 A (minimum, widely considered insufficient for a real product) through AA (the common legal and industry baseline) to AAA (rigorous, often impractical to apply uniformly because some AAA criteria conflict with reasonable visual design, e.g., very high contrast ratios that constrain color palette flexibility). A product could pick a single level and apply it uniformly, but ActivityMate has an unusual profile: most of the app is a conventional social/activity browsing experience where AA is the right cost/benefit tradeoff, but a small number of screens are safety-critical in a way that makes "conventional adequate" an unacceptable standard — a screen-reader user in a moment of genuine danger cannot be asked to tolerate AA-level friction.

## Options Considered

**WCAG 2.2 A everywhere.** Rejected outright — A is widely regarded as insufficient for a production consumer app and would leave core interactions (form labeling, focus order, color-only status indicators) below acceptable quality even for non-safety flows.

**WCAG 2.2 AA everywhere, uniformly.** The realistic industry-standard choice, and defensible on its own. Its weakness for ActivityMate specifically is that it treats a "did the user like the coffee meetup" rating screen and "does the user need to trigger the panic button in a moment of genuine risk" as requiring the same bar, when the cost of an accessibility failure in the second case is categorically higher than in the first.

**WCAG 2.2 AAA everywhere.** Rejected as impractical — AAA includes criteria (e.g., 7:1 contrast ratio, sign-language interpretation for prerecorded audio, extended context-sensitive help everywhere) that would meaningfully constrain visual design and slow delivery across screens where the marginal safety benefit doesn't justify the cost. Applying AAA uniformly would also dilute engineering and QA attention away from the screens that need it most.

**Tiered standard: AA floor, AAA for safety-critical flows (the recommendation).** Combines the practicality of AA as the default with a deliberate, named exception for the highest-stakes interactions. This directly mirrors [../04-product/product-principles.md](../04-product/product-principles.md) Principle 3 (Safety Over Growth) applied to accessibility specifically: the extra design and engineering cost of AAA is willingly paid exactly where the stakes are highest, and not spread thin everywhere else.

## Recommendation & Rationale

**WCAG 2.2 AA is the mandatory floor for every screen in the Flutter app.** This includes: minimum 4.5:1 contrast for normal text and 3:1 for large text/UI components (enforced against the palette in [../06-design-system/typography-color-system.md](../06-design-system/typography-color-system.md)), full keyboard/switch-access operability, correct semantic labeling for TalkBack (Android, the majority platform for the target Mumbai demographic) and VoiceOver (iOS), visible focus indicators, and no information conveyed by color alone — directly relevant to trust-status colors (verified/pending/flagged) which must always pair color with an icon or text label per [../06-design-system/typography-color-system.md](../06-design-system/typography-color-system.md).

**The following flows are held to AAA-level treatment, specifically the screen-reader and cognitive-load-relevant AAA success criteria (extended audio description equivalents are out of scope; the relevant AAA criteria here are enhanced contrast (7:1), no time-limited response requirement without extension, and context-sensitive help availability):**

1. **Panic button** — reachable and clearly announced by screen readers from any point in an active safety check-in window ([user-flows-core-journeys.md](user-flows-core-journeys.md) Flow 4), with an unambiguous, non-abbreviated screen-reader label ("Emergency: Alert my contact and Trust and Safety team now" — not just "Panic" or an icon with no label).
2. **Safety check-in widget** — the "I've arrived" action and its confirmation state must be announced clearly, with no reliance on visual-only checkmark animation to communicate success.
3. **Report/block flow** — a user reporting a safety concern must be able to complete the full flow via screen reader or switch access with zero ambiguity about what was submitted, since this flow's entire purpose is high-stakes by definition.
4. **Emergency contact setup (onboarding Flow 1, step 6)** — must be fully operable non-visually, since a user who cannot complete this setup is a user the no-check-in escalation (Flow 4) cannot protect.

## Testing & Enforcement

- Every PR touching a safety-critical screen requires a screen-reader pass (TalkBack minimum, VoiceOver where feasible) as part of QA sign-off, per [../29-testing-qa/README.md](../29-testing-qa/README.md) — not just an automated contrast-checker pass.
- Automated accessibility linting (contrast, semantic labels, tap-target sizing — minimum 48x48dp per Material guidelines, which also satisfies WCAG 2.2's target-size criterion) runs in CI per [../27-cicd-release/README.md](../27-cicd-release/README.md).
- Design reviews in [../06-design-system/component-library-spec.md](../06-design-system/component-library-spec.md) must document the accessible state (focus, label, contrast) for every component variant, not just the default visual state.

## Risks & Open Questions

- Reduced-motion compliance (an AA criterion) interacts with the motion guidelines in [../06-design-system/motion-interaction-guidelines.md](../06-design-system/motion-interaction-guidelines.md); the two documents must stay in sync as motion patterns are finalized.
- No accessibility audit budget has yet been allocated for a third-party WCAG conformance review pre-launch — an open question for [../37-engineering-standards/README.md](../37-engineering-standards/README.md) to resolve given P1 (blocks public launch, not build start) priority.
- Hindi/Marathi localization (tracked in [../41-localization-expansion/README.md](../41-localization-expansion/README.md)) will need its own screen-reader language-pack validation once in scope — out of scope for this MVP-stage document.

## Related Documents

- [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md)
- [user-flows-core-journeys.md](user-flows-core-journeys.md)
- [../06-design-system/typography-color-system.md](../06-design-system/typography-color-system.md)
- [../06-design-system/component-library-spec.md](../06-design-system/component-library-spec.md)
- [../29-testing-qa/README.md](../29-testing-qa/README.md)
