---
title: "Laravel POS System: Self-Hosted Point of Sale Options Compared in 2026"
description: "Shopify POS Pro is $89 per store per month. Six self-hosted Laravel and PHP POS systems compared, and the one test most buyers skip: shared stock."
categories:
  - Buyer Guides
  - Ecommerce
tags:
  - laravel-pos-system
  - point-of-sale
  - self-hosted
  - inventory
  - laravel
  - pos-pro
  - botble
image: https://botble.com/storage/news/laravel-pos-system-hero.jpg
status: published
is_featured: false
---

![Six self-hosted POS systems compared with Shopify POS, Square and Loyverse](https://botble.com/storage/news/laravel-pos-system-hero.jpg)

A shop with a website has two places that sell the same box of tea.

One is the till by the door. The other is the checkout on the site. If they keep separate stock counts, someone has to reconcile them, and the day nobody does is the day you sell the last box twice: once to a walk-in customer at 10am, once to an online order at 10:05 that you now have to refund and apologise for.

That's the question I'd put first when picking a POS, ahead of price and ahead of the feature list. Does the till sell from the **same stock** as the website, or from a copy that gets synced?

The hosted products answer it well, and they charge for it. I checked their pricing pages on 25 November 2026:

- **Shopify POS Pro** is **$89 per location per month**, billed yearly, on top of a Shopify plan (Basic is $25 a month). One shop is over $1,300 a year before card fees.
- **Square** has a $0 plan, then takes **2.6% + 15¢** of every in-person card sale. Plus is $49 per location per month.
- **Loyverse** is free, with advanced inventory at **$25 per store per month** and employee management at $5 per employee per month.

A self-hosted Laravel or PHP POS costs **$21 to $79 once**, plus your hosting. So the money argument is easy. The harder part is that most self-hosted POS scripts were built as stock-and-till systems first, and the online store is either a paid bridge to WooCommerce or a newer storefront bolted on the side. Which one you need depends on where your shop actually started.

## TL;DR: pick by your situation

- **You already run a Laravel online store and want a till for the counter:** an add-on that sells from the store's own catalogue. That's what our [POS Pro](https://marketplace.botble.com/portfolio/pos-pro) does for Botble ecommerce stores.
- **You're a physical shop first, with several branches and a warehouse:** UltimatePOS or SalePro. They're inventory systems with a till, and that's their strength.
- **Your connection drops for hours, not minutes:** Stocky, the only one here that sells itself as a fully offline app.
- **You want open source and can run a queue worker:** NexoPOS. Budget for its paid modules if you need more than one store.
- **You want it free and simple, and don't need a website:** Open Source Point of Sale, as long as you're fine keeping its footer.
- **You don't want a server at all:** Square or Loyverse. Pay per transaction or per store, and stop reading here.

## Six things to test in any POS demo

Feature lists for POS software all look alike: barcode, receipts, reports, roles. These six are what separate them once a real cashier is using one.

### 1. One stock count or two

Sell an item in the POS demo, then open the online store's product page. Has the quantity dropped? Now do it the other way round: place an online order and check the till.

If the online side is a WooCommerce add-on, you're running two systems and a sync between them. That works, but you now own a sync. Find out what happens when it fails halfway, and who notices.

In POS Pro there's nothing to sync. The till takes units off the same product rows the website checkout uses, under a row lock, so a web checkout that reserved stock a second earlier isn't overwritten. Refunds put the units back the same way.

### 2. Closing the register

At the end of a shift, someone counts the drawer. A real register flow records the opening float, adds cash sales, subtracts cash refunds, tells you what *should* be in the drawer, and stores the difference when the count doesn't match.

Plenty of scripts call a sales report a "register". Ask to see the close-shift screen. If there isn't one, you'll be doing this on paper.

### 3. Split payments, and refunds to the right place

A customer pays $30 in cash and the rest by card. Then comes back two days later for a refund. Where does the money go?

Check that the POS records each part of the payment separately and offers refund methods that match: back to cash, back to the original card, or as store credit. Without that, your cash-up and your card statement won't agree with each other.

### 4. Receipts on your printer

Thermal paper comes in 58mm and 80mm. A receipt template designed for A4 and squeezed onto 58mm is unreadable. Print a test receipt from the demo at the width you own, and check whether it can print automatically when a sale completes or needs a dialog every time.

### 5. What happens when the internet goes

Most self-hosted POS systems are web apps. No connection, no sales. Some handle short drops, one claims full offline mode. Pull the network cable on the demo (or switch off Wi-Fi) mid-sale and see what you get. If your shop is somewhere the connection is unreliable, this test outranks every other one on this list.

### 6. The licence and the modules

Two costs that don't show on the price tag:

- **Paid modules.** A $79 core that needs a $149 SaaS module and a $59 WooCommerce bridge is a $287 product.
- **The Extended licence.** On CodeCanyon, a Regular licence covers one end product that end users aren't charged for. If you'll sell the POS to other shops, or run it inside a platform that bills merchants, you need Extended. In this list it runs from about twice the Regular price to more than twenty times. Our [licence explainer](https://botble.com/understanding-botble-cms-license-regular-vs-extended) covers the difference.

## What hosted POS costs

Checked on each vendor's pricing page on 25 November 2026.

| Product | Software cost | Card fees in person | Notes |
|---|---|---|---|
| Square POS, Free | $0 per location | 2.6% + 15¢ | Online card sales 3.3% + 30¢ |
| Square POS, Plus | $49 per location per month | 2.5% + 15¢ | Online 2.9% + 30¢ |
| Shopify POS Pro | $89 per location per month, billed yearly | Depends on plan | Needs a Shopify plan too ($25 to $2,300 a month). Extra 0.6% to 2% if you use a third-party payment provider |
| Loyverse | Free | Your processor's | Advanced inventory $25 per store per month, employees $5 each per month |

None of these is a bad deal for a single shop that wants zero maintenance. They get expensive with more locations and more staff, and you can't change how they work.

## Comparison table

Self-hosted options, prices from each product page on 25 November 2026. CodeCanyon authors change prices often, so check before you buy.

| | Stack | Price | Online store | Register open/close | Offline | Last update |
|---|---|---|---|---|---|---|
| **Botble POS Pro** | Laravel add-on for Botble ecommerce | $39 ($27.30 direct) | Is the online store, same stock | Yes, with cash difference | No | November 2026 |
| **UltimatePOS** | Laravel | $79, Extended $764 | Paid WooCommerce add-on ($59) | Cash register report | "Works offline", no detail | 16 Nov 2026 |
| **SalePro** | Laravel | $21, Extended $99 | Included, shares POS stock | Cash register reports | Short breaks only | 20 Nov 2026 |
| **Stocky** | Laravel + Vue | $23, Extended $499 | Included, plus WooCommerce sync | Not described | Full offline app (PWA) | 19 Oct 2026 |
| **InfyPOS** | Laravel + React | $44, Extended $99 | Not described | Not described | Not described | 1 Oct 2026 |
| **NexoPOS** | Laravel 12 + Vue 3 | Free (GPL-3.0), paid modules | No | Yes | Not described | v6.3.0, 22 Oct 2026 |
| **OSPOS** | CodeIgniter 4 | Free (MIT + required footer) | No | Cash-up | Not described | 3.4.3, 22 Oct 2026 |

"Not described" means I couldn't find it on the product page or README. It doesn't prove the feature is missing, so ask the author.

## The six, one at a time

### Botble POS Pro: a till for the store you already have

This is ours, so weigh what I say accordingly. I'll stick to what's in the code.

POS Pro isn't a separate application. It's a plugin that adds a POS screen to a Botble ecommerce store (Shofy, Nest, Farmart, MartFury and the rest of the themes in our [ecommerce comparison](https://botble.com/best-laravel-ecommerce-scripts-in-2026-top-6-ranked-compared)). The products, variations, prices, coupons, taxes and customers are the store's own. A sale at the counter is an order in the same orders table as a website sale, so your reports, invoices and customer history don't need merging.

What the cashier gets:

- Search by name, SKU or barcode. Scanning works with a USB or Bluetooth scanner, or with the device camera when there's no scanner.
- Products with variations open a quick picker for size, colour and so on.
- Manual discounts (fixed or percentage), coupon codes, and automatic tax.
- **Split payment** across cash, card, gift card and store credit.
- **Registers**: open a shift with a starting float, see expected cash as you go, close by entering the counted amount, and the difference is stored. You can make an open register mandatory before anyone sells.
- **Refunds** to cash, to the original payment, or as store credit, with stock returned.
- **Receipts** at 58mm, 80mm or A4, with your logo, and optional automatic printing to a thermal printer.
- A **customer display** page for a second screen facing the buyer.
- Card readers through **Stripe Terminal**, and for Vietnamese shops, bank-transfer QR codes through SePay or PayFS.
- Reports by day, by payment type, and by top product.
- A toggle per product for whether it shows in the POS, so online-only items stay off the counter.

The part I haven't seen elsewhere at this price is **marketplace support**. On a multi-vendor store, each vendor can get their own POS with their own register, and can only refund orders their own drawer collected. If you're building a mall where every tenant has a physical stall, that matters. Our [multi-vendor marketplace comparison](https://botble.com/best-laravel-multi-vendor-marketplace-scripts-2026-top-6) covers the store side of that setup.

POS Pro is **$39 on CodeCanyon, or $27.30 bought [directly from us](https://marketplace.botble.com/portfolio/pos-pro)**, 30% less. It's also in the Shofy Complete bundle (Shofy plus POS Pro and four other add-ons), and in the Ecommerce SaaS Complete bundle if you're running [your own multi-tenant store platform](https://botble.com/build-your-own-shopify-alternative-a-self-hosted-multi-tenant-store-platform-on-laravel).

**Where it will annoy you:**

- It needs a Botble ecommerce store underneath. If you only want a till and no website, a standalone POS is simpler to run.
- There's no offline mode. It's a web app, so the counter needs the connection the website needs.
- Multi-location here means vendors on a marketplace, each with their own stock. It isn't a single business moving stock between its own branches and a central warehouse. UltimatePOS and SalePro are built for that.
- It will sell an item whose stock shows zero. That's deliberate (the item is physically in the customer's hand, so the count is what's wrong), but it means you should fix counts when the report shows negatives.

### UltimatePOS: the best seller, and the modules add up

$79, Extended $764, 7,015 sales, version 7.4.1 released 16 November 2026. It's the most-bought POS on CodeCanyon by a distance, with six releases between May and November this year.

It's an inventory system first: multiple businesses, multiple locations with stock tracked per location, barcode stickers, thermal receipts, custom roles, and a cash register report. Its FAQ says it works offline, with no further detail.

There's no storefront built in. Selling online means the WooCommerce add-on at $59, which syncs to a separate WordPress store. Other add-ons: SaaS and Superadmin $149, Essentials and HRM $59, Advance Repair $45. Price your real setup, not the $79.

### SalePro: cheap, broad, and honest about offline

$21, Extended $99, 3,198 sales, version 6.3.1 released 20 November 2026.

For $21 the feature list is long: two POS screens (classic and fast retail), split payment, credit and instalment sales, a customer display, thermal and WhatsApp receipts, multiple warehouses, batch and IMEI numbers, label printing, returns, and cash register reports. It also includes an online store with four designs that shares its products, stock, customers and orders with the POS, plus a restaurant mode with a kitchen display.

I like that its FAQ answers the offline question plainly: offline POS is only for short internet breaks during checkout. The SaaS add-on and mobile app are separate purchases.

### Stocky: the offline one

$23, Extended $499, 2,530 sales, version 5.9 released 19 October 2026. Laravel on the back end, a Vue single-page app on the front, with a REST API.

Stocky's selling point is a **fully offline POS** you install as an app. If your connection is unreliable, that's the one claim worth testing first. It also includes a storefront, two-way WooCommerce sync, Shopify product and stock sync, multi-screen customer display, unlimited warehouses and custom roles. The page says no core feature sits behind a paid add-on.

What I couldn't find is an open/close register with cash counts. The page mentions shift scheduling for staff, which is a different thing. Ask before you buy if your cash-up depends on it.

### InfyPOS: React front end, single shop

$44, Extended $99, 913 sales, last updated 1 October 2026. Laravel with a React single-page app.

It covers barcode scanning and printing in several paper sizes, purchase and sale returns, multiple currencies, roles, and reports. The CodeCanyon page describes it as **for a single shop with multiple warehouses**, while the vendor's own site says multi-location, so ask which one you're getting. I found nothing about an online store, a register, split payments or offline mode.

### NexoPOS: open source, modules for the rest

Free under GPL-3.0. Laravel 12, Vue 3, Tailwind 4. 1,311 stars on GitHub, version 6.3.0 released 22 October 2026, commits within the last week.

The core covers sales, payments, receipts, refunds, registers, roles and barcode (including scale barcodes). The README is clear that it starts as a **single-store POS**, and the rest comes as paid modules: MultiStore $18, Gastro for restaurants $19, Nexo Print Server $18 with a $12 adapter, and a Windows app $25. I didn't see an online store module.

Two practical notes. It needs a queue worker and scheduled tasks, so cheap shared hosting is out. And GPL obligations apply when you distribute the code, not when you run it for your own shop.

### Open Source Point of Sale: free, if you keep the footer

Free, MIT licence with a condition. CodeIgniter 4 and Bootstrap 3, PHP 8.2 or later. 4,533 stars, release 3.4.3 on 22 October 2026, though the release before that was June 2025.

It's a solid sale register with a transaction log, cash-up, receipt and invoice printing, barcode labels, gift cards, multiple tax types, users with permissions, and reports. No online store.

Read the licence before you brand it. The footer with the version and a link to the project **must stay visible on every page and can't be modified**. For an internal tool that's fine. For a product you'll resell under your name, it usually isn't.

## Before you buy any of them

- Run the stock test from section 1 in the demo. It takes two minutes and tells you whether you're buying one system or two.
- Close a register in the demo, with a wrong cash count on purpose.
- Print a receipt at your paper width.
- Turn off Wi-Fi mid-sale.
- Add up the modules you'll need, and the Extended licence if anyone but you will pay to use it.
- Read the last three months of the changelog, not the feature list.

If you already sell online with a Botble store, POS Pro is the shortest path to a counter that shares your stock. It's **$27.30 [direct](https://marketplace.botble.com/portfolio/pos-pro)**, the [install guide](https://docs.botble.com/pos-pro) is short, and the [live demo](https://pos-pro.botble.com/admin/pos) is open. If you haven't picked the store yet, start with the [Laravel ecommerce comparison](https://botble.com/best-laravel-ecommerce-scripts-in-2026-top-6-ranked-compared).
