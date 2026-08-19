---
name: firstpromoter-track-revenue-events
description: >-
  Report signups, sales, refunds and cancellations to FirstPromoter from your own billing system
  so affiliate commissions are calculated correctly, without relying on browser JavaScript.
api: firstpromoter:firstpromoter-v2-tracking
generated: '2026-08-13'
method: generated
source: openapi/firstpromoter-v2-tracking-openapi.yml + https://docs.firstpromoter.com/integrations/tracking/custom/api
operations:
  - POST /signup
  - POST /sale
  - POST /refund
  - POST /cancellation
---

# Track revenue events server-side

Base URL: `https://api.firstpromoter.com/api/v2/track`

Every request needs both credentials:

```
Authorization: Bearer {api_key}
ACCOUNT-ID: {account_id}
```

FirstPromoter deliberately does not accept sale events from the browser — the Tracking API is the
only supported path for money events.

## 1. Attribute the visitor

The browser script (`fpr.js`) sets a `_fprom_tid` cookie when a referred visitor lands. Read that
cookie value on your server and carry it into the signup call as `tid`. Accounts created before
April 2021 use `_fprom_track` instead.

If you already know which promoter a customer belongs to, you may pass `ref_id` instead of `tid`.
Promoters can change their own referral id by default, so `tid` is the safer key.

## 2. Record the signup — `POST /signup`

Body: one of `email` or `uid` is required (the spec enforces this with `oneOf`). Optional
`tid` / `ref_id`, `ip` (used for fraud analysis), `created_at` (ISO 8601), `skip_email_notification`.

Send this when a lead signs up or completes an opt-in form. It creates a referral in the `Leads`
state. It does **not** create a commission.

## 3. Record the sale — `POST /sale`

Required: `event_id` and `amount`.
Optional: `email`, `uid`, `quantity`, `plan`, `currency`, `mrr`, `promo_code`, `tid`, `ref_id`,
`skip_email_notification`.

Call this on **every** non-zero charge, including recurring renewals — FirstPromoter does not infer
renewals for you.

`event_id` is your own unique id for the charge. FirstPromoter has no `Idempotency-Key` header, so
`event_id` is the only replay protection in the API: reuse the same value if you retry a failed
sale call, and never reuse it across different charges.

## 4. Reverse a charge — `POST /refund`

Same shape as the sale call; it generates negative commissions rather than positive ones.

## 5. Close out a customer — `POST /cancellation`

Marks the customer as cancelled and decreases the MRR attributed to them.

## Errors and retries

The tracking operations answer `401` (missing or invalid bearer token) and `403` (wrong user type
or insufficient permissions) as their documented failure modes; validation failures surface as
`400`/`422` with a JSON body. There is no RFC 9457 `application/problem+json` envelope anywhere in
FirstPromoter's specs — see `errors/firstpromoter-problem-types.yml`.

The account-wide limit is 400 requests per minute; over it you get `429`. Back off exponentially
and re-send with the same `event_id`.
