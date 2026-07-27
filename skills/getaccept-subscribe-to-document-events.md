---
name: Subscribe to document events (webhooks)
description: Register a GetAccept webhook subscription so your system is notified when a document is signed, reviewed, or rejected, then verify and monitor delivery.
api: openapi/getaccept-openapi-original.json
operations:
  - Authenticate
  - ListSubscriptionEvents
  - CreateSubscription
  - CreateSignedSubscription
  - TestSubscription
  - ListSubscriptions
  - GetSubscriptionErrors
  - DeleteSubscription
---

# Subscribe to document events (webhooks)

GetAccept pushes document lifecycle events to your endpoint via webhook subscriptions instead of requiring you to poll.

## Auth
- Authenticate with OAuth2 or a bearer token (`Authenticate`, `POST /auth`); send `Authorization: Bearer <token>`.

## Steps
1. **Discover events.** Call `ListSubscriptionEvents` (`GET /subscriptions/events`) to get the live list of available event names.
2. **Subscribe.** Register your endpoint with `CreateSubscription` (`POST /subscriptions`), passing your `host` URL and the target `event`. For the common signed case you can use the convenience endpoint `CreateSignedSubscription` (`POST /subscriptions/events/signed`); `reviewed` and `rejected` have equivalents.
3. **Test delivery.** Fire a test payload with `TestSubscription` (`POST /subscriptions/test`) and confirm your endpoint receives the `Webhook` payload (`document`, `recipient`, `entity`, `custom_fields`, `subscription_id`).
4. **Verify + monitor.** List active subscriptions with `ListSubscriptions` (`GET /subscriptions`) and check failed deliveries with `GetSubscriptionErrors` (`GET /subscriptions/errors`).
5. **Clean up.** Remove a subscription with `DeleteSubscription` (`DELETE /subscriptions/{subscriptionId}`).

## Conventions
- Payloads are delivered as HTTP POST with a JSON `Webhook` body (see asyncapi/getaccept-webhooks.yml).
- Errors return `{ error, description, status }`; check `GetSubscriptionErrors` for delivery failures.
