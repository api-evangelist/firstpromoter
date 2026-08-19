---
name: firstpromoter-onboard-a-promoter
description: >-
  Create an affiliate in FirstPromoter, accept them onto a campaign, add them to further campaigns,
  and confirm the result — including the asynchronous batch behaviour that bulk actions trigger.
api: firstpromoter:firstpromoter-v2-promoters
generated: '2026-08-13'
method: generated
source: openapi/firstpromoter-v2-promoters-openapi.yml + openapi/firstpromoter-v2-batches-openapi.yml
operations:
  - createPromoter
  - getPromoter
  - acceptPromoters
  - addPromotersToCampaign
  - movePromotersToCampaign
  - updatePromoterCampaign
  - listBatchProcesses
  - getBatchProcessProgress
---

# Onboard a promoter

Base URL: `https://api.firstpromoter.com/api/v2/company`

```
Authorization: Bearer {api_key}
ACCOUNT-ID: {account_id}
```

## 1. Create the promoter — `createPromoter` (`POST /promoters`)

Creates the affiliate and places them on an initial campaign. Response carries the promoter `id`,
which every later call keys on.

## 2. Accept them — `acceptPromoters` (`POST /promoters/accept`)

Acceptance is scoped to a campaign, not to the promoter globally. The same batch shape backs
`rejectPromoters`, `blockPromoters`, `archivePromoters` and `restorePromoters`.

**This is where integrations go wrong.** These operations take an `ids` array. If you send more
than **5** ids, FirstPromoter processes the action asynchronously and returns a batch whose status
is most likely `in_progress` — a `200` here does not mean the state changed. Statuses are
`pending`, `in_progress`, `completed`, `failed`.

## 3. Confirm an async action — `getBatchProcessProgress` (`GET /batch_processes/progress`)

Poll progress, or fetch a specific batch with `getBatchProcess` (`GET /batch_processes/{id}`), or
list everything still running with `listBatchProcesses` (`GET /batch_processes`).

For 5 ids or fewer the change is applied synchronously and you can skip this step. Write your
integration to branch on the response, not on your own batch size assumption.

## 4. Verify — `getPromoter` (`GET /promoters/{id}`)

Returns the profile, balances and campaign membership. Read the per-campaign state back rather than
trusting the accept call; FirstPromoter's own MCP tooling does exactly this verification step.

## 5. Extend or move campaign membership

- `addPromotersToCampaign` (`POST /promoters/add_to_campaign`) — enrol in an extra campaign,
  leaving existing memberships intact.
- `movePromotersToCampaign` (`POST /promoters/move_to_campaign`) — move between campaigns;
  existing referrals can be soft-moved with them.
- `updatePromoterCampaign` (`PUT /promoter_campaigns/{id}`) — per-promoter overrides on one
  campaign: ref token, coupon, display coupon, direct URL, state, reward override.
- `POST /promoters/assign_parent` — set or clear the parent in a sub-affiliate hierarchy. Note that
  the published spec gives this operation the operationId `acceptPromoters`, duplicating
  `POST /promoters/accept`; bind by path, not by operationId.

## Pagination and errors

List operations use `page` and `per_page`. Failures are `401` (auth), `403` (permissions), `404`
(not found) and `422` (invalid parameters) with a JSON body — no `application/problem+json`.
