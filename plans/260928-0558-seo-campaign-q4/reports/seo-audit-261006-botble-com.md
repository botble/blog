---
name: SEO audit, botble.com
date: 2026-10-06
scope: technical, on-page, structured data, content hygiene
method: live HTTP checks + database audit of all 7 pages and 43 posts
---

# SEO audit, botble.com

43 posts, 7 pages. Everything below was measured, not assumed.

## 1. Critical: the whole site is duplicated on www

**`https://www.botble.com/` serves the entire site with HTTP 200 and a canonical tag pointing at itself.**

| URL | Status | Canonical it declares |
|---|---|---|
| `www.botble.com/` | 200 | `https://www.botble.com` |
| `www.botble.com/blog` | 200 | `https://www.botble.com/blog` |
| `www.botble.com/laravel-cms-2026` | 200 | `https://www.botble.com/laravel-cms-2026` |
| `www.botble.com/contact` | 200 | `https://www.botble.com/contact` |
| `www.botble.com/about-us` | 200 | `https://www.botble.com/about-us` |

Every page exists twice and each copy tells Google it is the original. This is the same failure as the `/index.php/<slug>` duplicates found on 30/09, except it covers **every URL on the site** rather than a handful.

The `/index.php` version was measurably costing traffic: 10 organic visits landed on the `/index.php` copy of the ecommerce comparison and 0 on the clean URL. There is no reason to think www behaves differently.

**Fix:** one nginx rule, 301 `www.botble.com` to `botble.com`, exactly like the `/index.php` rule added on 30/09. Until then, every link anyone builds to a www URL feeds a copy of the site that should not exist.

## 2. High: every post has two identical H1 tags

**38 of 38 markdown posts.** The theme renders the post title as `<h1 class="post-title">`, and the markdown body opens with `# Title`, which becomes a second `<h1>` with the same text:

```html
<h1 class="post-title text-center">Laravel CMS: The 9 Best Options in 2026, Compared</h1>
...
<h1>Laravel CMS: The 9 Best Options in 2026, Compared</h1>
```

This is a long-standing convention in the content repo, not a recent mistake, and it affects every post including the eight from this quarter's campaign.

**Fix:** drop the leading `# Title` line from each markdown file. The title already comes from front matter. One pass over the repo, then re-import.

## 3. High: /blog has no H1 at all

The listing page renders zero `<h1>` elements. The page now has a good title and description, but no heading.

## 4. High: robots.txt does not reference the sitemap

```
User-agent: *
Disallow:
```

That is all of it. The sitemap exists and is healthy (43 posts, including all eight campaign posts, plus pages, categories and tags), but robots.txt never points at it.

**Fix:** add `Sitemap: https://botble.com/sitemap.xml`.

## 5. High: meta descriptions

| Problem | Count |
|---|---|
| Longer than 165 characters, so truncated in results | **28 posts** |
| No description at all | 4 posts |
| Shorter than 70 characters | 1 post |

The worst are 207 to 276 characters. No duplicates were found, which is the harder problem to fix, so this is purely a trimming job.

Posts with no description: `install-our-cms-in-a-subfolder`, `the-best-way-to-install-our-script-on-a-shared-hosting`, `rename-theme-in-botble-cms`, `how-to-add-pdf-viewer-in-botble-cms`.

## 6. Medium: language signals are wrong for Vietnamese content

The Vietnamese post renders `<html lang="en">` and the site emits **no hreflang anywhere**. Google is told a Vietnamese article is English.

With only two Vietnamese posts this is small, but it caps how well they can rank in Vietnam, which is the market where direct bank-transfer sales keep 100% of the revenue.

## 7. Medium: smaller items

- **18 slugs longer than 60 characters**, up to 83. The importer derives slugs from titles and ignores the `slug:` front-matter key, so every new post needs manual shortening. Already documented in the campaign plan.
- **8 posts under 300 words**, mostly old install guides (102 to 247 words).
- **`cache-control: no-cache, private`** on article pages. Content that changes rarely is being served as uncacheable.
- **No HSTS header.**
- **Duplicate security headers**: `x-content-type-options` and `x-xss-protection` are each sent twice, so nginx and the application are both adding them.
- **`og:type` is `article` on the blog listing**, where `website` is correct.

## What is already healthy

Worth recording, so nobody re-investigates:

- `http://` redirects 301 to `https://`
- `/index.php/<slug>` redirects 301 to the clean URL — the 30/09 fix is holding
- `/blog/` with a trailing slash canonicalises correctly to `/blog`
- 404s return a real 404
- gzip is active: 86,742 bytes uncompressed, 12,812 compressed
- An article page is 26.8 KB gzipped and responds in 0.41s
- The sitemap index covers pages, posts, categories and tags, and all eight campaign posts are in it
- Every post has a featured image
- **Every image on every page checked has alt text** — 66 images on the homepage, none missing
- No duplicate titles and no duplicate meta descriptions across 43 posts
- Structured data is emitted and valid: BreadcrumbList, WebSite, NewsArticle, plus FAQPage on five posts and SoftwareApplication on three product posts
- All 7 CMS pages now have hand-written meta descriptions within length limits

## Suggested order

Ranked by impact divided by effort.

| # | Action | Effort |
|---|---|---|
| 1 | 301 www to non-www in nginx | 10 minutes |
| 2 | Add `Sitemap:` to robots.txt | 2 minutes |
| 3 | Remove the duplicate `# Title` H1 from 38 markdown posts, re-import | 1 hour |
| 4 | Trim 28 over-long meta descriptions, write 4 missing ones | 2 hours |
| 5 | Add an H1 to /blog | theme change |
| 6 | `lang` and hreflang for Vietnamese posts | theme + importer |
| 7 | Cache headers, HSTS, duplicate headers, og:type | nginx + theme |

Items 1 and 2 are the whole first afternoon's value.

## Status, same day

Items 1 to 5 were fixed on 2026-10-06 and verified on the live site.

| # | Item | Status |
|---|---|---|
| 1 | www duplicate | **Fixed.** nginx 301s `www.botble.com` to the non-www URL, path preserved. Verified on 4 URLs; the 5 other subdomains are unaffected |
| 2 | robots.txt sitemap | **Fixed.** `Sitemap: https://botble.com/sitemap.xml` added |
| 3 | Duplicate H1 | **Fixed.** Removed from 37 markdown files and resynced. **43 of 43 live posts now have exactly one `<h1>`** |
| 4 | Meta descriptions | **Fixed.** 29 rewritten in markdown, 4 written directly for posts that predate the repo. **43 of 43 live posts are now between 1 and 165 characters** |
| 5 | Page descriptions | **Fixed earlier the same day.** All 7 CMS pages |

Still open: /blog has no H1, `lang`/hreflang for Vietnamese, long slugs, thin posts, cache headers, HSTS, duplicate security headers, `og:type` on the listing.

### Found while fixing

**Cloudflare sits in front of the whole site.** `cf-cache-status: HIT`, `cache-control: max-age=14400`. This explains why updated pages appeared stale until a cache-busting query string was added — it was never an application bug.

The `CLOUDFLARE_API_TOKEN` in the local env can read zones but **lacks the Cache Purge permission**, so the stale robots.txt had to expire on its own. Granting that permission would let every future publish go live immediately instead of waiting up to four hours.

**The H1 removal needed three passes.** The heading sat in different places across the repo: straight after the front matter, after the hero image, or worded as a shortened version of the title. Each pass required the heading to restate the front-matter title before deleting it, so a real section heading could not be lost. Two files needed naming explicitly.

## Open questions

1. Is www intentionally served, for an old link profile? If so it still needs the 301; the canonical must not be self-referencing either way.
2. Should the 8 thin install guides be expanded, merged into one setup page, or left alone? They are old support content, not ranking targets.
3. Does anything depend on article pages being uncacheable, or can they get a short `max-age`?
| 6 | `/blog` had no H1 | **Fixed.** The breadcrumb title is now the `h1` on the blog index, and the page's SEO description renders as a lead paragraph under it |
| 7 | Vietnamese posts declared `lang="en"` | **Fixed.** The two Vietnamese posts serve `<html lang="vi">`; English posts are unchanged |
| 8 | `og:type=article` on everything | **Fixed.** Pages and all archives now send `website`; only posts send `article` |
| 9 | Duplicate security headers | **Fixed.** nginx was adding `X-Content-Type-Options` and `X-XSS-Protection` that Laravel already sends. Removed from nginx |
| 10 | CMS version leaked on every response | **Fixed.** `cms-version`, `authorization-at` and `activated-license` are now hidden with `fastcgi_hide_header` |

### Found while fixing the above

| Item | Status |
|---|---|
| Category, tag and search archives had **no H1 at all** — their only heading was the breadcrumb `h2` | **Fixed.** The breadcrumb heading tag is now chosen by the view, so archives get the `h1` |
| `/about-us`, `/contact`, `/privacy-policy`, `/terms-of-service`, `/cookie-policy` had **no H1 at all**, same cause | **Fixed** by the same change |
| None of the 14 category archives had a title or description of their own, so each went to Google as a bare word (`Laravel`, `Ecommerce`) | **Fixed.** All 14 now have a hand-written title (14–39 chars) and description (114–152 chars) |
| `pages.xml` looked like it contained a single URL | **Not a defect.** `rtk` was truncating piped `curl` output. It has all 9. Read sitemaps from a saved file, not a pipe |

Verified after deploy on `/blog`, 4 category archives, `/tag/laravel`, `/search`, all 7 CMS pages, the homepage, `/laravel-cms`, `/customize` and 2 posts: **exactly one `h1` each, `og:type` correct on each.**

Note on verifying deploys: opcache and the view cache take roughly **25 seconds** to pick up a theme change. Checking immediately after `cmd_deploy` reports the old markup — poll until it flips rather than concluding the fix failed.

## Still open

Each of these needs a decision rather than a fix.

| Item | Detail | Recommendation |
|---|---|---|
| **86 thin tag archives** | 119 tags: 3 have no published post at all, 83 have exactly one, so **72% are one-post pages** that duplicate the post they list. `/laravel` (category) and `/tag/laravel` also overlap | `noindex, follow` on tag archives and drop them from the sitemap, keeping the 14 categories indexable. Removes 119 URLs from the index, so it is a call to make deliberately. Delete the 3 empty tags outright |
| **HSTS** | Not sent. The site is HTTPS-only behind Cloudflare already | `max-age=31536000`, no `includeSubDomains`, no `preload`. Reversible by sending `max-age=0`, but only as visitors return, so worth deciding rather than assuming |
| **`cache-control: no-cache, private` on HTML** | Cloudflare therefore never caches a page; every hit reaches PHP. Botble sets this because a page can carry a logged-in state | A Cloudflare cache rule on blog paths with a bypass-on-cookie, rather than changing the app header |
| **Cloudflare API token is read-only** | `CLOUDFLARE_API_TOKEN` has no `Cache Purge`, so published changes can sit behind a stale edge copy. Does not affect articles, which are `no-cache` | Grant `Cache Purge` if the cache rule above goes in |
| **18 long post slugs** | On already-indexed posts. Shortening needs 301s and risks the rankings they have | Leave. Use shorter slugs for new posts only |
| **8 thin posts** | Short enough to compete poorly | Expand as content work, not as an SEO fix |
| **`og:type` fix is local to this site** | The cause is upstream: `PageService` types every page as `article`, `BlogService` does the same for archives. Fixed here in the theme | Worth the same fix in Botble core so every customer gets it |
