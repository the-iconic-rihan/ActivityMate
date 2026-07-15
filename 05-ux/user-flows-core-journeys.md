---
title: "User Flows Core Journeys"
folder: 05-ux
purpose: "Core flows"
pages_estimate: 10
prerequisites: "information-architecture"
dependencies: "trust-safety-fraud, activities-communities"
priority: P0
audience: "UXR, UI, FE"
status: draft-v1
generated: 2026-07-15
---

# User Flows: Core Journeys

## Overview

This document specifies the five flows that constitute ActivityMate's core loop, screen by screen, in enough detail for design and Flutter engineering to build against without re-deriving product decisions mid-implementation. Every flow is written against the [information-architecture.md](information-architecture.md) five-tab structure and assumes the trust-gating rules from [../04-product/product-principles.md](../04-product/product-principles.md).

## Actors / Personas Involved

Referencing [persona-definitions.md](persona-definitions.md): **Relocated Professional** (highest urgency, lowest existing Mumbai network, most likely to host and join solo), **Solo Explorer** (traveller/newcomer, short time horizon, high safety sensitivity), **Weekend Hobbyist** (existing local base, joins recurring category activities, most likely to convert into a Community). All three pass through the same flows below; differences are noted inline where relevant (e.g., a Solo Explorer's shorter account age affects trust score display).

---

## Flow 1: Onboarding and Verification

**Goal:** get a new user from install to a usable, minimally-verified account with the least friction that trust requirements allow.

1. **Splash → Phone entry.** User enters phone number. No email/social login offered first — phone is the primary identity anchor for the Indian market context, consistent with [../12-auth/oauth-social-login-integration.md](../12-auth/oauth-social-login-integration.md) treating OAuth as secondary, not primary.
2. **OTP verification.** 6-digit SMS OTP, 3 attempts, 60-second resend cooldown. This step is mandatory and blocking — no skip option exists. Successful OTP verification is the minimum bar to browse Discover in preview mode (see Edge Cases in [information-architecture.md](information-architecture.md)).
3. **Basic profile.** Name, age (used to enforce 21+ platform-wide, per PRD-001 target demographic), gender (used for safety-preference matching and female-retention tracking), profile photo (mandatory, not optional — a blank avatar is treated as a trust-negative signal downstream).
4. **Selfie verification (encouraged, not blocking).** A liveness-checked selfie compared against the profile photo. Presented as "Verify now — takes 30 seconds" with a visible trust-score preview showing the score jump this step grants, rather than a bare compliance form. Skippable, but the skip path shows a persistent, dismissible prompt on the Profile/Trust tab until completed — nagging by design, since selfie verification materially reduces catfishing risk and the product benefits from high completion.
5. **Interest/category selection.** User picks 3+ activity categories from PRD-001 §8's list (Coffee, Cycling, Trekking, Badminton, etc.) to seed the Discover feed's initial ranking — this is the cold-start signal referenced in [../14-matching-recommendation/cold-start-strategy.md](../14-matching-recommendation/cold-start-strategy.md).
6. **Safety setup (mandatory, not skippable).** User adds at least one emergency contact and reviews how the panic button and safety check-in work, with a short interactive walkthrough. This step is blocking — unlike selfie verification — because it is a prerequisite for the safety check-in flow (Flow 4) to function at all, and a user who has never seen the panic button before a real emergency is a design failure.
7. **Landing.** User lands on Discover, populated by category interests plus location radius (requested via standard OS location permission prompt, with a clear pre-permission explainer screen — "We use your location to show nearby activities. We never share your exact location with other users.").

**Verification-tier note:** government ID and LinkedIn verification are not part of onboarding; they are optional, later, progressive upgrades accessible from Profile/Trust once a user wants to unlock hosting privileges or late-night (post-10pm) activities. See [../13-trust-safety-fraud/user-verification-levels.md](../13-trust-safety-fraud/user-verification-levels.md).

---

## Flow 2: Create Activity

**Goal:** let a verified user post an activity with enough structure that joiners can trust it, while keeping the form fast enough not to suppress spontaneous posting.

1. **Entry.** Floating action button from Discover or My Activities.
2. **Category selection.** Grid of PRD-001 §8 categories (Coffee, Breakfast, Dinner, Cycling, Trekking, Movie, Badminton, Running, Photography Walk, Board Games, Museum, Networking, Coworking, etc.) plus a fallback "Other" with free-text — kept to encourage categorization for Discover ranking without blocking niche activities.
3. **Details.** Title, description, date/time, capacity (min/max participants), and venue.
4. **Venue selection — public-venue-first default.** The venue picker defaults to a curated map of public places (cafés, parks, sports complexes) sourced from the PostGIS-backed location service (see [../17-maps-location/README.md](../17-maps-location/README.md)); a private-address option exists but requires an extra confirmation step ("This is a private location. We recommend public venues, especially for a first meetup.") — friction added deliberately, per [product-principles.md](../04-product/product-principles.md) Principle 3, even though it costs some hosts convenience.
5. **Time-of-day trust gate.** If the activity start time is after 10pm, the host must have at least Selfie-verified trust tier; hosts below that tier see the field disabled with an inline explanation and a link to complete verification. This directly implements PRD-001's explicit user goal of avoiding solo late-night risk.
6. **Participant preferences (optional).** Minimum trust-score threshold to join, gender-composition preference (e.g., a Solo Explorer may set "female participants only" for a late-night activity) — surfaced as an accessible toggle, not hidden in advanced settings, since safety-relevant filtering should not require expertise to find.
7. **Review & publish.** Summary screen showing exactly what joiners will see, including the host's own trust badges — reinforcing to the host that their own trust signal is part of what they're publishing.
8. **Post-publish.** Activity appears in Discover for matching users within the location radius and category/interest overlap; host lands on the activity's management screen (RSVP list, chat, edit/cancel).

---

## Flow 3: Discover and Join

**Goal:** let a user find a relevant, trustworthy activity and join with confidence, not just curiosity.

1. **Discover feed.** Card-based feed, each card showing: activity title/category icon, date/time, venue (approximate map pin, exact address revealed after joining), host's name, photo, and **verification badge cluster** (see [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md)), current RSVP count vs. capacity, and host trust score band (not raw number — see [../06-design-system/typography-color-system.md](../06-design-system/typography-color-system.md) for the visual treatment).
2. **Filter/search.** Category, date range, distance radius, "verified hosts only" toggle (surfaced prominently, not buried), gender-composition filter matching what hosts set in Flow 2.
3. **Activity detail screen.** Full description, participant list (each participant shown with their own trust badge — visibility is symmetric, not host-only), venue map, host's completed-activity count and rating history, and a prominent "Request to Join" or "Join" button (instant-join vs. host-approval is a per-activity host setting from Flow 2).
4. **Trust-score gate check.** If the joining user's trust score is below the host's stated minimum (Flow 2, step 6), the Join button is disabled with an explanation and a path to raise trust score (e.g., "Complete selfie verification to meet this host's requirement").
5. **Join confirmation.** User confirms; if host-approval required, status shows "Pending host approval" in My Activities; if instant-join, exact venue address and a chat thread unlock immediately.
6. **Host approval (if applicable).** Host sees join requests in the activity management screen with the requester's trust profile surfaced before accepting — this is the point where host judgment is explicitly preserved per [../04-product/product-principles.md](../04-product/product-principles.md) Principle 4 (Community Driven, Not Algorithm Driven): the platform does not auto-accept participants even at full trust score.

---

## Flow 4: Pre-Meetup Safety Check

**Goal:** give every participant — not just the anxious ones — a lightweight, default-on safety net for the actual meetup moment, without making the app feel alarmist.

1. **T-minus reminder.** 2 hours before activity start, all confirmed participants get a push notification: activity details, venue map, and a one-tap "Share my status with emergency contact" prompt.
2. **Check-in widget activation.** The activity detail screen surfaces a persistent Safety Check-In card starting at T-minus 30 minutes: "Let us know you've arrived safely" with a single large tap target.
3. **Arrival check-in.** User taps "I've arrived" (optionally auto-suggested via geofence proximity to the venue, but never automatic without explicit tap — no silent location tracking). Confirmation is timestamped and visible to the user's configured emergency contact if they opted in during Flow 1's safety setup.
4. **Panic button — always accessible.** A persistent, visually distinct panic-button affordance is present on the activity detail screen and chat screen for the full duration of any live activity window (from T-minus 30 minutes to estimated end time), reachable in one tap per the two-tap IA rule in [information-architecture.md](information-architecture.md) being exceeded deliberately in the safety-critical case — one tap, not two. Triggering it immediately: (a) sends the user's live location to their emergency contact(s), (b) opens a discreet in-app options sheet ("Call local emergency services," "Alert ActivityMate Trust & Safety," "I'm safe, false alarm"), and (c) logs an incident record for the Trust & Safety team per [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md).
5. **No-check-in escalation.** If a user hasn't tapped "I've arrived" by 20 minutes past activity start, they receive a gentle in-app nudge; if still no response by 45 minutes past start, their opted-in emergency contact receives an automated notification ("[Name] hasn't checked in for their ActivityMate meetup — here's the last known plan") — this is opt-in behavior configured in Flow 1, not silent surveillance.
6. **Post-arrival, mid-activity.** Check-in widget collapses to a minimized state once arrival is confirmed for all participants, but the panic button remains accessible throughout.

---

## Flow 5: Post-Activity Feedback and Rating

**Goal:** close the loop that feeds the trust score, in a way fast enough that completion rate stays high (a slow rating flow gets skipped, which starves the trust engine of data).

1. **Trigger.** Push notification 30 minutes after the activity's stated end time: "How was [Activity Name]?"
2. **Rating screen.** Simple 5-star rating for the host (if joiner) or for each participant (if host) — capped to a maximum of 2 taps: star rating, then an optional one-line tag selection ("Great host," "No-show," "Felt unsafe," "Would do again") rather than free-text by default, to keep completion friction low while still generating structured signal for [../13-trust-safety-fraud/trust-engine-architecture.md](../13-trust-safety-fraud/trust-engine-architecture.md).
3. **Safety-flag branch.** If "Felt unsafe" or any safety-relevant tag is selected, the flow branches immediately into the formal report flow (see [../13-trust-safety-fraud/reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md)) rather than silently logging it as a low star rating — a safety signal must never be reducible to "just a bad rating."
4. **No-show reporting.** If a participant didn't show, hosts can flag no-show directly from this screen, which feeds the cancellation/no-show rate input to trust score (see [../15-activities-communities/cancellation-no-show-policy.md](../15-activities-communities/cancellation-no-show-policy.md)) — this is deliberately separated from the general 5-star rating since no-shows are a distinct, objectively verifiable signal.
5. **Community prompt.** If this is the Nth activity (threshold set in [../15-activities-communities/community-model-spec.md](../15-activities-communities/community-model-spec.md)) with substantially overlapping participants, the app proposes "Turn this into a group?" — the organic community-formation trigger described in [../04-product/product-strategy-doc.md](../04-product/product-strategy-doc.md).
6. **Skip path.** Rating is skippable (a hard-block would suppress future activity creation, which is worse for the platform than a missing rating), but skipped ratings are tracked as a response-rate input to the skipper's own trust score — reinforcing that participation in the trust loop is itself part of being a trustworthy user.

## Edge Cases (Cross-Flow)

- **Activity cancelled by host after joins:** all participants notified immediately, host's cancellation-rate trust input increments, and joiners are shown a one-tap path back into Discover with similar activities pre-filtered.
- **User blocks another user mid-chat:** any pending shared activity is unaffected structurally but the blocking user's future Discover feed suppresses activities hosted or joined by the blocked user, per [../13-trust-safety-fraud/reporting-blocking-system.md](../13-trust-safety-fraud/reporting-blocking-system.md).
- **Low-connectivity / offline states during safety check-in:** the check-in widget queues the tap locally and syncs when connectivity resumes (see [../09-frontend-mobile/offline-first-sync-strategy.md](../09-frontend-mobile/offline-first-sync-strategy.md)); the panic button, however, attempts an immediate SMS-fallback path if data connectivity is unavailable, since this is the one flow where offline-queueing latency is unacceptable.
- **Trust score changes mid-flow:** if a user's trust score drops below a host's threshold between joining and the activity date (e.g., due to an unrelated report), the system does not silently un-join them; it flags the host for a manual review decision rather than auto-removing a committed participant.

## Related Documents

- [information-architecture.md](information-architecture.md)
- [trust-safety-ux-patterns.md](trust-safety-ux-patterns.md)
- [journey-maps.md](journey-maps.md)
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md)
- [../15-activities-communities/activity-lifecycle-spec.md](../15-activities-communities/activity-lifecycle-spec.md)
