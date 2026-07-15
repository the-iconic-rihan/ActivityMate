# 17-maps-location/

**Phase:** Phase 4 — Trust, Safety & Core Product  
**Priority:** P1 (P1 — blocks public launch)  
**Owner:** BE / Infra  
**Depends on:** database  
**Document count:** 4

## Purpose

Geolocation, proximity, and — critically — location privacy.

## Why this folder exists

Precise location is a stalking vector on a platform that facilitates real-world meetups. This folder exists specifically to make location a safety feature rather than a liability.

## Documents

| File | Purpose | Description | Pages | Prerequisites | Dependencies | Priority | Audience |
|---|---|---|---|---|---|---|---|
| [maps-provider-evaluation.md](maps-provider-evaluation.md) | Provider choice | Compares Google Maps (best India point-of-interest accuracy, higher cost at scale), Mapbox (cheaper and more customizable, weaker India POI data), and OpenStreetMap (free, most inconsistent India coverage). Recommends Google Maps at launch for venue-suggestion accuracy, revisiting the cost tradeoff at scale. | 4 | architecture | none | P1 | BE |
| [location-privacy-strategy.md](location-privacy-strategy.md) | Privacy strategy | Approximate, fuzzed-radius location shown by default; precise location is only shared momentarily during an active, mutually confirmed meetup check-in. | 5 | trust-safety-fraud | none | P0 | BE, SEC |
| [geofencing-city-launch-logic.md](geofencing-city-launch-logic.md) | City launch gating | How city-launch geofence boundaries gate activity visibility. | 2 | maps-provider-evaluation | market-research | P1 | BE |
| [meetup-point-safety-recommendations.md](meetup-point-safety-recommendations.md) | Safe meetup points | Suggests verified public venues — cafes, coworking spaces — as the default meeting point over private addresses. | 3 | location-privacy-strategy | trust-safety-fraud, operations-support | P0 | BE, PM |
