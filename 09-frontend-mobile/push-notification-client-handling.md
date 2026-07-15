---
title: "Push Notification Client Handling"
folder: 09-frontend-mobile
purpose: "Client push handling"
pages_estimate: 3
prerequisites: "mobile-architecture-overview"
dependencies: "realtime-chat-notifications"
priority: P1
audience: "FE"
status: draft-v1
generated: 2026-07-15
---

# Push Notification Client Handling

## Overview

The Flutter client receives push payloads from FCM (chosen in [../07-architecture/third-party-vendor-evaluation.md](../07-architecture/third-party-vendor-evaluation.md)) originating from the backend's Notifications module ([../08-backend-services/service-catalog.md](../08-backend-services/service-catalog.md)). This document specifies client-side handling: payload shape, foreground/background behavior, and how push interacts with the live WebSocket connection so a user never gets duplicate or conflicting signals.

## Payload Shape

```json
{
  "notification": {
    "title": "Priya joined your Coffee Meetup",
    "body": "Tap to see who's coming"
  },
  "data": {
    "type": "activity_rsvp",
    "activity_id": "b3f1...",
    "deep_link": "https://activitymate.in/a/b3f1...",
    "trust_alert": "false"
  }
}
```

- `data.type` drives client-side routing/handling logic (an enum matching backend event types: `activity_rsvp`, `activity_cancelled`, `chat_message`, `trust_score_change`, `report_update`, `verification_status`).
- `data.deep_link` reuses the exact deep-link contract from [navigation-deep-linking.md](navigation-deep-linking.md) — tapping a notification and tapping a shared link resolve through the identical router path, so there is only one navigation-resolution code path to maintain and test.

## Foreground Behavior

When the app is in the foreground, FCM notifications are **not** shown as system notifications for `chat_message` type — the WebSocket connection already delivers the message live and the chat UI (if the relevant room is open) or an in-app toast (if not) handles it, avoiding a duplicate signal for the same event arriving via two channels. For all other types (`activity_rsvp`, `trust_score_change`, etc.), the app shows a local in-app banner rather than relying on the OS notification tray, since the user is already looking at the app.

```dart
FirebaseMessaging.onMessage.listen((RemoteMessage message) {
  final type = message.data['type'];
  if (type == 'chat_message' && ref.read(activeChatRoomProvider) == message.data['room_id']) {
    return; // WebSocket already delivering this live; suppress duplicate
  }
  ref.read(inAppNotificationBannerProvider.notifier).show(message);
});
```

## Background / Terminated Behavior

- **Background (app suspended, not killed):** standard FCM system notification is shown; tapping it invokes `FirebaseMessaging.onMessageOpenedApp`, which routes through the same deep-link resolver as [navigation-deep-linking.md](navigation-deep-linking.md).
- **Terminated (app fully killed):** `FirebaseMessaging.instance.getInitialMessage()` is checked on cold start, before the router is initialized, so a tap-to-open from a killed state lands directly on the target screen rather than the default `/discover` route.
- **Data-only messages** (no `notification` block, `data` only) are used for silent state updates that shouldn't interrupt the user — e.g., pre-fetching an updated trust score cache entry so it's ready by the time the user next opens the profile screen — handled via `FirebaseMessaging.onBackgroundMessage`, registered as a top-level function per Flutter's isolate requirement for background handlers.

## Trust and Safety-Relevant Notifications

`trust_score_change` and `report_update` notifications are never suppressed or batched, even under a future "reduce notification noise" feature — per the trust-score transparency requirement in [../13-trust-safety-fraud/trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md), a user must always be promptly informed of a change to their own trust standing or the outcome of a report they filed.

## Permission Handling

Push permission (iOS explicit prompt, Android 13+ explicit prompt) is requested contextually — after the user completes onboarding and joins or creates their first activity, not immediately on app launch — since a cold, context-free permission prompt has a measurably lower opt-in rate than one requested at the moment its value is obvious ("get notified when someone joins your activity").

## Related Documents

- [mobile-architecture-overview.md](mobile-architecture-overview.md)
- [navigation-deep-linking.md](navigation-deep-linking.md)
- [../16-realtime-chat-notifications/notification-system-architecture.md](../16-realtime-chat-notifications/notification-system-architecture.md)
- [../13-trust-safety-fraud/trust-score-transparency-policy.md](../13-trust-safety-fraud/trust-score-transparency-policy.md)
