---
name: Traffic read, 10 days of nginx logs (21–30 Sep 2026)
date: 2026-09-30
source: /home/nginx/domains/botble.com/log/access.log* (bot-filtered by user agent)
---

# What the logs say

Two days after the first campaign posts went live. Too early to judge them, early enough to find
something worth fixing.

## The blog's actual traffic

| Page | Humans | Unique IPs | From search |
|---|---:|---:|---:|
| `/laravel-cms-2026` | 202 | 130 | **45** |
| `/ecommerce-saas-run-your-own-store-hosting-platform-on-botble-cms` | 97 | 69 | 5 |
| `/best-laravel-ecommerce-scripts-in-2026-...` | 38 | 35 | 0 (see below) |
| `/best-laravel-multi-vendor-marketplace-scripts-2026-top-6` | 36 | 33 | 0 |
| **post #3** multi-tenancy (28/09) | 23 | 7 | 1 |
| **post #1** Shopify alternative (28/09) | 14 | 10 | 1 |
| DeskHive (first publish 28/09) | 6 | 6 | 1 |

Referrers across the whole site: Google 336, everything else in single digits (DuckDuckGo 6, Bing 5,
ChatGPT 4). Search is the channel; nothing else is close.

For comparison, product pages beat every blog post: `/products/license-manager` 383, the SaaS product
page 362, `/products/botble-ecommerce` 325.

## Finding 1: one page carries the blog

`/laravel-cms-2026` pulls **45 of the site's organic visits**. Every other post is in single digits.
It is a broad "what is a Laravel CMS" piece, not a product post and not a comparison. That is the
shape that ranks for us today.

## Finding 2: `/index.php/` duplicates are eating the comparison posts

`/index.php/<slug>` serves the same content with **a canonical tag pointing at itself**:

```
/index.php/best-laravel-ecommerce-scripts-...  → canonical: https://botble.com/index.php/best-...
```

So Google treats it as a separate page. It is not hypothetical: **10 organic visits landed on the
`/index.php` copy** of the ecommerce comparison, and 0 on the clean URL. The ranking signal for that
post is sitting on the wrong URL. 884 requests to `/index.php/...` in 10 days, across at least 8
distinct paths (`/blog`, `/laravel-cms-2026`, both comparisons, the AI assistant guide).

Fix is one nginx rule: 301 `/index.php/(.*)` to `/$1`.

## Finding 3: the new posts are simply too young

14 and 23 human visits, most of them on publish day. Nothing is wrong; Google has not finished
crawling them. Judge at 2–4 weeks, not now.

The internal linking is already working: the top organic page links to both new posts through its
related-posts block.

## What to do with the next post

1. **Fix the `/index.php` duplicate first.** It costs one nginx rule and affects every post,
   including the ones already written.
2. **Broad-question posts outrank comparison posts here.** Post #4 (car rental scripts) should not
   be only "best X scripts" — lead with the question an operator actually types, and let the
   comparison sit inside it.
3. **Submit the new URLs in Search Console** so crawling isn't left to chance. Needs Sang's account.
4. **Link new posts from `/laravel-cms-2026`**, on purpose, not just through the related-posts
   widget: it is the only page with organic authority to pass.

## Open questions

1. Is there Google Analytics or Search Console on this domain? The logs cannot show queries, time on
   page or scroll depth, and Search Console would answer "which query, which position".
2. `/contact` shows 800 hits from 268 IPs in 10 days, more than any blog post. Worth a look — that
   smells like form spam rather than readers.
