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
| 0 | Fix wrong specs in the existing SaaS post | — | — | ✅ done 28/09 |
| 1 | Build your own Shopify alternative | shopify alternative self hosted | Ecommerce SaaS | ✅ [published 28/09](https://botble.com/build-your-own-shopify-alternative-a-self-hosted-multi-tenant-store-platform-on-laravel) |
| 2 | Add Ecommerce SaaS to `best-laravel-ecommerce-scripts-2026` | (refresh, already ranks) | Ecommerce SaaS | ✅ done 28/09, together with its voice pass |
| 3 | Laravel multi-tenancy: database per tenant vs single database | laravel multi tenancy database per tenant | Ecommerce SaaS | ✅ [published 28/09](https://botble.com/laravel-multi-tenancy-database-per-tenant-vs-one-shared-database) |
| 4 | Car rental booking software, 9 self-hosted options | laravel car rental script | Carento + Carento Mobile | ✅ [published 01/10](https://botble.com/car-rental-booking-software-2026) |
| 5 | Laravel real estate scripts, 8 self-hosted options | laravel real estate script | Homzen, Flex Home | ✅ [published 02/10](https://botble.com/laravel-real-estate-script-2026) |
| 6 | Laravel helpdesk scripts, self-hosted support desks | laravel support ticket system | DeskHive | ✅ [published 05/10](https://botble.com/laravel-helpdesk-script-2026) |
| 7 | Botble License Manager: a self-hosted license server | botble license manager, license server | License Manager | ✅ [published 05/10](https://botble.com/botble-license-manager-license-server) |
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

## Voice — write like a person, not a content mill

Sang's note, 28/09: the first draft read like AI. It was clean, symmetrical and lifeless. Rules:

- **First person.** "I'd rather pay for a database per tenant" beats "the per-database approach is
  preferable". You are a developer who ships this stuff, so sound like one.
- **Use contractions.** isn't, you'll, we've. Formal register is the biggest tell.
- **Break the symmetry.** AI writes lists where every item is the same length and shape, and pairs
  everything ("not X, but Y"). Let some sections be a paragraph and others a bullet. Vary sentence
  length hard: a long one, then four words.
- **Ration the em dashes.** One or two per post. Commas and full stops do the same job.
- **Have opinions and say the unpleasant part.** "Where it will annoy you" is a better heading than
  "Limitations", and it earns trust that no feature table earns.
- **Anchor in real experience** — our own bank-transfer customers, the merchant who emails at 11pm.
  Real detail is the thing a generator cannot fake.
- **Cut the throat-clearing.** No "In today's fast-paced world", no "It's important to note that",
  no closing paragraph that summarises what the reader just read.
- **Keep internal financials out.** Order counts and revenue inform *which* post to write, they do
  not go in the post.

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
- **Publish** (VPS `root@108.160.138.161:2504`, site at `/home/nginx/domains/botble.com`):
  1. Upload the hero through the **media manager** at `https://botble.com/homeadm/media`, inside the `news` folder — that is what creates the media record and the `-150x150` / `-370x230` thumbnails. Do this first.
  2. The import command uses `file_exists()`, so it **cannot take a URL**: `curl` the raw GitHub markdown to `/tmp` on the server.
  3. `sudo -u nginx php artisan cms:blog:create-post-from-markdown /tmp/post.md --no-interaction` — it prints the live URL, whose slug comes from the title, not the filename.

⚠️ **The command cannot update a post whose title contains `&`.** It matches on the raw `posts.name`
column, which stores `&amp;`, while the markdown title carries a literal `&` — no match, so it
silently creates a **duplicate** with the same slug (hit on `best-laravel-ecommerce-scripts-2026`,
28/09; the duplicate was force-deleted). For those posts, update in place instead:

```php
$service = app(\Botble\MarkdownBlog\Services\MarkdownPostService::class);
$parsed = $service->parseMarkdownFile('/tmp/post.md');
$post = \Botble\Blog\Models\Post::find(<id>);
$service->updatePost($post, $parsed['front_matter'], $parsed['content']);
$service->syncCategories($post, $parsed['front_matter']['categories'] ?? []);
$service->syncTags($post, $parsed['front_matter']['tags'] ?? []);
```

Run it as `sudo -u nginx env HOME=/tmp php artisan tinker /tmp/script.php` (psysh cannot write to
the nginx user's home). The real fix belongs in the plugin: match on the decoded name, or on slug.

## Success criteria

- [ ] Each post published with a hero image and correct front matter
- [ ] Every post links to a direct purchase page and to ≥2 existing posts
- [ ] No unverified spec numbers
- [ ] Phase 0 corrections live before post #1 publishes

## Out of scope

- Paid promotion, newsletters, social posts (the Acelle campaign is a separate plan).
- Rewriting the 28 product-introduction posts.

## Decided 28/09

- **Vietnamese posts go on the same blog**, mixed in with the English ones. No separate destination.
- **The voice pass on the 10 AI-ish posts runs alongside the new posts**, starting with the two
  comparison posts because they rank and the rest of the blog links to them.

## Voice pass — the 10 worst (em dashes per 1k words vs human markers per 1k)

| Post | em/1k | human/1k | Status |
|---|---:|---:|---|
| best-laravel-multivendor-marketplace-scripts-2026 | 28.4 | 4.5 | ✅ 28/09 |
| travlla-introduction | 23.2 | 0.0 | ✅ 28/09 |
| snapcart-introduction | 23.0 | 0.9 | ✅ 28/09 |
| license-manager-introduction | 21.4 | 2.6 | ✅ 28/09 |
| amerce-now-available-on-codester | 21.0 | 1.9 | ✅ 28/09 |
| deskhive-introduction | 20.0 | 1.1 | ✅ 28/09 |
| amerce-introduction | 16.6 | 1.7 | ✅ 28/09 |
| claude-code-skills-for-botble-cms | 15.8 | 3.2 | ✅ 28/09 |
| best-laravel-ecommerce-scripts-2026 | 14.8 → **0.6** | 8.6 | ✅ 28/09 |
| live-chat-introduction | 14.7 | 2.3 | ✅ 28/09 |

Reference: post #1 after its rewrite sits at 2.9 / 11.7.

**Done 28/09.** All ten rewritten: 310 em dashes removed across twelve files (the three extra were
filler-word cleanups), brochure openings replaced, and every post re-imported. Two discoveries on
the way: the importer duplicates posts whose title contains `&` (fixed in the plugin, pushed to
`botble/botble.com`), and the DeskHive post had never actually been published — its markdown sat in
this repo unused, so it went live for the first time.
