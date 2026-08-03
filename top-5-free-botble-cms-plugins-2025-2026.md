---
title: "Top 5 Free Botble CMS Plugins Released in 2025–2026"
description: "We dated every free plugin on the Botble Marketplace and ranked only the ones released since 2025. Here are the 5 best new free plugins — Expected Delivery Date, Barcode Generator, LiveChat, EmailOrder, and Consultation Booking."
categories:
  - Buyer Guides
  - Development
tags:
  - botble-cms
  - plugin
  - free-plugins
  - marketplace
  - laravel
  - botble
image: https://botble.com/storage/news/top-5-free-botble-cms-plugins-2025-2026-hero.jpg
status: published
is_featured: false
---

# Top 5 Free Botble CMS Plugins Released in 2025–2026

Most "best free plugins" lists are really lists of the *oldest* free plugins. Download counters only go up, so a plugin from 2020 will out-rank a genuinely better one from last year forever.

So we did it the other way round. We pulled all **297 plugins** from the [Botble Marketplace](https://marketplace.botble.com/plugins), worked out when each free plugin was actually first released, threw out everything published before 2025, and ranked only what's left.

The result is a list of plugins built against **current Botble** — most require core 7.5.0+ — rather than plugins that happen to have had a five-year head start.

## How we dated and ranked them

This took more work than it should have, and it's worth being upfront about why.

**The marketplace doesn't publish a release date.** There's no date on the product page, and the `Product` JSON-LD carries no `datePublished` field — the only `datePublished` values in the schema belong to individual *reviews*. The sitemap's `lastmod` is useless too: 332 of 348 product URLs claim they were modified this month, because that timestamp gets bumped every time the view counter ticks.

So we derived the release date from two sources:

1. **GitHub repository history.** Nearly every community plugin links to a public repo. We resolved each one and took its **first release date** (falling back to repo creation). This is exact.
2. **Earliest review date**, for the handful of plugins with no public repo. A plugin can't be reviewed before it exists, so this is a *lower bound* on age, not a precise date. These are marked with a **~** below.

Then we applied three mechanical filters — free, first released on or after **1 January 2025**, and **at least 3 user ratings** — and ranked the survivors by download count.

> **One honest caveat on views.** The marketplace's *Most Viewed* sort only exposes its top 40, and every free plugin in it is a pre-2025 plugin. View data therefore can't separate plugins inside this cohort, so unlike a straight all-time ranking, this list is scored on **downloads and rating only**.

Two popular new plugins get cut by the ratings filter, and it's fair to name them: **announcementadmin** has 729 downloads — enough for 2nd place — but **zero ratings**, so there's no quality signal at all. **FOB Geo Data Detector** has 593 downloads but averages 3.0.

> Data snapshot: August 2026. All five plugins are MIT-licensed and free.

## The ranking at a glance

| # | Plugin | Released | Downloads | Rating |
|---|--------|----------|-----------|--------|
| 1 | [Expected Delivery Date](https://marketplace.botble.com/products/FriendsOfBotble/fob-expected-delivery-date) | Mar 2025 | 985 | 5.0 ★ (4) |
| 2 | [FOB Barcode Generator](https://marketplace.botble.com/products/FriendsOfBotble/fob-barcode-generator) | May 2025 | 642 | 4.0 ★ (3) |
| 3 | [FOB LiveChat](https://marketplace.botble.com/products/FriendsOfBotble/fob-live-chat) | Dec 2025 | 601 | 5.0 ★ (3) |
| 4 | [EmailOrder](https://marketplace.botble.com/products/botble/EmailOrder) | May 2025 | 580 | 5.0 ★ (3) |
| 5 | [Consultation Booking](https://marketplace.botble.com/products/botble/consultation-booking) | ~Sep 2025 | 554 | 4.5 ★ (10) |

---

## 1. Expected Delivery Date

**985 downloads · 5.0 ★ (4 ratings) · released March 2025 · v1.0.8 · requires Botble ≥ 7.5.0, PHP 8.2+**

The most downloaded new free plugin, and the only one in this cohort to clear 900 — with a clean 5.0 rating behind it.

It adds an expected delivery estimate to your products, and it works across every Botble ecommerce script. You set a minimum and maximum number of delivery days per product, with a site-wide default for anything you haven't configured individually, and the estimate renders on the product page.

The reason this matters more than it sounds: "when will it arrive?" is one of the biggest pre-purchase objections in ecommerce, and answering it on the product page rather than at checkout measurably reduces abandonment. Buyers who can't find a delivery estimate frequently just leave.

Colours and icon are configurable under **Settings → Expected Delivery Date**, so it can be styled to match your theme without touching a template.

**Use it when:** you sell physical goods with a predictable dispatch window.

**Skip it when:** you're digital-only, or your delivery times swing too wildly to quote a range honestly.

## 2. FOB Barcode Generator

**642 downloads · 4.0 ★ (3 ratings) · released May 2025 · v1.0.3 · requires Botble ≥ 7.5.0**

Easily the most feature-dense plugin on this list, and the one that replaces actual paid software.

It generates and prints barcode labels for products and orders, supporting **Code 128, EAN-13, EAN-8, UPC-A, UPC-E, QR Code, and Data Matrix**. Output goes to A4, Letter, P4 label sheets, or thermal printers (4×6 and 2×1) — so it works with the label printers people actually have in a warehouse.

You get granular control over what appears on each label, choosing from 14 product fields (name, SKU, price, brand, category, weight, dimensions, stock, and so on), with bulk generation across many products at once and a print preview before you commit a sheet.

Worth noting: it's completely self-contained, with **no Composer commands required** to install — a genuine convenience if you're deploying to shared hosting where you can't run Composer.

The 4.0 average is the lowest in this top 5, though across only 3 ratings. Given the scope of what it does, that still reads as a strong result rather than a warning.

**Use it when:** you run physical inventory, a warehouse, or a retail counter.

**Skip it when:** you don't print anything.

## 3. FOB LiveChat

**601 downloads · 5.0 ★ (3 ratings) · released December 2025 · v1.0.5 · requires Botble ≥ 7.3.0**

The newest plugin on this list — released in December 2025 and already past 600 downloads, which is the fastest uptake in this cohort.

It adds a floating AJAX live chat widget to your site for real-time visitor support, with an admin-side panel for handling conversations. No third-party SaaS, no per-agent monthly fee, no external script tag: the conversations stay in your own database.

That last point is the real argument for it. Hosted chat widgets mean piping visitor conversations through someone else's servers and paying per seat forever. For a small team, a self-hosted widget is often all you need.

At v1.0.5 within months of release, the author is clearly iterating quickly.

**Use it when:** you want live chat without a SaaS subscription or a third-party script on your pages.

**Skip it when:** you need a full omnichannel helpdesk with routing and SLAs — that's a different class of tool.

## 4. EmailOrder

**580 downloads · 5.0 ★ (3 ratings) · released May 2025 · v1.0.1 · Botble ecommerce**

A small plugin that removes a genuinely annoying daily task.

It lets you send a personalised email to a customer **directly from the order detail page** in the admin panel, instead of copying an address into your mail client and writing the message from scratch. It ships with reusable templates for the messages you send constantly — *Order Delivered*, *Delayed*, *Processing* — and logs every message against the order so you have a record of what was said and when.

That log is the underrated part. When a customer says "nobody told me it was delayed", the answer is on the order.

At v1.0.1 it's the least mature release here, and its author has two other plugins on the marketplace rated 2.0 — so this one's 5.0 across 3 ratings is the thing to weigh, not the author's overall track record.

**Use it when:** you manually email customers about order status more than a couple of times a week.

## 5. Consultation Booking

**554 downloads · 4.5 ★ (10 ratings) · released ~September 2025 · v1.2.5 · requires Botble ≥ 7.3.0**

Fifth on downloads, but by one measure the most trustworthy plugin in this entire cohort: **10 ratings** — nearly double any other plugin released since 2025, and more than most plugins twice its age.

It turns a Botble site into an appointment booking system: consultation services, consultant profiles with availability, a frontend AJAX booking form, and full backend management with role-based permissions. You drop it onto any page with a `[consultation-booking]` shortcode, and it inherits your theme's colours including dark mode.

It's also the most developed release here at **v1.2.5** — the only plugin in the top 5 already on a second minor version, which lines up with the review count. People are using it and the author is responding.

**Use it when:** you're a consultant, agency, clinic, or any service business that books time rather than shipping products.

**Skip it when:** you need payment-on-booking or multi-location resource scheduling — check the feature list against your workflow first.

---

## Honourable mentions

Strong 2025+ releases that missed the cut, mostly on the 3-rating minimum:

- **[FOB Request Quote for eCommerce](https://marketplace.botble.com/products/FriendsOfBotble/fob-request-quote)** (531 downloads · 5.0 ★ across **5** ratings · Sep 2025) — the narrowest miss on the list, and the best-reviewed after Consultation Booking. Adds a "Request a Quote" modal to product pages with status tracking through Pending → Processing → Completed. Essential for B2B stores that negotiate rather than list prices.
- **[FOB Product WhatsApp Order](https://marketplace.botble.com/products/FriendsOfBotble/fob-product-whatsapp-order)** (595 downloads · 5.0 ★, 2 ratings · Oct 2025) — one-click WhatsApp ordering from the product page, with a pre-filled message containing the product details.
- **[FOB Google Maps Geocoding](https://marketplace.botble.com/products/FriendsOfBotble/fob-google-maps-geocoding)** (608 downloads · 5.0 ★, 1 rating · May 2025) — address autocomplete and automatic lat/long. Note the hard limitation: it only supports Flex Home, Jobcy, JobBox, Hously, and Homzen.
- **[PWA](https://marketplace.botble.com/products/motionmedia/pwa)** (535 downloads · 4.3 ★, 4 ratings · ~Dec 2025) — makes your site installable and offline-capable. Requires one manual step: adding the manifest link to your theme's Header HTML.

Notice a pattern? **Friends Of Botble accounts for three of the top five and three of the four honourable mentions.** If you only follow one publisher on the marketplace, follow that one.

## Installing any of these

All five install the same way, and neither method needs the command line.

**From the admin panel (recommended):**

1. Go to **Admin → Plugins → Add new**.
2. Search for the plugin by name.
3. Click **Install**, then **Activate**.

**Manually:**

1. Download the ZIP from the plugin's marketplace page.
2. Extract it into `platform/plugins/`.
3. Go to **Admin → Plugins** and click **Activate**.

**Check your core version first.** This matters more for new plugins than old ones: Expected Delivery Date, Barcode Generator, and Request Quote all require **Botble 7.5.0+**, and Expected Delivery Date also wants **PHP 8.2+**. LiveChat and Consultation Booking are more forgiving at 7.3.0+. If you're on an older core, [upgrade first](https://botble.com/how-to-upgrade-botble-cms-with-customization-code).

## One caveat worth stating

Free and MIT-licensed doesn't mean supported. These are community plugins maintained by individual developers on their own time — several carry a "Support the Author" PayPal link on their marketplace page, which tells you the funding model honestly.

Newer plugins carry a specific version of that risk. A 2020 plugin with 3,000 downloads has proven it will be maintained; a plugin from eight months ago hasn't yet. The 3-rating minimum used here filters out the completely untested, but three reviews is still three reviews.

That's not a reason to avoid them — it's a reason to check the changelog date before you make one load-bearing, and to accept that you might end up maintaining a fork. For anything mission-critical — payments, KYC, wholesale pricing — the [official Botble plugins](https://marketplace.botble.com/plugins) come with support attached, and that's usually money well spent.

## Wrapping up

If you run a physical-goods store, install **Expected Delivery Date** today — it's ten minutes of work against one of the most common reasons people abandon a cart.

Add **FOB Barcode Generator** if you touch inventory, **FOB LiveChat** if you're paying a monthly fee for a chat widget you could self-host, and **Consultation Booking** if you sell time rather than products.

Browse the full catalogue at [marketplace.botble.com/plugins](https://marketplace.botble.com/plugins) — and if you build something useful, [publish it there](https://marketplace.botble.com). Every plugin on this list started as one developer solving their own problem.
