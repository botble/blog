---
title: "Speeding Up Botble CMS: What We Learned Tuning 112 Demo Sites"
description: "Bots, OPcache, JIT, nginx full-page caching and Cloudflare. What made the biggest difference on our 112 Botble demo sites, and what you can copy on your own server."
categories:
  - Documentation
  - Tutorials
tags:
  - performance
  - opcache
  - cloudflare
  - nginx
  - full-page-cache
  - botble-cms
  - laravel
image: https://botble.com/storage/news/speed-up-botble-cms-lessons-from-112-demo-sites-hero.jpg
status: published
is_featured: false
---

Every live demo you click on botble.com runs on our own servers. That's 112 Botble sites (Shofy, Martfury, Farmart, Nest, Carento, Velura and the rest), and we run dozens of them side by side on each ordinary VPS.

Demos are the first thing a buyer sees, so we care a lot about how fast they feel. This year we went through those servers layer by layer. Some changes made a big difference, some barely moved the needle, and a couple of popular tips turned out to be bad ideas.

Here's what we learned, in the order I'd do it on a new server.

## 1. Measure before you touch anything

Most people guess the database first. On the pages we profiled, the queries took around 30 ms in total, a small slice of the request. The rest was PHP: booting the framework and rendering views, shortcodes and widgets. That's true of almost any Laravel app.

So before you buy a bigger database server or start adding indexes, time the page and time the queries. With `APP_DEBUG=true` on a staging copy, the debug bar shows both. Then turn debug mode off again. A live site left with `APP_DEBUG=true` is the most common cause of slow-site tickets we see, and it adds a second or more to every request.

The cheap app-level wins are all in the [Performance Optimization guide](https://docs.botble.com/cms/performance-optimization.html): debug off, `php artisan optimize`, the menu/widget/shortcode caches in **Settings → Cache**, and Redis for cache and sessions if your host has it. Do those first. The rest of this post is about the server.

## 2. Block the bots that aren't sending you customers

This was the single biggest win, and it has nothing to do with PHP tuning.

Look at your access log and you'll probably find that a big share of requests aren't people. AI training crawlers and SEO tools walk every filter, sort and language combination on your product listings. Every one of those URLs is unique, so no cache can help, and every one is a full page render. Some crawlers also invent URLs that nobody links to, which end in 404s that still cost a render.

When we blocked the crawlers we didn't want, CPU load on the busiest server dropped by more than 80% within minutes. Nothing else we did came close.

What to do on your server:

- **Decide which bots you want.** Search engines bring you customers. Many AI training crawlers don't. Pick a policy on purpose rather than letting everything in by default.
- **Block at Cloudflare if you can.** A request blocked at the edge never reaches your server at all. Cloudflare has a ready-made setting for AI crawlers, and you can add custom WAF rules for anything else.
- **Or block in nginx** with a user-agent match that returns 403 before PHP runs.
- **Send junk URLs away cheaply.** Nobody links to `/index.php/some/page` on purpose. A one-line nginx rule redirects those to the clean URL before PHP is involved:

```nginx
# inside server {}: /index.php/<path> -> /<path>
rewrite ^/index\.php/([^/\x5c].*)?$ /$1 permanent;
```

## 3. Check that OPcache isn't full

You almost certainly have OPcache enabled. What you probably haven't checked is whether it's full.

PHP's defaults are `opcache.memory_consumption=128` (MB) and `opcache.max_accelerated_files=10000`. That's fine for one site, but if you host several Laravel sites on one server, each one gets its own copy in OPcache, even when the files are identical. Once the cache is full, PHP stops caching new scripts and compiles them again on every request. The symptom looks random: some sites are quick and others aren't, depending on which ones got a visitor first after a restart.

Check yours with a throwaway file in `public/` (delete it right after):

```php
<?php
$s = opcache_get_status(false);
echo json_encode([
    'full' => $s['cache_full'],
    'hit_rate' => round($s['opcache_statistics']['opcache_hit_rate'], 1),
    'scripts' => $s['opcache_statistics']['num_cached_scripts'],
    'free_mb' => round($s['memory_usage']['free_memory'] / 1048576),
]);
```

If `full` is true or the hit rate sits well under 99%, give it more room:

```ini
opcache.memory_consumption = 256      ; more if you host several sites
opcache.interned_strings_buffer = 32
opcache.max_accelerated_files = 50000
```

On a VPS you can also add `opcache.file_cache=/some/writable/dir`. Scripts that don't fit in memory then load from precompiled files on disk instead of being compiled again. On a server running dozens of sites, that one line cut average response time by about a third. Clean out old entries now and then; a weekly cron that deletes files older than 14 days is enough.

## 4. JIT: don't expect much

PHP 8's JIT gets a lot of attention. For a CMS it barely matters. When we measured it on its own, the gain was about 4%. JIT speeds up long-running maths, and a typical web request spends its time loading classes and rendering templates.

It also gets in the way. With `opcache.jit=tracing` enabled, PHP stopped writing to the OPcache file cache from step 3. We turned JIT off (`opcache.jit=disable`, which is also the PHP 8.4 default) and kept the file cache. That was clearly the better deal.

## 5. Cache full pages for guests

For guests who all see the same HTML, the fastest page is one you don't render at all.

Our demos use nginx's FastCGI cache for this, together with Botble's [Public Cache Control](https://docs.botble.com/cms/usage-public-cache-control.html) setting (**Settings → Cache → Public cache headers**). With it on, guest pages get `Cache-Control: public` and per-visitor cookies are removed, so nginx or a CDN is allowed to store them. Pages that contain a CSRF token (contact forms, newsletter boxes) are never marked public, because every visitor needs their own token. Cached pages come back from nginx in about 10 ms.

One popular tip to avoid: you'll find advice online to add `fastcgi_ignore_headers Set-Cookie;` so that nginx caches pages that set a cookie. Don't. nginx then stores the first visitor's cookie together with the page and hands it to every visitor after them, so strangers end up sharing one session. **Never tell nginx, Varnish or Cloudflare to ignore `Set-Cookie` or `Cache-Control` on a Botble site.** If a page doesn't cache, the app had a reason to mark it private. Let it decide.

A setup that works, simplified:

```nginx
# http {}
fastcgi_cache_path /var/cache/nginx/botble levels=1:2 keys_zone=BOTBLE:50m inactive=60m max_size=1g;
fastcgi_cache_key "$scheme$request_method$host$request_uri";

# server {}
set $skip_cache 0;
if ($request_method !~ ^(GET|HEAD)$) { set $skip_cache 1; }
if ($http_cookie ~* "botble_session") { set $skip_cache 1; }
if ($request_uri ~* "^/([a-z]{2}/)?(admin|cart|checkout|customer|vendor|login|register|ajax|api)") { set $skip_cache 1; }

# in the PHP location
fastcgi_cache BOTBLE;
fastcgi_cache_bypass $skip_cache;
fastcgi_no_cache $skip_cache;
add_header X-FastCGI-Cache $upstream_cache_status;
```

Replace `admin` with your admin path if you changed it. nginx won't store a response that has `Set-Cookie` or `Cache-Control: private` anyway, so the app stays in charge of what's cacheable. The skip rules are a second layer of safety.

## 6. Cloudflare: mostly for what never reaches you

Cloudflare's biggest value for a Botble site is often the traffic your server never sees: TLS, static assets served from the edge, and bot rules (step 2).

**Restore the real visitor IP.** Behind Cloudflare, nginx and Laravel only see Cloudflare's addresses unless you tell nginx to trust `CF-Connecting-IP` (`set_real_ip_from` for each Cloudflare range plus `real_ip_header CF-Connecting-IP`). Without it, every log line shows a Cloudflare IP, per-IP rate limits can't work, and finding a misbehaving crawler is guesswork.

If you want Cloudflare to cache HTML too, turn on Public Cache Control, create a Cache Rule that respects the origin's cache headers, and make sure requests carrying the `botble_session` cookie bypass it. Otherwise a logged-in customer can get a guest's copy of the page.

## 7. PHP-FPM: more workers won't fix a CPU problem

When pages queue, raising `pm.max_children` looks like the fix. If each request is CPU-bound, more workers just means more requests fighting over the same cores. Size the pool for memory (a PHP worker typically uses 100 to 200 MB) and reduce the load instead: bots, cache hits, OPcache. Turn on `request_slowlog_timeout = 5s` so you can see what's slow.

## The order I'd do it in

1. `APP_DEBUG=false`, `php artisan optimize`, Botble's own caches.
2. Block bots you don't want, at Cloudflare if you use it.
3. Check OPcache isn't full; add `opcache.file_cache` on a VPS.
4. Leave JIT off.
5. Full-page caching: Public Cache Control with nginx or a CDN in front.
6. Restore real visitor IPs behind Cloudflare.

None of this needs code changes to Botble itself. Keep your install [up to date](https://botble.com/how-to-upgrade-botble-cms-with-customization-code) to get the latest caching improvements. And if you're still choosing a CMS, [here's how Botble compares](https://botble.com/laravel-cms-2026) with the other Laravel options.

Questions or numbers from your own server? Send them to our support team. We'd like to know what you find.
