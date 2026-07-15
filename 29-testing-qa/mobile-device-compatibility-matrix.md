---
title: "Mobile Device Compatibility Matrix"
folder: 29-testing-qa
purpose: "Device matrix"
pages_estimate: 2
prerequisites: "test-strategy"
dependencies: "frontend-mobile"
priority: P1
audience: "QA, FE"
status: draft-v1
generated: 2026-07-15
---

# Mobile Device & OS Compatibility Matrix

## Overview

ActivityMate ships as a Flutter app (per [PRD-001](../PRD-001_Master_Product_Requirements.md)) targeting a Mumbai launch market where the Android device landscape is dominated by budget and mid-range devices with wide OS fragmentation, unlike the iOS-heavy, few-SKU markets many device matrices are written for. This document sets the concrete tier list of devices and OS versions QA and Flutter engineering are accountable for.

## Context / Problem

A device matrix modeled on a US/EU market (a handful of recent iPhones, top 3 flagship Android phones) would leave ActivityMate blind to the devices most of its actual target users own. India's Android install base skews heavily toward budget devices (Xiaomi/Redmi, Samsung Galaxy M/A series, Realme, Vivo) with 3-6GB RAM, mid-tier SoCs, and a long tail of OS versions still in active use because manufacturers ship slower updates at this price tier. Testing only on high-end devices would systematically hide performance and layout bugs from the majority of the actual user base.

## Options Considered

1. **Test only on latest 2 OS versions, flagship devices.** Rejected — does not represent the target market; would ship a product that performs well in the office and badly on the device most users actually carry.
2. **Attempt exhaustive coverage of every Android OEM/version combination.** Rejected — combinatorially infeasible for a small QA function; diminishing returns past a representative tier set.
3. **Tiered device matrix, weighted toward budget/mid-range Android, with a minimum OS floor set by Flutter's own support window.** Selected.

## Recommendation & Rationale

**Tier 1 — must pass every release, physical device or equivalent farm device (e.g., BrowserStack/Firebase Test Lab), full manual + automated regression:**
- Redmi/Xiaomi mid-range (e.g., Redmi Note series class), Android 12-13, 4-6GB RAM
- Samsung Galaxy M/A series budget-mid tier, Android 13-14
- One low-RAM device (3-4GB) specifically for memory-pressure and background-kill behavior testing — relevant because the app needs to survive being backgrounded during an active in-person meetup without losing chat/check-in state

**Tier 2 — automated regression every release, manual spot-check monthly:**
- A recent flagship Android device (Samsung Galaxy S-series or Pixel current generation) — upper bound of the market, catches issues that only appear on high-refresh-rate displays or newer Android privacy permission models
- One iOS device (iPhone, last 2 major iOS versions) — smaller Mumbai share at launch but non-zero, and app-store review requires a functioning iOS build regardless of usage share

**Tier 3 — smoke test only, pre-major-release:**
- Oldest Android OS version still within Flutter's supported minimum SDK (set in [../09-frontend-mobile/mobile-architecture-overview.md](../09-frontend-mobile/mobile-architecture-overview.md)) — confirms the app installs and core flows (auth, browse, chat) function, not full regression

**Network condition testing.** Layered onto the device matrix rather than treated separately: Tier 1 devices are additionally tested on throttled 3G/unstable-network profiles (using Android's network simulation or Charles Proxy), because budget-device users in Mumbai are disproportionately likely to be on inconsistent mobile data rather than WiFi — this directly informs [../09-frontend-mobile/offline-first-sync-strategy.md](../09-frontend-mobile/offline-first-sync-strategy.md), particularly for chat and check-in reliability.

**Performance budget tie-in.** Tier 1 devices are the reference hardware for the performance and battery budgets defined in [../09-frontend-mobile/performance-battery-budget.md](../09-frontend-mobile/performance-battery-budget.md) — a performance target validated only on a flagship device is not a meaningful target for this product.

**Review cadence.** This matrix is reviewed quarterly against current Indian Android market-share data (StatCounter or similar) rather than fixed permanently, since device distribution shifts over the product's multi-year life and especially as the product expands beyond Mumbai per [../41-localization-expansion](../41-localization-expansion/README.md).

## Related Documents

- [test-strategy.md](test-strategy.md)
- [../09-frontend-mobile/performance-battery-budget.md](../09-frontend-mobile/performance-battery-budget.md)
- [../09-frontend-mobile/offline-first-sync-strategy.md](../09-frontend-mobile/offline-first-sync-strategy.md)
