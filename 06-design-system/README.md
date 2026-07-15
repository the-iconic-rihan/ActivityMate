# 06-design-system/

**Phase:** Phase 2 — Product & Design  
**Priority:** P1 (P1 — blocks public launch)  
**Owner:** Head of Design  
**Depends on:** ux  
**Document count:** 7

## Purpose

The reusable visual and component language every screen is built from.

## Why this folder exists

Without a system, trust/safety UI patterns get reimplemented inconsistently screen by screen — exactly where visual trust signals lose credibility. A token-based system also lets the Flutter client ship faster once the component library exists.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [brand-visual-identity.md](brand-visual-identity.md) | Visual identity | Logo system and color meaning — the primary hue is chosen to read as trustworthy and calm, explicitly avoiding the pink/red palette conventions of dating apps. | 5 | naming-brand-story | branding-identity | P1 | UI, MK |
| [design-tokens.md](design-tokens.md) | Token spec | Color, spacing, type, and radius tokens as the single source the Flutter client consumes, preventing hardcoded values from drifting away from design. | 4 | brand-visual-identity | frontend-mobile | P1 | UI, FE |
| [component-library-spec.md](component-library-spec.md) | Component inventory | Every reusable component (cards, badges, chips, the safety-check widget) with its states and variants. | 12 | design-tokens | ux | P1 | UI, FE |
| [typography-color-system.md](typography-color-system.md) | Type & color scale | Type ramp and color scale, including semantic colors for trust levels (verified, pending, flagged) kept visually distinct from the brand accent so status is never ambiguous. | 4 | design-tokens | none | P1 | UI, FE |
| [iconography-illustration-guidelines.md](iconography-illustration-guidelines.md) | Icon & illustration rules | Icon set sourcing and illustration style for empty states and onboarding. | 3 | brand-visual-identity | none | P2 | UI |
| [dark-mode-theming.md](dark-mode-theming.md) | Theming spec | Token-level dark mode strategy with contrast requirements maintained across both themes. | 3 | design-tokens | none | P2 | UI, FE |
| [motion-interaction-guidelines.md](motion-interaction-guidelines.md) | Motion rules | When animation is used versus avoided, and reduced-motion compliance. | 3 | component-library-spec | none | P2 | UI, FE |
