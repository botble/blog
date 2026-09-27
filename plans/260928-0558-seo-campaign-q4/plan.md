---
name: SEO content campaign — 7 posts + 1 refresh
date: 2026-09-28
status: in progress
---

# SEO campaign, Q4 2026

31 posts exist. 28 are product introductions; only 3 are the kind that earn search traffic
(`best-laravel-ecommerce-scripts-2026`, `best-laravel-multivendor-marketplace-scripts-2026`,
`top-5-free-botble-cms-plugins-2025-2026`). Those three also carry most of the internal links —
the ecommerce comparison alone is linked from 10 other posts.

This campaign adds posts that answer what buyers actually type into Google, and points them at the
products that are selling.

## What the sales data says (Sep 2026)

| Signal | Number | What it means for content |
|---|---|---|
| Ecommerce SaaS, first 10 days | 11 orders, $395 net — 2nd best direct item | New, high-priced, only has an intro post. Biggest gap |
| Direct vs marketplace, per order | direct earns **1.55×** | Every post should send readers to the direct store, not the marketplace |
| Vietnamese buyers | 3 bank transfers in Sep | Bank transfer keeps **100%**; Lemon Squeezy costs ~7–8%. Vietnamese posts are the highest-margin traffic |
| DeskHive | first ever direct order, 26/09 | Product is invisible. One post is a cheap fix |
| License Manager | lost its homepage promo slot 24/09 | Needs a search entry point to replace it |

## Order of work

| # | Post | Target keyword | Sells | Status |
|---|------|----------------|-------|--------|
| 0 | Fix wrong specs in the existing SaaS post | — | — | pending |
| 1 | [Build your own Shopify alternative](briefs/01-shopify-alternative.md) | shopify alternative self hosted | Ecommerce SaaS | pending |
| 2 | Add Ecommerce SaaS to `best-laravel-ecommerce-scripts-2026` | (refresh, already ranks) | Ecommerce SaaS | pending |
| 3 | Laravel multi-tenancy: database per tenant vs single database | laravel multi tenancy database per tenant | Ecommerce SaaS | pending |
| 4 | Best Laravel car rental scripts 2026 | laravel car rental script | Carento + Carento Mobile | pending |
| 5 | Best Laravel real estate scripts 2026 | laravel real estate script | Homzen, Flex Home | pending |
| 6 | Laravel helpdesk & support ticket scripts | laravel support ticket system | DeskHive | pending |
| 7 | Add licensing and auto-updates to your Laravel app | laravel license key system | License Manager | pending |
| 8 | Vietnamese version of #1 | website bán hàng đa gian hàng | Ecommerce SaaS | pending |

One post at a time, each published before the next is started, so the first results inform the rest.

## Phase 0 — fix the existing SaaS post first (blocking)

`ecommerce-saas-multi-tenant-store-platform-introduction.md` repeats claims that were fact-checked
against the product source on 24/09 and found wrong. Everything in this campaign links to that post,
so fix it before adding links:

| Claim in the post | Reality (`~/workspace/ecommerce-saas`) |
|---|---|
| "REST API with 38 endpoints" (title description + body) | **39** routes in `platform/packages/tenancy/routes/api.php` |
| "43 languages" for storefronts | 43 = operator console; the Amerce storefront ships **23** |
| "in about a minute" provisioning | docs say inline provisioning is **~1–2s** (`TENANCY_PROVISION_SYNC=true`); queued is the slower path |

27 webhook events and 20 designs are correct.

## House rules for every post

- **Front matter** per `README.md`: title, description (the SEO snippet — write it last, make it a
  promise), categories, tags, `image: https://botble.com/storage/news/<slug>-hero.jpg`, status.
- **Hero image**: build an HTML mock, screenshot it, save as `images/<slug>-hero.jpg` plus the
  `.source.html` next to it — that is the existing pattern in `images/`.
- **Links out**: every post links to the product's **direct** page (`marketplace.botble.com/...` or
  the landing), never only to CodeCanyon. Mention the 30% direct discount once, not in every section.
- **Links in**: link at least two existing posts, and add a link back from the ecommerce comparison
  post when the topic overlaps — that post is the hub.
- **No invented specs.** Numbers come from the product source or the docs, not from memory. Phase 0
  exists because that rule was broken once already.
- **Length**: comparison posts 2,500–4,000 words (that is what the two ranking ones do); technical
  and FAQ posts 900–1,500.
- **Publish**: `php artisan cms:blog:create-post-from-markdown path/to/post.md` from the site repo.

## Success criteria

- [ ] Each post published with a hero image and correct front matter
- [ ] Every post links to a direct purchase page and to ≥2 existing posts
- [ ] No unverified spec numbers
- [ ] Phase 0 corrections live before post #1 publishes

## Out of scope

- Paid promotion, newsletters, social posts (the Acelle campaign is a separate plan).
- Rewriting the 28 product-introduction posts.

## Open questions

1. Who publishes — do I run `cms:blog:create-post-from-markdown` against production, or hand over the
   markdown for you to publish?
2. Vietnamese posts: keep them on `botble.com/blog` mixed with English, or is there a separate
   destination?
