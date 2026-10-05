---
title: "Build Your Own Shopify Alternative: A Self-Hosted Multi-Tenant Store Platform on Laravel"
description: "Want to host stores for other people instead of building them one by one? Here's the honest list of what you have to build first — tenant isolation, provisioning, billing, domains — and what it costs to skip that work."
categories:
  - Ecommerce
  - Buyer Guides
tags:
  - shopify-alternative
  - multi-tenant
  - saas
  - laravel
  - laravel-13
  - ecommerce-saas
  - stancl-tenancy
  - botble
image: https://botble.com/storage/news/build-your-own-shopify-alternative-multi-tenant-laravel-hero.jpg
status: published
is_featured: true
---

# Build Your Own Shopify Alternative: A Self-Hosted Multi-Tenant Store Platform on Laravel

![Two stores running on one self-hosted platform, each with its own design](https://botble.com/storage/news/build-your-own-shopify-alternative-multi-tenant-laravel-hero.jpg)

When someone says "Shopify alternative", they usually mean one of two things.

A shop owner means: somewhere else to put my shop. A developer or an agency usually means something bigger. They want to *be* Shopify. Merchants sign up on their domain, pay them monthly, and run stores they host.

This post is for the second group. I'll go through what you actually have to build, and be honest about the parts that will annoy you.

## Why hosting beats building

I've watched a lot of agencies figure this out the slow way. You build a store for a client, you get paid once, and six months later you're negotiating the next project. Build a platform instead and the same store pays you every month. Better still, store number one hundred costs you almost nothing, because the platform does the work, not you.

So you have three options:

**Resell someone else's SaaS.** Fast to start, thin margin, and the platform owns your customers. When they raise prices, you find out at the same time your merchants do.

**Build multi-tenancy yourself.** You own everything. You also spend the better part of a year before the first paying store, and most of that year goes on plumbing nobody will ever thank you for.

**Self-host a multi-tenant platform.** You own the code, you keep the revenue, and you inherit the server operations. That last part is a real cost, not a footnote.

The third one is what this post is about, but only after you've looked at the list below, because that list is what you're buying.

## What "multi-tenant ecommerce" actually means

People underestimate this because the storefront is the visible part, and the storefront is the easy part. Here's what sits underneath.

### Tenant isolation

Every store's data has to be separated from every other store's. You can do it with a `tenant_id` column everywhere, a schema per tenant, or a whole database per tenant.

The column approach is cheapest to build and the easiest one to get badly wrong. Forget a single `where tenant_id = ?` in one query and store A is looking at store B's orders. I'd rather pay the cost of a database per tenant, because a missed filter can't leak anything, and deleting a customer means dropping one database instead of hunting rows across forty tables.

And it isn't only the database. Uploaded files, cache keys, queued jobs — all of it needs scoping. A cache key collision between two stores is the kind of bug that shows a merchant someone else's revenue numbers, and you won't hear about it from your logs. You'll hear about it from them.

### Provisioning

Someone signs up. Now something has to create a database, run every migration, seed demo content, make the admin user, attach the subdomain and send the welcome email. Reliably. Without making the signup request sit there for two minutes.

Then there's the unhappy path: a migration that dies halfway, a half-created store, two people claiming the same subdomain in the same second.

### Subscription billing

Plans, trials, upgrades, downgrades, proration, failed cards, dunning, grace periods. What you're really building is a state machine for the store's life: trialing, active, past due, suspended, cancelled. Not just a Stripe integration.

And if you sell anywhere cards aren't the norm, you need the boring offline path too: bank transfer, an invoice, someone flipping a switch by hand. In Vietnam, that's most of our own sales.

### Custom domains

Merchants get tired of `their-shop.yourplatform.com` faster than you'd expect. So: verify they own the domain, get a certificate, and route a domain you've never seen before to the right tenant.

### Themes

Nobody accepts one look. You need several designs, a preview, and a switch that doesn't wipe their products and pages when they change their mind.

### The operator side

You're the platform now. You need a console that tells you how many stores are live, what your MRR is, who's past due, whose domain is stuck unverified, which store failed to provision. And a way to log into a merchant's admin to help them, without asking for their password.

### Integrations

An API and webhooks, because sooner or later your CRM or your accounting needs to know when a store is created or a subscription lapses.

That's seven areas. Each one is weeks. And none of them is the ecommerce itself — products, cart, checkout, shipping, tax, payments, refunds — which you still need, and which is the only part your merchants will actually judge you on.

## Or you buy the list

[Ecommerce SaaS](https://marketplace.botble.com/ecommerce-saas) is that list, already built, on Laravel 13 and PHP 8.3+, with `stancl/tenancy` handling the tenancy layer and Botble's ecommerce as the storefront.

Going back through the seven:

**Isolation.** One MySQL database per store, per-tenant file storage under `storage/tenants/{id}`, prefixed cache keys.

**Provisioning.** Queued by default so signup returns straight away. On a small single server, set `TENANCY_PROVISION_SYNC=true` and it runs inline during the signup request in a second or two, no worker to look after.

**Billing.** Stripe Checkout and the Stripe Billing Portal through Cashier. Plus offline orders, invoices and comped accounts, which is how you handle bank transfers. Plans, trials and coupons live in the operator console, and the lifecycle is enforced rather than just logged.

**Custom domains.** Merchants add and verify their own from their store admin. You see every domain on the platform and which ones are still waiting. A scheduled command re-checks them.

**Themes.** 20 storefront designs, switchable after signup without touching the merchant's catalogue. Read the limits section before you get excited about that number.

**Operator console.** Stores, plans, coupons, subscriptions, plan orders, bank transfers, domains, API keys, webhooks, MRR snapshots, usage metering. There's a 60-second single-use token for impersonating a merchant's admin.

**Integrations.** A control-plane API with 39 endpoints and 27 signed webhook events. Separately, each store exposes the normal Botble ecommerce REST API, so a merchant can go headless or build a mobile app on their own shop.

Underneath, the storefront is ordinary Botble ecommerce: variations, cart, checkout, coupons, shipping, tax, orders, eight payment gateways available to the stores, and a multi-vendor module if one of your merchants wants vendors of their own.

## The server bit, before the price

This is the part that decides whether the whole idea works for you, so it goes first.

| What you need | Why | On shared hosting? |
|---|---|---|
| MySQL 8 user with global `CREATE`/`DROP DATABASE` | every store is a database | almost never |
| Wildcard DNS and wildcard TLS | every store gets a subdomain, immediately | rarely |
| A queue worker, or inline provisioning | creating a store is real work | no long-running processes |
| Cron running 9 scheduled commands | billing, usage, lifecycle emails, domain checks, webhook retries | usually one cron |
| Redis | nice at volume | optional |

A small VPS is the realistic floor. If the plan was to upload this to cPanel, it's the wrong product, and I'd rather you find that out here than after paying.

## Where it will annoy you

- **It's one theme.** The 20 designs are curated presets of the bundled Amerce theme, not 20 separate themes. Merchants get a genuinely different look. They don't get a different codebase.
- **Operator admins are all-powerful.** There's no role system on the operator side yet, so you can't give a support person access to stores but not billing. If you need that, you're writing it.
- **You're the host now.** Uptime, backups, upgrades, and the merchant emailing you at 11pm because their checkout looks wrong on their phone. That's the trade for keeping the whole subscription.
- **It won't find you merchants.** No software does.

## The price

$69 on the marketplace, or **$48.30 buying direct** — same product, 30% off, because going direct skips the marketplace's cut. One payment, full Laravel source, lifetime updates, six months of support, one production domain.

Compare that to the list above at any freelance rate you like. This was never a $69 decision versus a $0 one. It's a $69 decision versus three months of your life, and the version you'd build in those three months would do less.

Poke at it first: there's a [live platform](https://saas.botble.com) with the operator console open, and the [documentation](https://docs.botble.com/ecommerce-saas/) covers installation, billing and the API properly.

## Questions we get asked

**Is this a Shopify clone?**
No. It's a platform for hosting stores, the way Shopify hosts stores. What your merchants get is Botble ecommerce, not a Shopify reimplementation.

**Can each store use its own domain?**
Yes, with ownership verification. Whether a particular plan allows it is your call — the sample plans have it off on the cheapest tier, which you can change in a minute.

**Do I really need a queue worker?**
No. `TENANCY_PROVISION_SYNC=true` provisions inline during signup, in a second or two. Once you have real volume, run the worker.

**How do merchants pay me?**
Stripe Checkout for cards, with the Billing Portal so they can manage it themselves. For markets where cards are awkward, there's an offline path: invoice, bank transfer, manual activation.

**Can I set my own plans and currency?**
Yes. Prices, trial lengths, limits and coupons are all yours.

**What happens when a card fails?**
The store moves to past due, then suspended, with lifecycle emails along the way. Nothing is deleted when a store is suspended.

**Is there an API?**
Two. The control-plane one (39 endpoints, 27 signed webhook events) for running the platform, and the per-store ecommerce API for whatever a merchant wants to build.

## Where to next

- [The full Ecommerce SaaS write-up](https://botble.com/ecommerce-saas-run-your-own-store-hosting-platform-on-botble-cms), with screenshots of the operator console and the signup flow
- [Best Laravel ecommerce scripts in 2026](https://botble.com/best-laravel-ecommerce-scripts-in-2026-top-6-ranked-compared), if it turns out you want one store and not a platform
- [Why buying direct is cheaper](https://botble.com/buy-botble-products-direct-and-save-30-vs-codecanyon)
- Vietnamese readers: [đa gian hàng hay cho thuê shop?](https://botble.com/website-ban-hang-da-gian-hang-2026)

If you're still weighing this against building it yourself, do one thing first: take the seven items above and write an honest number of days next to each. That total is the real comparison.
