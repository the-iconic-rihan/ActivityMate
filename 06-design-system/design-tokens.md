---
title: "Design Tokens"
folder: 06-design-system
purpose: "Token spec"
pages_estimate: 4
prerequisites: "brand-visual-identity"
dependencies: "frontend-mobile"
priority: P1
audience: "UI, FE"
status: draft-v1
generated: 2026-07-15
---

# Design Tokens

## Overview

This is the single source of truth for every color, spacing, type, and radius value used in the Flutter client. Tokens are consumed by the Flutter `ThemeData`/`ThemeExtension` layer described in [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md); no screen should hardcode a hex value, a raw `EdgeInsets` number, or a `TextStyle` outside this token set. Hardcoded values are the exact failure mode this document exists to prevent — per [README.md](README.md), a token-based system is what stops trust/safety UI patterns from being reimplemented inconsistently screen by screen.

## Color Tokens

Base palette derived from [brand-visual-identity.md](brand-visual-identity.md). Each color has a numeric scale (50 = lightest tint, 900 = darkest shade); UI consumes the named role, not the raw scale step, wherever possible.

**Primary — Trust Teal**
| Token | Value | Usage |
|---|---|---|
| `color.primary.50` | `#E6F4F3` | subtle backgrounds, selected-state fills |
| `color.primary.100` | `#C0E3E1` | hover/pressed backgrounds |
| `color.primary.400` | `#2E9994` | secondary buttons, icons |
| `color.primary.600` | `#0E7C7B` | primary buttons, links, brand mark (base value) |
| `color.primary.800` | `#0A5250` | pressed/active state, dark-mode primary text on light fill |

**Secondary — Warm Amber**
| Token | Value | Usage |
|---|---|---|
| `color.secondary.400` | `#F0B268` | activity-category chips, highlights |
| `color.secondary.600` | `#E8973A` | Create Activity FAB, discovery CTAs |
| `color.secondary.800` | `#B96F1F` | pressed state |

**Neutral**
| Token | Value | Usage |
|---|---|---|
| `color.neutral.0` | `#FFFFFF` | light-theme surface |
| `color.neutral.100` | `#F4F5F6` | light-theme background |
| `color.neutral.300` | `#D6D9DC` | dividers, disabled fills |
| `color.neutral.600` | `#6B7278` | secondary text |
| `color.neutral.900` | `#1B1E20` | primary text (light theme), dark-theme surface base |

**Semantic — reserved, never repurposed for brand/decorative use (full rationale and states in [typography-color-system.md](typography-color-system.md))**
| Token | Value | Usage |
|---|---|---|
| `color.semantic.verified` | `#2E9E5B` | verified trust badge, success states |
| `color.semantic.pending` | `#E0A100` | pending verification, awaiting-check-in (amber, distinct from secondary.600) |
| `color.semantic.flagged` | `#C24A3F` | flagged/under-review account states |
| `color.semantic.alert` | `#D64545` | panic button and safety-critical alerts ONLY — see [../05-ux/trust-safety-ux-patterns.md](../05-ux/trust-safety-ux-patterns.md) |

## Spacing Tokens

4px base unit, geometric-ish progression tuned for thumb-friendly mobile density on the mid-range Android devices common in the target market (per [../09-frontend-mobile/performance-battery-budget.md](../09-frontend-mobile/performance-battery-budget.md)'s device baseline):

| Token | Value | Typical usage |
|---|---|---|
| `space.1` | 4px | icon-to-label gaps |
| `space.2` | 8px | tight component padding |
| `space.3` | 12px | default internal card padding |
| `space.4` | 16px | standard screen margin, default gap between elements |
| `space.6` | 24px | section spacing |
| `space.8` | 32px | major section breaks |
| `space.12` | 48px | screen-top safe-area buffer below status bar |

## Radius Tokens

| Token | Value | Usage |
|---|---|---|
| `radius.sm` | 4px | chips, tags |
| `radius.md` | 8px | buttons, input fields |
| `radius.lg` | 16px | cards (activity cards, the safety check-in widget) |
| `radius.full` | 999px | avatars, badges, the panic-button circular affordance |

## Type Tokens

Named type roles reference the full ramp in [typography-color-system.md](typography-color-system.md); this file only defines the token names and their mapping so Flutter `TextTheme` construction is unambiguous:

`type.display`, `type.h1`, `type.h2`, `type.h3`, `type.bodyLarge`, `type.body`, `type.bodySmall`, `type.caption`, `type.overline` — each resolves to a `{fontFamily, fontSize, fontWeight, lineHeight, letterSpacing}` tuple defined in full in [typography-color-system.md](typography-color-system.md).

## Elevation / Shadow Tokens

| Token | Value | Usage |
|---|---|---|
| `elevation.0` | none | flat surfaces |
| `elevation.1` | `0 1px 2px rgba(27,30,32,0.08)` | activity cards at rest |
| `elevation.2` | `0 4px 8px rgba(27,30,32,0.12)` | FAB, active bottom sheets |
| `elevation.3` | `0 8px 24px rgba(27,30,32,0.16)` | the panic-button confirmation sheet, modal dialogs |

## Governance

Tokens are versioned in the Flutter design-system package and changed only through a PR reviewed by Head of Design; a token rename or value change requires updating this document in the same PR — token drift between this document and the Flutter codebase is treated as a build-blocking documentation bug, not a low-priority cleanup. Dark-mode token overrides are specified separately in [dark-mode-theming.md](dark-mode-theming.md) rather than inline here, to keep the light-theme base values as the canonical reference.

## Related Documents

- [brand-visual-identity.md](brand-visual-identity.md)
- [typography-color-system.md](typography-color-system.md)
- [component-library-spec.md](component-library-spec.md)
- [dark-mode-theming.md](dark-mode-theming.md)
- [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md)
