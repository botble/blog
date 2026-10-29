---
title: "Moving a WordPress Site to Laravel: What Comes Across, and What You'll Rebuild"
description: "A practical guide to moving a WordPress site to a Laravel CMS: what the WordPress Importer carries over, how to keep every URL, and the parts you'll have to rebuild by hand."
categories:
  - Documentation
  - Tutorials
tags:
  - wordpress
  - migration
  - wordpress-importer
  - seo
  - botble-cms
  - laravel
image: https://botble.com/storage/news/wordpress-to-laravel-migration-hero.jpg
status: published
is_featured: false
---

People who write to us about leaving WordPress are rarely unhappy with writing posts in it. The editor is fine. What wears them down is everything around the posts: thirty plugins that each want an update, a custom feature that fights WordPress's data model, a team that writes Laravel all day and then has to context-switch into hooks and `functions.php`.

If that's you, moving the content is the easy part. Keeping your URLs is the part that matters for traffic. And rebuilding the features your plugins used to provide is where the real work is.

This guide walks through all three with Botble CMS and our free WordPress Importer plugin. I'll be specific about what the importer does and doesn't carry over, because the gaps are what bite you a week after launch.

## Should you move at all?

Not always. Stay on WordPress if:

- It's mostly a content site and your editors love Gutenberg.
- You depend on a plugin with no Laravel equivalent: a big LMS, a membership suite, a WooCommerce extension that runs half your checkout.
- Nobody on the team writes PHP. A Laravel CMS rewards people who'll open the code.

Move when the custom parts have outgrown the platform. A booking flow, a directory, a customer portal, an API for an app. Those are a week of work as a Laravel module and a month of fighting as a WordPress plugin.

## Step 1: Export from WordPress

In your WordPress admin, go to **Tools → Export**, choose **All content** and download the file. That's a WXR file, WordPress's XML format, and it holds your posts, pages, categories, tags, authors and the list of uploaded images.

Big sites are fine. Files over 25 MB are read with a streaming parser rather than loaded into memory at once, and the import runs in chunks of 50 items with one database transaction per chunk.

If you run WooCommerce, export products separately as CSV (**Products → All Products → Export → Generate CSV**). There's a section on that further down.

## Step 2: Import into Botble

Install [Botble CMS](https://marketplace.botble.com/portfolio/botble), then the [WordPress Importer](https://marketplace.botble.com/products/botble/wordpress-importer) plugin. The plugin is free and MIT licensed. You'll find it under **Admin → Tools → WordPress Importer**.

Upload the XML file and pick a few options:

- **Copy images**: download images into your media library. Leave it off only if the old site stays online forever.
- **Copy categories**: recreate your categories, or drop every post into one default category if you're restructuring anyway.
- **Load SEO meta from Yoast SEO**: on by default.

### What comes across

| WordPress | In Botble | Worth knowing |
|---|---|---|
| Posts | Posts | Slug, publish date, author and featured image kept. The excerpt becomes the description (first 400 characters). View counts carry over if you used `post_views_count` |
| Pages | Pages | Slug kept. Template set to default |
| Categories | Categories | Name, description, parent/child hierarchy and slug kept. A post gets **all** its categories |
| Tags | Tags | Slug kept, attached to their posts |
| Authors | Users | Matched by email or username if they already exist. New users get a random password (more on that below) |
| Featured and in-content images | Media library | Content is rewritten to point at the local copies |
| Yoast or Rank Math title and description | SEO meta on each post | Only title and description |
| Published posts | Published | Drafts, private, pending and scheduled posts all come in as **drafts** |

Two of those rows used to be worse. Until version 1.7.2 a post only received its first category, tags were created but never attached to anything, and pages and categories got new slugs built from their titles. I found that while checking the plugin for this article, and fixed it before publishing. One catch if you imported with an older version: a re-run skips posts it already created, so it won't add the missing tags to them. Delete the imported posts, categories and tags, then import again.

About passwords: WordPress password hashes don't come across, and roles aren't mapped. Each new user gets a random 16-character password, and the importer lets you download those once as a CSV, for 30 minutes after the import. Download it straight away, or plan to have everyone use "forgot password".

### What doesn't come across

This list is the one to read twice.

- **Comments.** Botble has a comments plugin, but the importer doesn't fill it. If comments matter to you, they need a separate script.
- **Menus.** Rebuild them under **Appearance → Menus**. On most sites that's ten minutes.
- **Custom post types and custom fields (ACF and friends).** Only posts and pages are imported. Products, events, portfolio items and their fields are ignored. If they matter, they become a Botble plugin, see "Rebuilding the features" below.
- **Shortcodes.** `[caption]`, `[contact-form-7 id="12"]` and every plugin shortcode end up as literal text on the page. Watch out for `[gallery ids="1,2,3"]` in particular: Botble has its own `gallery` shortcode with different attributes, so it won't render what you expect.
- **Embeds.** A bare YouTube or Twitter URL on its own line, which WordPress turns into a player, stays a plain URL.
- **Gutenberg block comments.** The `<!-- wp:paragraph -->` markers stay in the HTML. They're invisible on the page and harmless, just untidy if you edit the source.
- **WPML and Polylang.** Translations come in as separate posts in your default language, and the links between them are lost. Botble does multilingual well, but you'll reconnect translations yourself.
- **The rest of Yoast.** Focus keyphrase, canonical URL, Open Graph fields and noindex flags aren't imported.
- **Unused media.** Only images that posts and pages actually use are downloaded. SVG files are refused on purpose, because an SVG can carry script.

## Step 3: Keep every URL

This is where migrations lose traffic, so it gets the most space here.

The good news first. If your WordPress permalinks are set to **Post name** (`/%postname%/`), which is the common choice, Botble serves posts from the root too. `/my-post` on WordPress is `/my-post` on Botble. Pages work the same way: `/about` stays `/about`, even when the page is titled "About Our Company". Tags live at `/tag/laravel` on both.

Three things need attention.

**Category URLs.** WordPress puts categories under `/category/news/`. Botble puts them at the root, `/news`, by default. Go to **Admin → Settings → Permalink** and set the category prefix to `category`, and the old URLs work again.

**Trailing slashes.** WordPress URLs end in `/`, Laravel's don't. On Apache, the `.htaccess` that ships with Botble already 301-redirects `/my-post/` to `/my-post`. On nginx, add it yourself:

```nginx
# Old WordPress URLs end in a slash; send them to the slash-less version
rewrite ^/(.+)/$ /$1 permanent;
```

**Date-based permalinks.** If your old URLs look like `/2024/05/my-post/`, Botble can't reproduce them, and it has no built-in redirect manager. Add one rewrite in your web server and it covers every post at once:

```nginx
# nginx: /2024/05/my-post/ and /2024/05/02/my-post/ -> /my-post
rewrite ^/\d{4}/\d{2}/(?:\d{2}/)?([^/]+)/?$ /$1 permanent;
```

```apache
# Apache (.htaccess, above the Laravel rules)
RewriteRule ^\d{4}/\d{2}/(?:\d{2}/)?([^/]+)/?$ /$1 [R=301,L]
```

One subtle thing: WordPress lets a page and a category share a slug, because they live under different paths there. In Botble both sit at the root, so if you have a page called `news` and a category called `news`, the second one imported becomes `news-1`. The importer does that on purpose instead of letting two records answer one URL. Look for `-1` slugs after the import and decide which one keeps the clean URL.

**Non-Latin slugs.** WordPress exports Vietnamese, Arabic or Cyrillic slugs percent-encoded. The importer decodes them. Whether they stay as they are depends on **Settings → Permalink → Turn off automatic URL translation into Latin**. Turn it on (that is, turn translation off) before importing if you want `/tin-tức` to stay `/tin-tức`. Leave it off and you'll get transliterated slugs, which means new URLs and a redirect job.

## Step 4: Images

There are three image modes. **Sync** downloads during the import, and that's what most sites should use. **External** keeps the old URLs. **Queue** downloads in the background and needs a queue worker running.

The two problems we see most in support:

- **Cloudflare blocks the downloads.** If the old site sits behind Cloudflare's bot protection, your server's requests get challenged, and the importer reports "Blocked by Cloudflare bot protection". Allow your new server's IP in Cloudflare (**Security → WAF → Tools**) or pause protection for the import, then run it again. Images that already came across aren't downloaded twice, and posts still pointing at the old site get fixed.
- **The old site is on your laptop.** Importing from `http://wordpress.test` is blocked by default, since private network addresses are refused for safety. Set `WP_IMPORTER_ALLOW_PRIVATE_IMAGE_HOSTS=true` in `.env` for the import.

Anything that fails keeps its original URL and is listed in the import summary, so nothing breaks silently. `srcset` attributes are removed rather than rewritten, and lazy-loading `data-src` attributes are moved into `src`.

## WooCommerce products

You need Botble's ecommerce plugin, which ships with every ecommerce theme ([Shofy](https://marketplace.botble.com/portfolio/shofy) is a good default). Then upload the WooCommerce CSV on the same importer screen.

What comes across: names, descriptions, SKUs, regular and sale prices with sale dates, stock, weight and dimensions, categories (`Clothing > Shirts` paths are matched by name or created), tags, images, variations built from the attribute columns, and downloadable files.

Three rules that save a support ticket:

1. **Upload the file exactly as WooCommerce exported it.** Opening and saving it in Excel or Numbers breaks the columns.
2. **Export with your WordPress admin in English.** Column names are translated otherwise, and the importer can't find **Name**, **Published**, **Is featured?** and **In stock?**. It stops and tells you which ones are missing.
3. **Give every product a SKU before exporting.** That's how a re-run recognises products it already imported. Without one, running the import twice gives you two of each.

Not imported: orders, customers, reviews and coupons. Grouped and external/affiliate products come in as ordinary physical products. For a store with years of order history, that's usually a reason to keep the old WooCommerce admin read-only for accounting rather than to migrate orders.

## After the import: a checklist

- **Check every old URL.** Download the old `sitemap.xml` and request each URL on the new site. Each one should answer 200 or 301, never 404. A quick way to do it, if your old sitemap lists posts directly:

  ```bash
  curl -s https://old-site.com/post-sitemap.xml \
    | grep -o '<loc>[^<]*' | sed 's/<loc>//; s#https://old-site.com#https://new-site.com#' \
    | while read url; do echo "$(curl -s -o /dev/null -w '%{http_code}' "$url") $url"; done \
    | grep -v '^200\|^301'
  ```

  An empty result means you're done.
- **Search for leftover shortcodes.** Search post content for `[` and fix what you find.
- **Rebuild menus and widgets.**
- **Sort out passwords** for imported authors.
- **Submit the new sitemap.** Botble generates `/sitemap.xml` on its own and can ping search engines through IndexNow.
- **Keep the old site** on a subdomain, password-protected, for a few weeks. You will want to look something up.

## Rebuilding the features

This is the part no importer does, and it's the actual reason you're moving.

Some of it is already in the box. Botble ships a contact form, galleries, per-post SEO fields, a sitemap, and [multilingual support](https://botble.com/what-is-botble-cms-the-laravel-cms-thats-built-for-vibe-coding) with per-record translations. The [free plugins on our marketplace](https://botble.com/top-5-free-botble-cms-plugins-released-in-2025-2026) cover more.

Custom post types become a plugin. Two Artisan commands scaffold the model, migration, admin form, table, routes and permissions, and you fill in the fields:

```bash
php artisan cms:plugin:create events
php artisan cms:plugin:make:crud events event
```

That's the moment the move pays off. The "events" plugin is a normal Laravel model with a normal migration, not a custom post type with forty meta keys.

## If you'd rather hand it over

We do migrations as a service: content, URL mapping and redirects, rebuilding the custom parts as plugins, and the server setup. Send us the site and what it does, and you'll get a fixed quote within one business day. See [what the service covers](https://marketplace.botble.com/customize).

If you're still choosing the platform, our [comparison of Laravel CMS options](https://botble.com/laravel-cms-2026) puts Botble next to Statamic, October CMS, Filament and the rest. And once you're live, [these are the speed fixes](https://botble.com/speeding-up-botble-cms-what-we-learned-tuning-112-demo-sites) we use on our own 112 demo sites.

Botble CMS costs $20.30 when you buy it [directly from us](https://marketplace.botble.com/portfolio/botble), 30% under the CodeCanyon price. The WordPress Importer is free.
