---
title: "Brand Visual Identity"
folder: 06-design-system
purpose: "Visual identity"
pages_estimate: 5
prerequisites: "naming-brand-story"
dependencies: "branding-identity"
priority: P1
audience: "UI, MK"
status: draft-v1
generated: 2026-07-15
---

# Brand Visual Identity

## Overview

ActivityMate's visual identity has one job before any aesthetic consideration: it must not read as a dating app on first glance, in a screenshot, or in an app-store thumbnail. Every color, mark, and typographic choice below was filtered through that constraint first, then optimized for warmth and clarity second. This document defines the logo system and the color-meaning rationale; token-level implementation values live in [design-tokens.md](design-tokens.md).

## Context / Problem

The Indian social-app landscape (see [../02-market-research/competitor-teardown-reports.md](../02-market-research/competitor-teardown-reports.md)) has a near-universal visual convention: pink-to-red gradients, heart iconography, flame/spark motifs signaling romantic or hookup intent. This convention is so strong that any new social app defaulting to warm pink/red risks being visually mistaken for a dating app regardless of its actual positioning — a categorization error that would undermine the "not a dating app" claim from PRD-001 before a user reads a single word of copy. At the same time, the identity can't overcorrect into something cold or corporate — ActivityMate is still a social, warm, activity-driven product, not an enterprise SaaS tool or a banking app, and the identity needs to invite spontaneous participation, not just signal safety.

## Options Considered

**Warm pink/red/coral (the category default).** Rejected. Even executed tastefully, this palette carries too much learned association with romantic-intent apps in this specific market; it would work against the product's core positioning from day one.

**Cold blue/gray (enterprise/fintech convention).** Considered as the "obviously not a dating app" alternative, but rejected as the primary direction — it solves the mis-categorization risk by overcorrecting into feeling transactional and unexciting for a product whose job is to get people enthusiastic about doing things together on a Saturday.

**Teal/green family, warmed with an amber accent (the recommendation).** Teal reads as trustworthy and calm (associations with clarity, water, stability) without the coldness of pure blue, and is visually distant enough from the pink/red dating-app convention to avoid mis-categorization at a glance. A warm amber secondary accent is layered in specifically to carry the "spontaneous, energizing, let's go do something" emotional register that pure teal alone would under-deliver — this combination lets the brand be simultaneously "calm/trustworthy" (teal, dominant) and "inviting/energetic" (amber, accent only), matching the product's actual dual nature as both a safety-first and activity-first platform.

## Recommendation & Rationale

**Primary color: Trust Teal.** A deep, slightly desaturated teal (`#0E7C7B` — see [design-tokens.md](design-tokens.md) `color.primary.600` for the exact token) as the dominant brand color across the wordmark, primary buttons, and the verification-badge system's "verified" treatment (coordinated with, but visually distinct from, the semantic trust-status colors in [typography-color-system.md](typography-color-system.md), so brand color and status color never get confused). Teal was chosen over navy blue specifically because pure navy reads slightly more corporate/fintech, while teal retains warmth appropriate to a social product.

**Secondary/accent color: Warm Amber.** A warm, appetizing amber-orange (`#E8973A`) used sparingly for calls-to-action tied to activity creation and discovery ("Create Activity" FAB, category highlights) — never used in trust/safety UI, which is reserved exclusively for teal (positive) and the dedicated alert-red token (panic button only, per [../05-ux/trust-safety-ux-patterns.md](../05-ux/trust-safety-ux-patterns.md)). Keeping amber and alert-red visually and semantically separate prevents the accidental "everything urgent-colored" effect that would erode the panic button's distinctiveness.

**Logo system.** Wordmark-primary lockup: "ActivityMate" set in a rounded-geometric sans (see [typography-color-system.md](typography-color-system.md) for the typeface), paired with a simple abstract mark — two overlapping rounded forms suggesting "two paths meeting" (activity + companion), rendered in Trust Teal. The mark must work standalone at favicon/app-icon size (a single teal glyph on a light or dark rounded-square background per platform icon guidelines) and must never incorporate a heart, flame, or figure-silhouette shape — an explicit exclusion list to prevent future iteration drift back toward dating-app convention under time pressure.

**Logo don'ts:** no gradient treatments that shift toward pink/magenta at any stop; no heart-adjacent negative space; no tagline lockup implying romance ("find your match" is banned copy — see [../33-branding-identity/brand-voice-tone-guide.md](../33-branding-identity/brand-voice-tone-guide.md)).

## Color Meaning Summary

| Color | Role | Meaning carried |
|---|---|---|
| Trust Teal (primary) | Brand, primary actions, verified-adjacent UI | Calm, trustworthy, stable |
| Warm Amber (secondary) | Activity creation/discovery CTAs | Energetic, inviting, spontaneous |
| Neutral grays | Backgrounds, body text, structure | Unobtrusive, lets teal/amber carry meaning |
| Alert Red (reserved) | Panic button and safety-critical alerts only | Never used elsewhere, to preserve urgency signal |

Full numeric token values, tints, and shades are specified in [design-tokens.md](design-tokens.md); semantic trust-state colors (verified/pending/flagged) are specified separately in [typography-color-system.md](typography-color-system.md) and are deliberately distinct hues from this brand palette.

## Risks & Open Questions

- Teal-forward palettes are also common in fintech and health apps; ActivityMate's specific shade and its pairing with warm amber needs a differentiation check against the direct competitive set before final lock — tracked for [../33-branding-identity/naming-rebranding-evaluation.md](../33-branding-identity/naming-rebranding-evaluation.md).
- The logo mark's abstract "two paths meeting" concept has not yet been user-tested for legibility/meaning at small sizes (app icon, notification icon) — flagged for a design review pass before final asset production.
- Marketing asset guidelines ([../33-branding-identity/marketing-asset-guidelines.md](../33-branding-identity/marketing-asset-guidelines.md)) need to inherit these don'ts explicitly so paid social creative doesn't drift toward dating-app visual convention under performance-marketing pressure to "just use what converts."

## Related Documents

- [design-tokens.md](design-tokens.md)
- [typography-color-system.md](typography-color-system.md)
- [../33-branding-identity/brand-positioning-statement.md](../33-branding-identity/brand-positioning-statement.md)
- [../00-foundation/naming-brand-story.md](../00-foundation/naming-brand-story.md)
- [../05-ux/trust-safety-ux-patterns.md](../05-ux/trust-safety-ux-patterns.md)
