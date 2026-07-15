---
title: "Meetup Point Safety Recommendations"
folder: 17-maps-location
purpose: "Safe meetup points"
pages_estimate: 3
prerequisites: "location-privacy-strategy"
dependencies: "trust-safety-fraud, operations-support"
priority: P0
audience: "BE, PM"
status: draft-v1
generated: 2026-07-15
---

# Meetup Point Safety Recommendations

## Overview

Where two strangers first physically meet is the single highest-risk moment in ActivityMate's entire product loop. This document defines the policy that steers hosts toward safe, verifiable public venues as the default meeting point, and away from private addresses.

## Context / Problem

An activity listing needs a location, and a host choosing "my flat" or an unnamed, unverifiable spot as the meeting point for a first-time meetup with strangers is a materially different risk profile than choosing a named cafe with public visibility, staff presence, and other patrons around. This is not a hypothetical edge case — it is one of the most common safety patterns across every stranger-meetup platform, and it is entirely preventable by product design rather than left to individual judgment. Because ActivityMate explicitly targets night activities post-10pm (PRD-001 Section 3: "Avoid going alone for night activities post 10pm"), the exposure window is not limited to daytime, low-risk scenarios — it includes exactly the conditions where a bad meeting-point choice carries the most risk.

## The Actual Policy

**Default and strongly steered: verified public venues.** When a host creates an activity, the venue field is backed by Google Maps Place search (maps-provider-evaluation.md) constrained, by default, to place categories carrying an inherent level of public accountability — cafes, restaurants, coworking spaces, gyms, public parks, malls, museums, and similar named, publicly-listed venues. These surface first and are visually distinguished in the venue picker as "Recommended — public venue."

**Private residential addresses — allowed only with friction, never as a first-time default.** A host is not flatly forbidden from listing a private address (some legitimate activities — a board-game night, a home-cooked dinner — genuinely happen at someone's home, and ActivityMate does not want to push all such activities off-platform where they'd be coordinated with zero safety infrastructure at all). But listing a private address triggers: (a) an explicit in-app warning to prospective guests shown before RSVP ("this activity is at a private residence — read our meetup safety guidance"); (b) a requirement that the host be at a minimum trust tier (a meaningfully verified, positive-history account, not a brand-new signup) before a private-address activity is even listed; (c) exclusion from the discovery feed's default "recommended" surfacing — private-address activities are search-reachable but not pushed.

**First-meetup guidance surfaced to guests.** Regardless of venue type, any activity that is a guest's first-ever meetup with a given host surfaces a lightweight, non-blocking safety checklist at RSVP time: meet at the stated public point rather than an alternate location suggested last-minute, tell someone your plans, use the in-app check-in (location-privacy-strategy.md) rather than sharing precise location outside the app. This mirrors and operationalizes standard guidance from Trust & Safety without needing every guest to have independently sought it out.

**Change-of-venue red flag.** A host changing the meetup venue after guests have already RSVPed — especially a late change from a public venue to a private one — is flagged for review by the trust engine (13-trust-safety-fraud) as an anomalous pattern, since this specific bait-and-switch (announce a safe public venue, redirect once guests are committed) is a known manipulation pattern on meetup platforms.

## Enforcement

Venue-category classification is derived automatically from the Google Places category returned at venue-selection time, not self-declared by the host, so a host cannot mislabel a private address as a cafe to bypass the friction path. Trust-tier gating on private-address listings is enforced server-side at activity-creation time. Operations (34-operations-support) partners with Trust & Safety on periodically reviewing which place categories should count as "verified public" as Mumbai coverage data evolves.

## Related Documents

- [location-privacy-strategy.md](location-privacy-strategy.md) — precise-location check-in flow referenced in first-meetup guidance
- [maps-provider-evaluation.md](maps-provider-evaluation.md) — Place category data this policy is built on
- [../13-trust-safety-fraud/safety-engine-spec.md](../13-trust-safety-fraud/safety-engine-spec.md) — anomaly detection for venue-change red flags
- [../34-operations-support/community-management-ops.md](../34-operations-support/community-management-ops.md) — ops partnership on venue-category review
- [../15-activities-communities/activity-lifecycle-spec.md](../15-activities-communities/activity-lifecycle-spec.md) — where venue selection sits in activity creation
