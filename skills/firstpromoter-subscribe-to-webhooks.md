---
name: firstpromoter-subscribe-to-webhooks
description: >-
  Create a FirstPromoter webhooks v2 subscription, choose event types, test it, and handle
  deliveries, retries and duplicate events correctly.
api: firstpromoter:firstpromoter-v2-webhooks
generated: '2026-08-13'
method: generated
source: openapi/firstpromoter-v2-webhooks-openapi.yml + https://docs.firstpromoter.com/webhooks-v2/overview
operations:
  - GET /webhooks/event_types
  - POST /webhooks
  - POST /webhooks/test
  - GET /webhooks/{webhook_id}/webhook_deliveries
  - POST /webhooks/{webhook_id}/webhook_deliveries/{id}/retry_delivery
---

# Subscribe to FirstPromoter webhooks (v2)

Base URL: `https://api.firstpromoter.com/api/v2/company`

```
Authorization: Bearer {api_key}
ACCOUNT-ID: {account_id}
```

## 1. List what you can subscribe to — `GET /webhooks/event_types`

Event types are `{entity}.{action}` strings across promoters, promoter campaigns, referrals,
commissions, payouts, payout methods, payments batches and contract documents — 44 documented types
in `asyncapi/firstpromoter-webhooks.yml`. Fetch them from the API rather than hard-coding, because
the docs page and the live list can drift.

## 2. Dry-run the endpoint — `POST /webhooks/test`

Required: `url`, `event_type`. Optional: `basic_auth_user`, `basic_auth_password`, `headers`.

Do this before creating the subscription so you find TLS, routing and auth problems while nothing
real is firing.

## 3. Create the subscription — `POST /webhooks`

Documented subscription fields:

| field | required | meaning |
|---|---|---|
| `url` | yes | HTTPS endpoint receiving the payloads |
| `event_types` | yes | array of event type strings |
| `campaign_ids` | no | restrict to campaigns; `[1]` (default) means all |
| `description` | no | human label |
| `active` | no | pause without deleting; defaults to `true` |
| `headers` | no | arbitrary headers sent on every request |
| `max_retries` | no | defaults to `3`; `0` disables retries |

Subscriptions are per account and unlimited, unlike the legacy three-URLs-per-campaign scheme.

## 4. Receive and acknowledge

Every delivery is a `POST` with `Content-Type: application/json` plus `X-Event-Id` and
`X-Event-Type` headers. The body carries `event_id`, `event_type`, `action`, `entity_type`,
`entity_id`, `changes`, `data` and `timestamp`.

Return any `2xx` to acknowledge. **Deduplicate on `event_id`** — FirstPromoter retries, so the same
`event_id` can arrive more than once. There is no HMAC signature on the payload; if you need to
authenticate the sender, set a shared secret through the subscription's `headers` field and check
it on your endpoint.

For `updated` events, `changes` maps each changed field to `[old_value, new_value]`; it is empty on
`created` and `deleted`, where the full resource is in `data` instead.

## 5. Debug a failure

- `GET /webhooks/{webhook_id}/webhook_deliveries` — delivery history with status
  (`pending`, `delivered`, `retrying`, `failed`).
- `GET /webhooks/{webhook_id}/webhook_deliveries/{id}` — a single delivery record.
- `POST /webhooks/{webhook_id}/webhook_deliveries/{id}/retry_delivery` — replay it by hand.

Retry backoff is `attempt × 20 minutes` (20, 40, 60 …) until `max_retries` is reached, then the
delivery is marked `failed`. The default request timeout is 30 seconds — return `2xx` fast and do
the work asynchronously.
