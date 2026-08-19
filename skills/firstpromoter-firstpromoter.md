---
name: Firstpromoter
description: Use when building, configuring, or managing affiliate and referral programs for SaaS and subscription businesses. Reach for this skill when integrating tracking scripts, managing promoters/affiliates, configuring billing provider connections, tracking referrals and sales, managing commissions and payouts, or automating affiliate workflows via API or webhooks.
metadata:
    mintlify-proj: firstpromoter
    version: "1.0"
---

# FirstPromoter Skill

## Product summary

FirstPromoter is an affiliate and referral program platform for SaaS and subscription businesses. It tracks clicks, referrals, and sales; manages promoters (affiliates); calculates and approves commissions; and handles payouts. Agents use FirstPromoter to integrate tracking into websites, manage affiliate campaigns, configure billing provider connections, and automate referral workflows.

**Key files and config:**
- Account ID: Found in Settings → Integrations (required for all API calls and script setup)
- API Key: Required for API authentication (v2 Admin API)
- Main tracking script: `fpr.js` loaded from `https://cdn.firstpromoter.com/fpr.js`
- Tracking cookies: `_fprom_tid` (tracking ID), `_fprom_ref` (referral ID)

**Primary docs:** https://docs.firstpromoter.com

---

## When to use

Reach for FirstPromoter when:

- **Setting up tracking** — Adding click, referral, or sales tracking to a website (custom, WordPress, platform-specific)
- **Managing promoters** — Creating, updating, accepting, rejecting, or moving affiliates between campaigns via API
- **Configuring billing integrations** — Connecting Stripe, Chargebee, Paddle, Recurly, or Braintree for automatic sales tracking
- **Tracking referrals server-side** — Using the Tracking API to record signups, sales, refunds, or cancellations without JavaScript
- **Managing commissions** — Approving, denying, or updating commissions; marking non-monetary rewards as fulfilled
- **Handling payouts** — Retrieving payout data, generating payouts, or updating payout status
- **Automating workflows** — Setting up webhooks to listen for commission or referral events and trigger downstream actions
- **Managing promo codes** — Creating, updating, or archiving coupon codes tied to promoters or campaigns
- **Reporting and analytics** — Querying campaign, promoter, referral, or traffic source reports

---

## Quick reference

### Essential API endpoints (v2)

| Task | Endpoint | Method |
|------|----------|--------|
| Track signup | `POST /api/v2/track/signup` | POST |
| Track sale | `POST /api/v2/track/sale` | POST |
| Track refund | `POST /api/v2/track/refund` | POST |
| Track cancellation | `POST /api/v2/track/cancellation` | POST |
| Create promoter | `POST /company/promoters` | POST |
| Get promoters | `GET /company/promoters` | GET |
| Update promoter | `PUT /company/promoters/{id}` | PUT |
| Get referrals | `GET /company/referrals` | GET |
| Update referral | `PUT /company/referrals/{id}` | PUT |
| Get commissions | `GET /company/commissions` | GET |
| Approve commissions | `POST /company/commissions/approve` | POST |
| Get payouts | `GET /company/payouts` | GET |
| Get promo codes | `GET /company/promo_codes` | GET |
| Create promo code | `POST /company/promo_codes` | POST |

### fpr.js script methods

| Method | Purpose |
|--------|---------|
| `fpr("init", {cid: "ACCOUNT_ID"})` | Initialize with account ID |
| `fpr("click")` | Track click on referral link |
| `fpr("referral", {email: "...", uid: "..."})` | Track signup/referral |
| `fpr("details", callback)` | Get promoter/campaign details |
| `fpr("onReady", callback)` | Check if script is loaded |
| `fpr("crossDomain", domains)` | Track across multiple domains |
| `fpr("urlTracking")` | Enable direct URL tracking |

### Required headers for API calls

```
Account-ID: YOUR_ACCOUNT_ID
Authorization: Bearer YOUR_API_KEY
Content-Type: application/json
```

### Key request/response fields

**Tracking API (signup/sale):**
- `email` or `uid` — identify the referral (required)
- `tid` or `ref_id` — link to promoter (required if email/uid not in system)
- `sale_amount` — in cents (e.g., 4900 = $49.00)
- `event_id` — deduplication key (required for sales)
- `currency` — if different from account default

**Promoter creation:**
- `email` — promoter email (required)
- `initial_campaign_id` — campaign to add to
- `always_approve` — auto-accept or require manual approval
- `drip_emails` — send welcome email

**Referral update:**
- `fraud_check` — set to `no_suspicion`, `same_ip_suspicion`, `same_promoter_email`, or `ad_source`
- `split_details` — array of split partners and percentages

---

## Decision guidance

### When to use script-based vs API-based tracking

| Scenario | Use fpr.js | Use Tracking API |
|----------|-----------|------------------|
| Client-side click/signup tracking | ✓ | — |
| Server-side signup tracking | — | ✓ |
| Custom payment flow (non-Stripe) | — | ✓ |
| Mobile app referral tracking | — | ✓ |
| Webhook-driven sales from billing provider | — | ✓ |
| WordPress/platform integration | ✓ | Optional |

### When to use billing provider integration vs API

| Scenario | Billing Integration | Tracking API |
|----------|-------------------|--------------|
| Stripe, Chargebee, Paddle, Recurly, Braintree | ✓ | — |
| Custom or unsupported billing provider | — | ✓ |
| Need to manually track sales | — | ✓ |
| Webhook-based sales from your backend | — | ✓ |

### Referral identification: email vs uid vs tid vs ref_id

| Method | When to use | Notes |
|--------|-----------|-------|
| `email` | Referral email known | Simple, human-readable |
| `uid` | Customer ID from your system | Survives email changes; must match billing provider customer ID |
| `tid` | Visitor came via referral link | Captured in `_fprom_tid` cookie; best for click→signup flow |
| `ref_id` | Direct promoter assignment | Use sparingly; can be modified by promoter unless disabled |

---

## Workflow

### 1. Set up basic tracking on a custom website

1. **Get Account ID** — Navigate to Settings → Integrations in FirstPromoter dashboard; copy Account ID.
2. **Add main tracking script** — Insert the fpr.js init and click code into the `<head>` of every public page:
   ```html
   <script>
     (function(w){w.fpr=w.fpr||function(){w.fpr.q=w.fpr.q||[];w.fpr.q[arguments[0]=='set'?'unshift':'push'](arguments);};})(window);
     fpr("init", {cid:"YOUR_ACCOUNT_ID"});
     fpr("click");
   </script>
   <script src="https://cdn.firstpromoter.com/fpr.js" async></script>
   ```
3. **Add referral tracking** — On signup success page or callback, call:
   ```javascript
   fpr("referral", {email: "user@example.com", uid: "cus_12345"});
   ```
4. **Connect billing provider** — Go to Settings → Integrations and connect Stripe, Chargebee, etc. (if supported).
5. **Test** — Copy an affiliate's referral link, visit it, sign up, and verify click/referral/sale appear in dashboard.

### 2. Create and manage promoters via API

1. **Create promoter** — POST to `/company/promoters` with email, campaign ID, and optional profile data.
2. **Verify response** — Check for `auth_token` (used for auto-login) and `promoter_campaigns` array.
3. **Add to campaign** — If not added on creation, POST to `/company/promoters/add_to_campaign`.
4. **Update promoter** — PUT to `/company/promoters/{id}` to change profile, note, or status.
5. **Move between campaigns** — POST to `/company/promoters/move_to_campaign` to reassign.

### 3. Track sales server-side (custom billing provider)

1. **Receive webhook** — Set up endpoint to receive sale events from your billing provider.
2. **Extract data** — Get customer email/uid, sale amount (in cents), order ID, and currency.
3. **Call Tracking API** — POST to `/api/v2/track/sale` with email/uid, amount, event_id, and currency.
4. **Handle response** — 200 = commission created; 204 = not a referral; 409 = duplicate event_id.
5. **Track refunds/cancellations** — Call `/api/v2/track/refund` or `/api/v2/track/cancellation` when applicable.

### 4. Manage commissions and payouts

1. **List commissions** — GET `/company/commissions` with filters (status, campaign, promoter).
2. **Approve commissions** — POST to `/company/commissions/approve` with array of commission IDs.
3. **Deny commissions** — POST to `/company/commissions/deny` with reason.
4. **Get payouts** — GET `/company/payouts` to list pending, completed, or failed payouts.
5. **Update payout status** — PUT to `/company/payouts/{id}` to mark as completed after payment.

### 5. Set up webhooks for automation

1. **Navigate to webhooks** — Settings → Integrations → Webhooks.
2. **Create subscription** — Click "Add new webhook" and enter:
   - HTTPS endpoint URL
   - Event types (e.g., `commission.created`, `commission.approved`)
   - Campaign IDs (optional filter)
   - Custom headers (optional)
3. **Handle payload** — Your endpoint receives POST with event data; respond with 2xx to acknowledge.
4. **Retry logic** — Failed deliveries retry automatically; check delivery history in dashboard.
5. **Test** — Use "Test webhook" button to send sample payload.

---

## Common gotchas

- **Account ID required everywhere** — Every API call and script needs the Account ID. It's not the same as API key. Find it in Settings → Integrations.

- **sale_amount in cents** — Always send amounts in cents (e.g., 4900 for $49.00). For zero-decimal currencies like JPY, send whole values.

- **event_id deduplication** — Always pass a unique `event_id` when tracking sales. Duplicate event_ids are rejected with 409 Conflict. Use order ID or transaction ID.

- **tid vs ref_id confusion** — `tid` is the tracking ID from the `_fprom_tid` cookie (set when visitor clicks referral link). `ref_id` is the promoter's referral ID. Don't mix them up.

- **Referral tracking timing** — Call `fpr("referral", ...)` **after** the user successfully signs up, not before. If you redirect immediately, the request may be cancelled. Place it on the thank-you page instead.

- **uid must match billing provider** — If using Stripe, the `uid` must match the Stripe customer ID (or be set in customer metadata as `fp_uid`). Mismatches break sales attribution.

- **Fraud suspicions block commissions** — Referrals flagged for fraud (same IP, same email as promoter, ad source) may have commissions denied. Review and clear manually via `fraud_check` parameter.

- **Batch operations async** — API calls with >5 items (e.g., moving 10 promoters) return 202 Accepted and process asynchronously. Check batch status with the returned batch ID.

- **Webhook timeout 30s** — Webhooks timeout after 30 seconds. Keep your endpoint fast or queue the work asynchronously.

- **Cookies cleared by ad-blockers** — `_fprom_tid` and `_fprom_ref` cookies may be blocked. Use proxy setup or API-only tracking as fallback.

- **Campaign-specific promo codes** — Promo codes are tied to a promoter_campaign (promoter + campaign pair), not just a campaign. Ensure you're using the correct promoter_campaign_id.

- **Non-monetary commissions need fulfillment** — For rewards like points or free months, call `mark_fulfilled` after delivering the reward in your system.

---

## Verification checklist

Before submitting work:

- [ ] Account ID is correct and included in all API calls and scripts
- [ ] API key is valid and has appropriate permissions
- [ ] Tracking script is in `<head>` of all public pages (not just homepage)
- [ ] `fpr("referral", ...)` is called **after** signup success, not before
- [ ] Sale amounts are in cents (multiply by 100 if needed)
- [ ] `event_id` is unique per sale (no duplicates)
- [ ] `uid` matches billing provider customer ID (if using Stripe, Chargebee, etc.)
- [ ] Webhook endpoint is HTTPS and responds with 2xx within 30 seconds
- [ ] Webhook event types match the events you want to receive
- [ ] Promoter creation includes `initial_campaign_id` or promoter is added to campaign separately
- [ ] Fraud flags are reviewed and cleared if legitimate referrals are blocked
- [ ] Batch operations (>5 items) are monitored for completion status
- [ ] Promo codes are tied to correct promoter_campaign_id
- [ ] Non-monetary commissions are marked fulfilled after delivery
- [ ] Test with a real referral link → signup → sale flow before going live

---

## Resources

**Comprehensive navigation:** https://docs.firstpromoter.com/llms.txt

**Critical pages:**
- [Integrate Your Website](/guides/integrate-your-website) — Step-by-step setup for clicks, referrals, and sales tracking
- [API Admin Reference](/api-reference-v2/api-admin/introduction) — Full API documentation with authentication and rate limits
- [Webhooks v2 Overview](/webhooks-v2/overview) — Event subscriptions, delivery, and retry logic

---

> For additional documentation and navigation, see: https://docs.firstpromoter.com/llms.txt