---
title: "Top 5 Free Botble CMS Plugins Released in 2025-2026"
description: "We read every review on every free plugin released since 2025, not just the star ratings, and ranked the five that users confirm actually work: Expected Delivery Date, FOB LiveChat, WhatsApp Order, Google Merchant Feed and Facebook Catalog Feed."
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

# Top 5 Free Botble CMS Plugins Released in 2025-2026

Most "best free plugins" lists are really lists of the oldest free plugins. Download counters only go up, so a plugin from 2020 will out-rank a better one from last year forever.

We did it the other way round. We went through all 297 plugins in the [Botble Marketplace](https://marketplace.botble.com/plugins), kept only the free ones first released since 2025, and ranked those.

What you get is a list of plugins built against current Botble. Most of them require core 7.5.0 or higher.

## How we ranked them

Two filters to start: free, and first released on or after 1 January 2025. Then we ranked what was left by download count.

The important part is what we did next, and it's worth explaining because it changed the list completely.

**We ignored the star ratings and read the reviews instead.** On the marketplace, the review box doubles as a support channel. People open it to report a bug or ask for a feature, leave 5 stars out of politeness, and move on. The result is that a 5.0 average tells you almost nothing. One plugin in this group averages 4.3 stars across four reviews, and every single one of those four reviews says the plugin won't install.

So we read all of them. A plugin only made this list if its reviews contain someone confirming it works and no unresolved reports of it failing. That knocked out several plugins with more downloads than the ones below, including the two highest-rated new plugins in the catalogue.

If you take one thing from this article, make it that: on any plugin marketplace, open the reviews and read them. The number above them is not the same thing.

> Data snapshot: August 2026. All five plugins are MIT-licensed and free.

## The ranking at a glance

| # | Plugin | Released | Downloads | What reviewers say |
|---|--------|----------|-----------|--------------------|
| 1 | [Expected Delivery Date](https://marketplace.botble.com/products/FriendsOfBotble/fob-expected-delivery-date) | Mar 2025 | 985 | "Phenomenal stuff!" |
| 2 | [FOB LiveChat](https://marketplace.botble.com/products/FriendsOfBotble/fob-live-chat) | Dec 2025 | 601 | "Really nice plugin" |
| 3 | [FOB Product WhatsApp Order](https://marketplace.botble.com/products/FriendsOfBotble/fob-product-whatsapp-order) | Oct 2025 | 595 | No issues reported |
| 4 | [Google Merchant Feed](https://marketplace.botble.com/products/rajaishtiaq6/google-merchant-feed) | Nov 2025 | 453 | Feed accepted by Merchant Center |
| 5 | [FOB Facebook Catalog Feed](https://marketplace.botble.com/products/FriendsOfBotble/fob-facebook-catalog-feed) | Aug 2025 | 358 | "Works excellent" |

## 1. Expected Delivery Date

985 downloads, released March 2025, v1.0.8. Requires Botble 7.5.0+ and PHP 8.2+.

The most downloaded new free plugin, and the only one in this group past 900. It's also the only plugin we looked at with four reviews and not one bug report.

It shows an expected delivery estimate on your products and works across every Botble ecommerce script. You set a minimum and maximum number of delivery days per product, plus a site-wide default for anything you haven't configured individually.

"When will it arrive?" is one of the biggest pre-purchase questions in ecommerce, and answering it on the product page instead of at checkout cuts abandonment. Buyers who can't find a delivery estimate often just leave.

Colours and icon are configurable under Settings > Expected Delivery Date, so you can match your theme without touching a template.

Install it if you sell physical goods with a predictable dispatch window. Skip it if you're digital-only, or if your delivery times swing too much to quote a range honestly.

## 2. FOB LiveChat

601 downloads, released December 2025, v1.0.5. Requires Botble 7.3.0+.

The newest plugin here, released in December 2025 and already past 600 downloads. That's the fastest uptake in this group.

It adds a floating AJAX live chat widget for real-time visitor support, with an admin panel for handling conversations. There's no third-party SaaS involved and no external script tag, so conversations stay in your own database. Hosted chat widgets route visitor conversations through someone else's servers and charge per seat forever. For a small team, a self-hosted widget usually covers it.

Two things reviewers flagged, neither of them breakage. The widget shows a "Powered By FOB" link in the chat window, and the online status is always on rather than reflecting whether anyone is actually there. Both are worth knowing before you put it in front of customers.

Reaching v1.0.5 within a few months of release suggests the author is iterating quickly.

Install it if you're paying a subscription for a chat widget and you need one person answering messages.

**If you need more than that, look at [Tawkly](https://marketplace.botble.com/portfolio/tawkly)** ($20.30, by Botble Technologies, 4.8 stars from 5 reviews). It's the paid version of this idea and it's built for a support team rather than a solo admin. Agents get their own portal at `/agent/login` with a dashboard, messenger and availability toggle, separate from admin users. New conversations round-robin to whoever has the fewest open threads. On top of that you get file attachments with signed download links, a canned-response library, proactive chat, working-hours scheduling that flips you online and offline automatically, and webhooks into Slack, Discord, Telegram, n8n or Zapier. Still self-hosted with no monthly fee, and there's a [live demo](https://live-chat.botble.com) including the agent portal.

## 3. FOB Product WhatsApp Order

595 downloads, released October 2025, v1.0.1. Requires Botble 7.5.0+ and PHP 8.1+.

Adds a "Chat on WhatsApp" button to product pages. The customer taps it, WhatsApp opens with a message already filled in containing the product details, and they're talking to you.

In a lot of markets, Brazil, India, Indonesia, the Middle East and most of Southeast Asia, WhatsApp is the sales channel. Buyers don't fill in contact forms or complete a checkout flow, they message you and ask if you have it in stock. This turns that into one tap from the product page.

You configure the message template with dynamic product variables, control button colour and placement, and set display rules based on stock status. It validates international phone numbers and is fully translatable.

No reviewer has reported a problem with it, though that's across only two reviews, so it's a thinner signal than the two above.

Install it if your customers already message you on WhatsApp.

## 4. Google Merchant Feed

453 downloads, released November 2025, v1.1.0, by Ishtiaq Ahmed.

Auto-generates a Google Shopping XML feed from your product catalogue so you can list products in Google Merchant Center without exporting spreadsheets by hand.

The feed updates in real time from your store and includes title, description, price, sale price, brand, categories, images and stock availability. Categories are mapped to Google product types automatically, and pricing uses whatever currency your store is configured for.

One reviewer running it on Shofy confirmed the feed generates and Merchant Center accepts it, which is the thing you actually need to know. The same reviewer noted that apparel products throw warnings in Merchant Center, because Google wants size, colour and age group attributes for clothing that the feed doesn't currently map. If you sell clothes, expect to fill those in on Google's side.

Install it if you want to run Google Shopping ads. Note it pairs naturally with the next one.

## 5. FOB Facebook Catalog Feed

358 downloads, released August 2025, v1.0.3.

The same job as above, pointed at Facebook. It generates a Facebook Catalog Feed XML compatible with Commerce Manager, and because Facebook accepts the Google Shopping feed format, it works for both.

That's exactly what its one reviewer reports: it works for both Google Merchant and Facebook commerce. Lowest download count on this list, but an unambiguous confirmation from someone running it in production.

It's more configurable than the Google plugin. You get four separate feed URLs (all products, new, featured, on sale), the option to include or exclude out-of-stock items, the option to list product variations as separate items, a fallback brand name, and configurable condition and availability text. There's an admin dashboard widget with the feed URLs so you're not hunting for them.

Install it if you're running Facebook or Instagram Shopping. If you want both Google and Facebook and only want one plugin, start here, since the format covers both.

## Honourable mentions

- **[Login Guard](https://marketplace.botble.com/products/botble/login-guard)** (271 downloads, Jan 2026, by Botble Technologies). Detects new device logins by fingerprinting user agent and IP, emails the user, and lets them manage trusted devices from their profile. Works for both admin and customer accounts. Its one review reads "Excellent, works as described!" Too few downloads to rank, but it's clean and it's a security win.
- **[FOB Barcode Generator](https://marketplace.botble.com/products/FriendsOfBotble/fob-barcode-generator)** (642 downloads, May 2025). By downloads this would sit at number 2, and it's the most capable plugin in the whole group: seven barcode formats, thermal and sheet printing, bulk generation, 14 configurable label fields. It's here rather than in the list because two reviewers report real problems, one that permissions don't work for anyone below SuperAdmin, and one that the seller dashboard redirects to admin on multi-vendor setups. If you're a single admin printing labels, it will probably serve you well. If you run multi-vendor, wait.
- **[FOB Disable Right Click](https://marketplace.botble.com/products/FriendsOfBotble/fob-disable-right-click)** (420 downloads, Nov 2025). Basic content protection. One reviewer confirms it works on shop script v1.4.2, another says it does nothing on 1.8.2.1, so check it against your version.

Four of the five plugins in the main list are published by **Friends Of Botble**. If you follow one publisher on the marketplace, make it that one.

## Installing any of these

All five install the same way, and neither method needs the command line.

From the admin panel:

1. Go to Admin > Plugins > Add new.
2. Search for the plugin by name.
3. Click Install, then Activate.

Manually:

1. Download the ZIP from the plugin's marketplace page.
2. Extract it into `platform/plugins/`.
3. Go to Admin > Plugins and click Activate.

Check your core version first. Expected Delivery Date and WhatsApp Order both need Botble 7.5.0+, and Expected Delivery Date also wants PHP 8.2+. LiveChat runs on 7.3.0+. If you're on an older core, [upgrade first](https://botble.com/how-to-upgrade-botble-cms-with-customization-code).

## Before you install

Free and MIT-licensed doesn't mean supported. These are community plugins maintained by individual developers in their own time, and several carry a "Support the Author" PayPal link on their marketplace page, which tells you the funding model honestly.

Newer plugins carry a particular version of that risk. A 2020 plugin with 3,000 downloads has proven it will be maintained. A plugin from eight months ago hasn't yet.

So do what we did. Open the reviews before you install, and read past the star rating to what people actually wrote. A plugin with two reviews saying "this works" is a safer bet than one with ten reviews averaging 4.5 where half of them are error messages.

For anything mission-critical like payments, KYC or wholesale pricing, the [official Botble plugins](https://marketplace.botble.com/plugins) come with support attached, and that's usually money well spent.

## Where to start

If you run a physical-goods store, install Expected Delivery Date today. It's ten minutes of work against one of the most common reasons people abandon a cart.

Add FOB LiveChat if you're paying monthly for a chat widget you could self-host, WhatsApp Order if your customers already message you there, and one of the two feed plugins if you're advertising on Google or Facebook.

Browse the full catalogue at [marketplace.botble.com/plugins](https://marketplace.botble.com/plugins). If you build something useful, [publish it there](https://marketplace.botble.com). Every plugin on this list started as one developer solving their own problem.
