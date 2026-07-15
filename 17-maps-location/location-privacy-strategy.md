---
title: "Location Privacy Strategy"
folder: 17-maps-location
purpose: "Privacy strategy"
pages_estimate: 5
prerequisites: "trust-safety-fraud"
dependencies: "none"
priority: P0
audience: "BE, SEC"
status: draft-v1
generated: 2026-07-15
---

# Location Privacy Strategy

## Overview

ActivityMate connects strangers for real-world meetups, which means location data is simultaneously essential to the product and one of the highest-severity safety risks it carries. This document is the policy governing how precise a user's location is ever allowed to be to another user, at every stage of the interaction, from browsing activities to standing at the meetup point.

## Context / Problem

Precise, continuously-available location is a stalking vector on any platform that facilitates in-person meetups — a user could otherwise infer another user's home, workplace, or daily routine from repeated exposure to precise coordinates, independent of whether any activity is ever actually attended together. This risk is structurally different from a delivery or ride-hailing app's location handling, because on ActivityMate the other party is a stranger the user has not necessarily built trust with yet, and the whole point of the product is to bring that stranger physically closer. The parent README states this plainly: location must be engineered as a safety feature, not merely a proximity convenience. The policy below is the concrete rule set that follows from that stance.

## The Actual Policy

**Default state — fuzzed, approximate location only.** A user's location, wherever it is shown to another user (activity host's general area, "guests near you," a discovery feed sorted by distance), is never the precise GPS coordinate. It is fuzzed to a randomized point within a defined radius (on the order of several hundred meters to roughly a kilometer, randomized per-session rather than a fixed offset, so the fuzzing radius itself cannot be reverse-engineered by repeated observation) centered on the user's true location, rounded to the nearest named locality/neighborhood for display text (e.g., "Bandra West" rather than a street address). This is the state for: profile location, activity discovery/search radius filtering, and any "distance away" indicator shown pre-RSVP.

**Activity venue location — always precise, because it is not a person's location.** The venue itself (the cafe, park, trailhead) is a public place the host explicitly chooses to disclose as part of creating the activity, and is shown at full precision to anyone who can see the activity listing — fuzzing a venue address would defeat the purpose of the listing. The distinction the policy draws is between *a person's* location (always fuzzed by default) and *a place* a host has voluntarily and publicly designated as a meeting point (never fuzzed).

**Precise location exchange — narrow, momentary, and mutual only.** A user's precise, real-time location is disclosed to another specific user in exactly one circumstance: an active, mutually confirmed meetup check-in, where both parties for a specific activity have opted in (typically within a bounded window immediately before/during the scheduled activity time) to share live location with each other for wayfinding and safety purposes — e.g., "guest is 4 minutes away." This precise share is: (a) scoped only to the other confirmed participant(s) of that specific activity, never broadcast more widely; (b) time-boxed, automatically expiring a set duration after the activity's scheduled end time or on manual end, whichever is sooner; (c) revocable by either party at any time; (d) never persisted as a location history — only the fact that a check-in occurred is logged, not a trail of coordinates.

**No passive location tracking.** ActivityMate does not collect background/continuous location in the way a fitness or ride-hailing app does. Location is read only when the app is in active use for a location-relevant action (browsing nearby activities, an active check-in) — there is no standing location service running against a user's will or awareness, and this is enforced at the mobile permission level (Flutter location APIs requested contextually, "while using app," never "always").

**Emergency override.** If a user files an active safety report during a live check-in window (13-trust-safety-fraud/safety-engine-spec.md), Trust & Safety Ops may access the precise check-in location data already shared for that session to support an immediate safety response — this is a narrow, logged, auditable exception, not a standing surveillance capability.

## Enforcement

Location fuzzing is applied server-side, not client-side — the backend never transmits a user's true coordinate to another user's client in the default state, so there is no client-side value to intercept or reverse-engineer. Precise check-in sharing requires an explicit, per-session, mutual opt-in UI action from both parties, logged with consent timestamps for DPDP Act accountability. Any engineering change that would expose a precise coordinate outside the narrow check-in flow requires Security Lead sign-off before merge, treated with the same rigor as an auth change.

## Related Documents

- [maps-provider-evaluation.md](maps-provider-evaluation.md) — provider whose geocoding this policy fuzzes before display
- [meetup-point-safety-recommendations.md](meetup-point-safety-recommendations.md) — the venue-precision counterpart to this person-precision policy
- [geofencing-city-launch-logic.md](geofencing-city-launch-logic.md) — city-level location logic operating above the per-user fuzzing described here
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md) — safety report flow referenced in the emergency override
- [../31-privacy-compliance-legal/india-dpdp-act-compliance.md](../31-privacy-compliance-legal/india-dpdp-act-compliance.md) — consent and data-minimization basis for this policy
- [../10-database/geospatial-data-strategy.md](../10-database/geospatial-data-strategy.md) — PostGIS-level implementation of fuzzing
