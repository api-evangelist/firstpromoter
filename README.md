# FirstPromoter (firstpromoter)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
