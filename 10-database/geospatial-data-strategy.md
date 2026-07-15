---
title: "Geospatial Data Strategy"
folder: 10-database
purpose: "Geo storage"
pages_estimate: 4
prerequisites: "database-selection-rationale"
dependencies: "maps-location"
priority: P1
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Geospatial Data Strategy

## Overview

ActivityMate's core discovery loop — "show me activities happening near me" — is a geospatial query run at high frequency against a live, growing set of activity rows. This document specifies how PostGIS is used to serve that query efficiently, and draws a hard line between storage-layer geo precision (exact) and application-layer geo *disclosure* (fuzzed), which is where the safety-relevant decisions actually live.

## Context / Problem

Proximity search is a solved problem at the database layer — PostGIS is mature, well-indexed, and already part of the chosen Postgres deployment (see [database-selection-rationale.md](database-selection-rationale.md)). The harder problem is not "how do we query nearby activities fast," it's "how do we do that without ever letting one user pinpoint another user's or a solo host's exact home or live location through repeated queries." That problem is a safety problem, not a database problem, and this document explains why the fix belongs in the application layer rather than the storage layer.

## Storage Model

`activities.location` is a PostGIS `GEOGRAPHY(POINT, 4326)` column — geography rather than geometry, because geography type does its distance math on a spheroid (accounting for earth curvature) which matters at Mumbai-metro-area scale where users compare distances across double-digit kilometers, whereas the simpler planar `geometry` type would introduce small but compounding distortion. SRID 4326 (WGS 84) is used because it is what every consumer-facing mapping provider (Google Maps, used per [../17-maps-location/maps-provider-evaluation.md](../17-maps-location/maps-provider-evaluation.md)) natively returns from geocoding.

Full-precision coordinates are stored exactly as captured (from the host's device GPS or manual pin-drop at activity-creation time) — the database always holds the true point. Fuzzing is never applied before storage, because the platform still needs exact coordinates for legitimate internal purposes: distance-sorted search ranking, city/geofence boundary checks, and — critically — providing exact meeting-point detail to *accepted* participants only, once trust has been established for that specific activity.

## Query Patterns

The primary query is a k-nearest / radius search: "find open activities within N km of point P, filtered by category and trust-score gate." This is served with a GIST index on the `location` column and PostGIS's `ST_DWithin` function, which is index-accelerated (unlike computing `ST_Distance` for every row and filtering afterward). A composite approach — GIST index on `location` combined with a partial btree index on `status = 'open'` from [data-modeling-standards.md](data-modeling-standards.md) — keeps the common case ("open activities near me") fast as activity volume grows city by city.

City-level filtering (Mumbai only at launch) is enforced both as an explicit `home_city`/`city` column filter and as a geofence boundary check against the launch-city polygon defined in [../17-maps-location/geofencing-city-launch-logic.md](../17-maps-location/geofencing-city-launch-logic.md), so a spoofed or drifting GPS reading near the city edge cannot pull in activities from a neighboring, not-yet-launched market by radius search alone.

## Location Fuzzing: An Application-Layer Concern

This is the central architectural decision of this document: **the storage layer holds exact coordinates; the application layer decides how much of that precision any given API response reveals, to whom, and when.** PostGIS has no native concept of "reveal less precision to this caller" — building that into the storage layer would mean either maintaining two columns (exact and fuzzed, doubling write complexity and creating a drift risk) or trusting every future query author to remember to fuzz manually. Neither is acceptable for a safety-critical property.

Instead, a single application-layer read path (a `LocationDisclosurePolicy` service used by every endpoint that returns an activity's location) enforces:

- **Discovery / browse views (unauthenticated relationship to the activity):** location is truncated to a coarse area label and a randomized point within roughly 300–500m of the true point — enough to judge "is this near me" without pinpointing a host's actual meeting spot or, transitively, their likely home/workplace.
- **Requested-to-join, not yet accepted:** same fuzzed precision as browse — a join request does not itself earn location precision.
- **Accepted participant:** full precision released, since the participant is about to physically attend and needs the real meeting point; this reveal is logged as a trust-relevant event.
- **After activity completion or cancellation:** exact location remains visible only to users who were accepted participants, for a bounded historical window relevant to dispute/report investigation; it is never re-fuzzed retroactively for participants who already had it, but it is never granted to non-participants after the fact either.

The specific fuzzing radius, the exemptions (e.g. a public venue like a well-known café may not need fuzzing at all, since the venue's public address is already public information), and the interaction with reports/investigations are owned by [../17-maps-location/location-privacy-strategy.md](../17-maps-location/location-privacy-strategy.md) — this document defines that the enforcement point is the application layer reading from exact PostGIS storage, not the reverse.

## Related Documents

- [database-selection-rationale.md](database-selection-rationale.md)
- [schema-design-erd.md](schema-design-erd.md)
- [../17-maps-location/location-privacy-strategy.md](../17-maps-location/location-privacy-strategy.md)
- [../17-maps-location/geofencing-city-launch-logic.md](../17-maps-location/geofencing-city-launch-logic.md)
- [../17-maps-location/meetup-point-safety-recommendations.md](../17-maps-location/meetup-point-safety-recommendations.md)
- [../17-maps-location/maps-provider-evaluation.md](../17-maps-location/maps-provider-evaluation.md)
