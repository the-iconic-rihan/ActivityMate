---
title: "Geofencing City Launch Logic"
folder: 17-maps-location
purpose: "City launch gating"
pages_estimate: 2
prerequisites: "maps-provider-evaluation"
dependencies: "market-research"
priority: P1
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Geofencing City Launch Logic

## Overview

ActivityMate launches in one city — Mumbai — by design (PRD-001 Section 3: "Launch in Mumbai," "Expand city by city"). This document defines how a geographic boundary controls which users can see and create activities, so the product does not silently sprawl into cities with zero user density before density is deliberately built there.

## Context / Problem

Local network density is a named core product principle (PRD-001 Section 6). An activity platform with users scattered across five cities and a handful of people in each is worse than the same total users concentrated in one — nobody has anyone nearby to actually do the activity with. Left unconstrained, organic signups (a user downloading the app from outside Mumbai, a host on a work trip creating an activity in Bengaluru) would fragment density before there's any of it to fragment. Geofencing is the mechanism that keeps supply and demand concentrated inside the city currently being built, city by city, matching the expansion strategy.

## The Actual Policy

**Launch geofence definition:** a polygon (not a simple radius) covering Mumbai and its immediate commuter suburbs (the Mumbai Metropolitan Region catchment relevant to the target persona — urban professionals, students, commuting distances typical of the city), defined in PostGIS as a stored geography boundary, sourced from Google Maps Platform administrative boundary data (per maps-provider-evaluation.md) and refined against market-research's launch-city definition.

**Enforcement points:**
1. **Activity creation** — a host can only create an activity with a venue location that resolves inside the active geofence(s). An attempt to create an activity outside it is blocked client-side with a clear "not yet available in your area" message, not silently allowed and hidden.
2. **Discovery/search** — the discovery feed and search results (20-search-discovery) are scoped by default to activities whose venue falls inside the same geofence the requesting user is currently located in, preventing a Mumbai user from seeing a lone activity in a not-yet-launched city and vice versa.
3. **Signup gating (soft, not hard):** a user signing up from outside any active geofence is not blocked from creating an account (blocking signup entirely wastes future-city demand signal), but is shown a waitlist state instead of the full activity-creation flow, and is geographically tagged for prioritized outreach when their city's geofence activates.

**Multi-city readiness:** the geofence table is designed from day one as a list, not a single hardcoded polygon, so opening a second city is a data operation (add a polygon, activate it) rather than a code change — consistent with the "expand city by city" business goal and avoiding a rebuild at the second-city milestone.

**Boundary edge handling:** a user physically near the geofence edge (e.g., a commuter town just outside the defined boundary) is evaluated against their stated/fuzzed home location, not a single GPS ping, to avoid flapping in and out of the active state during ordinary movement or fuzzing jitter (per location-privacy-strategy.md).

## Enforcement

Geofence boundaries are owned jointly by BE and the city-launch decision-maker (market-research/business-strategy) — engineering does not unilaterally redraw a launch boundary; it implements boundaries decided elsewhere. Boundary changes are version-controlled and logged, since a boundary change silently changes who can see and create activities.

## Related Documents

- [maps-provider-evaluation.md](maps-provider-evaluation.md) — source of the administrative boundary data
- [location-privacy-strategy.md](location-privacy-strategy.md) — per-user fuzzing this boundary logic operates above
- [../02-market-research/](../02-market-research/README.md) — launch-city market sizing this geofence encodes
- [../41-localization-expansion/](../41-localization-expansion/README.md) — multi-city expansion this design anticipates
