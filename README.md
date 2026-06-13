# FirstPromoter (firstpromoter)

FirstPromoter is an all-in-one affiliate and referral tracking platform built for SaaS and subscription-based companies. It provides a REST API for managing promoters, tracking referrals, running campaigns, and automating reward distribution. The platform is Stripe-native and supports percentage of sale, flat fee, one-time, lifetime, and tiered commission models with multi-currency support across 190 currencies.

APIs.json: https://raw.githubusercontent.com/api-evangelist/firstpromoter/refs/heads/main/apis.yml

Naftiko: https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=firstpromoter-api-evangelist&utm_content=repo

## Tags

- Affiliate Marketing
- Referral Tracking
- SaaS
- Commission Management
- Reward Distribution
- Promoters

## APIs

### FirstPromoter Admin API

REST API v2 for managing affiliate programs, promoters, campaigns, referrals, rewards, and payouts. Base URL: `https://api.firstpromoter.com/api/v2`. Authentication uses Bearer token plus Account-ID header.

- Documentation: https://docs.firstpromoter.com/api-reference-v2/api-admin/introduction
- GitHub Org: https://github.com/firstpromoter

## Plans, Rate Limits, and FinOps

- **Plans/Pricing**: [plans/firstpromoter-plans-pricing.yml](plans/firstpromoter-plans-pricing.yml) — Three tiers: Starter ($49/month), Business ($99/month), Enterprise ($149/month). All plans include a 14-day free trial and 0% transaction fees.
- **Rate Limits**: [rate-limits/firstpromoter-rate-limits.yml](rate-limits/firstpromoter-rate-limits.yml) — 400 requests per minute per account; HTTP 429 returned on excess; pagination capped at 100 items per page.
- **FinOps**: [finops/firstpromoter-finops.yml](finops/firstpromoter-finops.yml) — FOCUS-aligned subscription cost model covering all three SKUs with no overage or transaction fees.

## Timestamps

- Created: 2026-06-13
- Modified: 2026-06-13

## Common

| Type | URL |
|------|-----|
| Website | https://firstpromoter.com/ |
| Documentation | https://docs.firstpromoter.com/ |
| GitHub Org | https://github.com/firstpromoter |
| LinkedIn | https://www.linkedin.com/company/first-promoter |
| Blog | https://firstpromoter.com/blog |
| Pricing | https://firstpromoter.com/pricing |
| X | https://x.com/firstpromoter |

## Maintainers

- **Kin Lane** — kin@apievangelist.com
