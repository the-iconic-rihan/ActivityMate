---
title: "Maps Provider Evaluation"
folder: 17-maps-location
purpose: "Provider choice"
pages_estimate: 4
prerequisites: "architecture"
dependencies: "none"
priority: P1
audience: "BE"
status: draft-v1
generated: 2026-07-15
---

# Maps Provider Evaluation

## Overview

Every activity on ActivityMate happens at a real place — a cafe, a running track, a trekking trailhead. The mapping provider ActivityMate builds on determines whether a host can actually find and confidently select the venue they mean, and whether a guest can navigate there without confusion. This document evaluates Google Maps, Mapbox, and OpenStreetMap for that role and recommends one for launch.

## Context / Problem

Mapping providers differ along three axes that matter differently to a Mumbai-launch activity platform: point-of-interest (POI) data accuracy in Indian cities specifically (global coverage quality does not transfer uniformly — India's informal addressing, dense mixed-use buildings, and rapidly changing small-business landscape stress POI databases harder than US/EU markets), cost at scale (per-load and per-geocode pricing that compounds as DAU grows), and customization/control (styling, offline capability, vendor lock-in). A wrong choice here surfaces as a bad first impression at the exact moment a new host is trying to create their first activity — mistyped or missing venues erode trust in the product before a single message is sent.

## Options Considered

**Google Maps Platform.** Pros: by a wide margin the most accurate and complete India POI database — autocomplete and place search reliably surface small cafes, local eateries, and less-prominent venues in Mumbai neighborhoods that other providers frequently miss or mis-geocode; a Flutter SDK (`google_maps_flutter`) already well-supported and idiomatic for the chosen frontend stack; familiar UI/UX for Indian users who overwhelmingly already use Google Maps for navigation, minimizing onboarding friction. Cons: highest cost per API call at scale (Places Autocomplete, Geocoding, and Maps SDK loads are billed separately and can compound quickly with DAU growth); some vendor lock-in risk since venue IDs (Place IDs) are Google-specific and not portable to another provider without a re-geocoding effort.

**Mapbox.** Pros: materially cheaper at comparable usage volumes, highly customizable map styling (useful for ActivityMate's brand identity on the map view), and a capable Flutter SDK. Cons: India POI data is sourced substantially from OpenStreetMap contributions layered with Mapbox's own data partnerships, which is measurably weaker than Google's India coverage for smaller, informally-named venues — exactly the category (a specific chai stall, a lesser-known trekking base point) that shows up disproportionately in ActivityMate's activity categories (PRD-001 Section 8: cycling, trekking, badminton, photography walks).

**OpenStreetMap (self-hosted or via a pass-through provider).** Pros: free, fully open, no vendor cost at any scale, complete control. Cons: India coverage is the most inconsistent of the three — POI completeness and freshness depend entirely on volunteer contribution density, which is uneven across Mumbai's neighborhoods and near-absent in some suburban and semi-urban areas ActivityMate will expand into. Also requires ActivityMate to self-host or contract a tile-serving and geocoding layer, adding operational burden with no cost offset large enough to justify the accuracy tradeoff at this stage.

## Recommendation & Rationale

**Recommend Google Maps Platform at launch**, prioritizing venue-suggestion accuracy over cost. The core risk this decision manages is not cost overrun — it is a new host giving up on creating an activity because the venue-search box could not find their cafe, or a guest showing up at the wrong location because the map mis-geocoded a venue. Both directly undermine the Activity First and Trust Before Matching product principles (PRD-001 Section 6) before the trust system even gets a chance to work. At MVP scale, API cost is a manageable, monitored line item; it becomes a real constraint only well past PMF.

**Cost control at launch:** aggressive client-side caching of Place Autocomplete session tokens (Google's session-token billing model rewards batching an autocomplete session into one billed lookup instead of per-keystroke charges), server-side caching of resolved Place ID → coordinates + address mappings in PostgreSQL/PostGIS (geospatial-data-strategy.md) so a popular venue is geocoded once and reused indefinitely, and restricting Places Autocomplete bias to the launch city geofence (geofencing-city-launch-logic.md) to reduce irrelevant, wasted lookups.

**Revisit trigger:** re-evaluate the cost/accuracy tradeoff once monthly Maps Platform spend crosses a defined threshold tied to DAU growth, or ahead of any additional city launch — at that scale a hybrid approach (Google for autocomplete/geocoding, a cheaper tile provider for map rendering) becomes viable and should be re-costed against then-current pricing.

## Related Documents

- [location-privacy-strategy.md](location-privacy-strategy.md) — how coordinates from this provider are fuzzed before display
- [geofencing-city-launch-logic.md](geofencing-city-launch-logic.md) — city-boundary logic built on this provider's geocoding
- [meetup-point-safety-recommendations.md](meetup-point-safety-recommendations.md) — venue suggestions sourced from this provider's POI data
- [../10-database/geospatial-data-strategy.md](../10-database/geospatial-data-strategy.md) — PostGIS storage of resolved coordinates
- [../07-architecture/third-party-vendor-evaluation.md](../07-architecture/third-party-vendor-evaluation.md) — general vendor evaluation framework
