---
title: "Third Party Vendor Evaluation"
folder: 07-architecture
purpose: "Build vs buy"
pages_estimate: 6
prerequisites: "system-architecture-overview"
dependencies: "maps-location"
priority: P0
audience: "BE, DO, F"
status: draft-v1
generated: 2026-07-15
---

# Third Party Vendor Evaluation

## Overview

ActivityMate depends on exactly three external vendor categories at MVP: a maps provider, an SMS/OTP provider, and a push notification provider. This document scores the realistic options on India coverage, cost at scale, and vendor lock-in risk, and names the recommendation for each.

## Context

Every third party is a dependency the small ActivityMate team does not control the roadmap of, and every one touches something safety- or trust-critical (location, phone verification, safety alerts). The evaluation criteria are deliberately narrow: India-specific coverage/compliance, cost as usage scales past MVP, and how hard it would be to switch later.

## Maps Provider

### Options Considered

**Google Maps Platform**
- Pros: best-in-class India road/POI coverage, most Mumbai users already trust Google Maps pins mentally, mature Flutter SDK, reliable geocoding for Indian addresses (which are often informally described).
- Cons: usage-based pricing that scales linearly and can get expensive at high query volumes (every activity creation, every "activities near me" feed load is a potential maps call); requires careful client-side caching discipline to control cost.

**Mapbox**
- Pros: more generous free tier at low volume, highly customizable map styling, good Flutter SDK.
- Cons: India POI/geocoding data density is noticeably weaker than Google's in tier-2 areas of Mumbai (less of a gap in South Mumbai/BKC, more of one in outer suburbs) — a real risk for a product whose core value is "find people near you for real activities" in exactly those less-central areas.

**OpenStreetMap (self-hosted tiles) + Nominatim**
- Pros: zero per-query cost, full control, no vendor lock-in.
- Cons: requires operating and maintaining tile-serving and geocoding infrastructure in-house — a distraction from product work for a small team, and OSM India geocoding quality for informal addresses is inconsistent.

### Recommendation

**Google Maps Platform** for MVP, with PostGIS as the actual geospatial source of truth (see [../10-database/geospatial-data-strategy.md](../10-database/geospatial-data-strategy.md)) so Google is used only for the map UI, geocoding, and reverse-geocoding — not for proximity queries, which run against Postgres directly and cost nothing per query. This bounds Google usage to a controllable, cacheable subset of calls (geocode-on-activity-creation, not geocode-on-every-feed-load), keeping cost predictable while getting best-in-class India coverage. Lock-in risk is mitigated by keeping all coordinate data in ActivityMate's own Postgres/PostGIS store — switching map *rendering* providers later would touch only the Flutter map widget, not the data layer.

## SMS / OTP Provider

Phone-OTP is the foundation of ActivityMate's entire auth model (see [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md)), so this vendor choice is P0-critical and must be India DLT (Distributed Ledger Technology, TRAI's anti-spam regime) compliant — non-compliant providers will simply have their messages blocked by Indian telecom carriers.

### Options Considered

**MSG91 (or comparable India-first provider, e.g. Gupshup, Karix/Kaleyra)**
- Pros: DLT registration handled/guided as part of onboarding, India-specific delivery route optimization (higher OTP delivery success than generic global providers), competitive per-SMS cost at India volumes, typically includes WhatsApp OTP fallback as an add-on.
- Cons: smaller global footprint if ActivityMate ever expands beyond India; requires ActivityMate to independently complete DLT template/sender-ID registration, which has lead time (allow 1-2 weeks) and cannot be skipped.

**Twilio**
- Pros: excellent global reputation, mature SDKs, best-in-class documentation and reliability tooling.
- Cons: India OTP delivery via Twilio still routes through DLT-compliant local aggregators under the hood and is typically more expensive per message at India volumes than an India-first provider, with less direct control over delivery troubleshooting for India-specific carrier issues.

### Recommendation

**An India-first, DLT-compliant provider (MSG91 or equivalent, selected via direct vendor bake-off before build start)**, because OTP delivery reliability in India is the single highest-leverage lever on signup conversion, and India-first providers consistently outperform global providers on India delivery rates at lower cost. Lock-in risk is mitigated by wrapping the SMS send call behind an internal `OtpProvider` interface in the Identity module so switching providers later touches one adapter, not call sites across the codebase.

## Push Notification Provider

### Options Considered

**Firebase Cloud Messaging (FCM)**
- Pros: free, the de facto standard for Android push (which is the primary platform given device mix), first-class Flutter plugin support (`firebase_messaging`), handles both foreground and background delivery patterns needed for chat/activity alerts.
- Cons: Google dependency; iOS push still requires APNs certificates configured through Firebase, adding setup steps for the P1 iOS release.

**OneSignal**
- Pros: provider-agnostic abstraction over FCM/APNs, richer built-in analytics/segmentation dashboard out of the box.
- Cons: adds a third-party layer on top of FCM for no meaningful MVP benefit — ActivityMate doesn't need OneSignal's marketing-segmentation features at launch, and it's one more vendor relationship and one more point of failure for a safety-relevant notification path (e.g., trust score alerts, report confirmations).

### Recommendation

**FCM directly**, since it is free, is the native path for Android (the primary launch platform), and ActivityMate's notification needs at MVP (chat messages, RSVP updates, trust alerts) don't require OneSignal's marketing tooling. Revisit only if a future growth-marketing need (see [../32-growth-marketing/README.md](../32-growth-marketing/README.md)) specifically requires segmentation/campaign tooling FCM doesn't provide.

## Summary Table

| Category | Recommendation | Primary Reason | Lock-in Mitigation |
|---|---|---|---|
| Maps | Google Maps Platform | Best India POI/geocoding coverage | PostGIS is source of truth; Google used only for UI/geocoding |
| SMS/OTP | India-first DLT-compliant provider | Highest India delivery reliability, lowest cost at volume | Internal `OtpProvider` adapter interface |
| Push | Firebase Cloud Messaging | Free, native Android/Flutter support | Standard FCM payload shape, swappable if needed |

## Related Documents

- [system-architecture-overview.md](system-architecture-overview.md)
- [../17-maps-location/maps-provider-evaluation.md](../17-maps-location/maps-provider-evaluation.md)
- [../10-database/geospatial-data-strategy.md](../10-database/geospatial-data-strategy.md)
- [../12-auth/authentication-architecture.md](../12-auth/authentication-architecture.md)
- [../09-frontend-mobile/push-notification-client-handling.md](../09-frontend-mobile/push-notification-client-handling.md)
