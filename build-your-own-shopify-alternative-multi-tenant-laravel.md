---
title: "Build Your Own Shopify Alternative: A Self-Hosted Multi-Tenant Store Platform on Laravel"
description: "What it really takes to run your own Shopify: tenant isolation, provisioning, subscription billing, custom domains and themes. A build-vs-buy checklist, the infrastructure you need, and a self-hosted Laravel platform that already does it."
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

"Shopify alternative" means two very different things depending on who is asking.

A merchant means *another place to put my shop*. A developer or an agency usually means something else: **I want to be Shopify.** I want merchants signing up on my domain, paying me monthly, running stores I host — a small Shopify for my country, my language or my niche.

This post is about the second one. What you actually have to build, what it costs to skip the building, and where the honest limits are.

## Why people want to host stores instead of selling one

The maths is not complicated. A store you build once and hand over earns you once. A store you host earns every month, and the work of adding the hundredth store is the same as the tenth — because the platform does it, not you.

There are three routes:

| Route | You own | Monthly economics | Where it hurts |
|---|---|---|---|
| Resell a hosted SaaS | nothing | thin reseller margin | you cannot change the product, and the platform owns your customers |
| Build multi-tenancy from scratch | everything | all of it | 6–12 months before the first paying store |
| Self-host a multi-tenant platform | everything | all of it | server operations are now your job |

The third one is the reason this post exists — but only if you understand the checklist below, because that is what you are buying (or building).

## The real checklist: what "multi-tenant ecommerce" actually requires

Most people underestimate this because the shopfront is the visible part, and the shopfront is the easy part. Here is what sits under it.

### 1. Tenant isolation

Every store needs its data separated from every other store. Three approaches exist: one shared database with a `tenant_id` column everywhere, a separate schema per tenant, or **a separate database per tenant**.

The shared-column approach is the cheapest to build and the easiest to get catastrophically wrong: one forgotten `where tenant_id = ?` in one query and store A sees store B's orders. The per-database approach costs more in connection handling and migrations, but a missed filter cannot leak data across stores, and you can export, back up or delete one customer's entire shop by touching one database.

Isolation is not just the database either — uploaded files, cache keys and queued jobs all need scoping. A cache key collision between two stores is a silent bug that shows one merchant another merchant's dashboard numbers.

### 2. Provisioning

When someone signs up, something has to create the database, run every migration, seed demo content, create the admin user, attach the subdomain and send the welcome email — reliably, and without the signup request hanging for two minutes.

Then handle the failures: half-created stores, a migration that dies mid-run, a duplicate subdomain claimed at the same second by two people.

### 3. Subscription billing

Plans, trials, upgrades, downgrades, proration, failed payments, dunning emails, grace periods, and what happens to a store when the card finally stops working. You need a *state machine* for the store lifecycle — trialing, active, past due, suspended, cancelled — not just a Stripe integration.

And if you sell in a country where cards are not the default, you need an offline path: bank transfer, an invoice, manual activation.

### 4. Custom domains

Merchants outgrow `their-shop.yourplatform.com` fast. Custom domains mean verifying the domain belongs to them, issuing TLS certificates, and routing requests for a domain you have never seen before to the right tenant.

### 5. Themes and design

Merchants will not accept one look. You need multiple designs, a way to preview and switch them, and switching must not destroy their products, pages or settings.

### 6. The operator side

You are now the platform. You need a console that answers: how many stores, how many live, what is my MRR, who is past due, whose domain is stuck unverified, which store is failing to provision — and a way to log into a merchant's admin to help them without asking for their password.

### 7. Integrations

A REST API and webhooks, because you will eventually want your CRM, your accounting or an automation tool to know when a store is created or a subscription lapses.

Seven areas. Each is weeks of work, and none of them is the ecommerce itself — products, cart, checkout, shipping, tax, payments, orders, refunds. That part you still need, and it is the part merchants judge you on.

## Buying the checklist instead: Ecommerce SaaS

[Ecommerce SaaS](https://marketplace.botble.com/ecommerce-saas) is that checklist, implemented, on Laravel 13 and PHP 8.3+, using `stancl/tenancy` v3 for the tenancy layer and Botble's ecommerce for the storefront itself.

Mapping it back to the seven items:

**Isolation** — one MySQL database per store, plus per-tenant file storage under `storage/tenants/{id}` and prefixed cache keys. Deleting a customer means dropping their database, not hunting rows.

**Provisioning** — queued by default, so signup returns immediately while the store is built. On a single small server you can set `TENANCY_PROVISION_SYNC=true` and provisioning runs inline during the signup request in about one to two seconds, with no queue worker to babysit.

**Billing** — Stripe Checkout and the Stripe Billing Portal through Cashier for cards, plus offline orders, invoices and comped accounts for bank transfers and manual sales. Plans, trials and coupons are managed in the operator console, and the store lifecycle (trialing → active → past due → suspended) is enforced, not just recorded.

**Custom domains** — merchants add their own domain and verify ownership from their store admin; the operator sees every domain on the platform and which ones are still awaiting verification. A scheduled command re-checks them.

**Themes** — 20 storefront designs ship with it, switchable after signup without touching the merchant's catalogue. They are presets of one bundled theme, which matters — see the limits section.

**Operator console** — stores, plans, coupons, subscriptions, plan orders, bank transfers, domains, API keys, webhooks, plus MRR snapshots and usage metering. There is a single-use, 60-second impersonation token for logging into a merchant's admin.

**Integrations** — a control-plane REST API with 39 endpoints and 27 signed webhook events. Separately, each *store* also exposes the bundled Botble ecommerce REST API, so a merchant's shop can be driven headlessly or from a mobile app.

The storefront underneath is ordinary Botble ecommerce: products with variations, cart, checkout, coupons, shipping, tax, order management, plus eight payment gateways available to the stores themselves and a multi-vendor marketplace module if a merchant wants vendors inside their own shop.

## The infrastructure you need

This is the part that decides whether the project is realistic for you, so it goes before the pricing, not after.

| Requirement | Why | Shared hosting? |
|---|---|---|
| MySQL 8 user with global `CREATE`/`DROP DATABASE` | every store is a database | almost never granted |
| Wildcard DNS (`*.yourdomain.com`) + wildcard TLS | every store gets a subdomain, instantly | rarely available |
| A queue worker, or inline provisioning | store creation is real work | no long-running processes |
| Cron running 9 scheduled commands | billing, usage, lifecycle emails, domain checks, webhook retries | usually one cron only |
| Redis (optional) | cache and queues at volume | optional |

A small VPS is the practical minimum. If your plan was "upload it to cPanel", this is the wrong product, and that is better to learn now than after the purchase.

## What it is not

Honesty here saves everyone a refund.

- **One storefront theme.** The 20 designs are curated presets of the bundled Amerce theme, not 20 independent themes. Merchants get a genuinely different look, not a different codebase.
- **No role-based permissions on the operator side.** Operator admins are admins. If you need a support agent who can see stores but not billing, that is yours to add.
- **You are the host.** Uptime, backups, upgrades and merchant support are now your responsibility. That is the trade for keeping 100% of the subscription revenue.
- **It is a platform, not a business.** Nothing in the box brings you merchants.

## What it costs

Ecommerce SaaS is **$69** on the marketplace, or **$48.30 buying direct** — the same product, 30% cheaper, because buying direct skips the marketplace's cut. One payment, full Laravel source, lifetime updates, six months of support, one production domain per license.

Put that against the alternative: at a freelance rate of $30/hour, the seven-item checklist above is not a $69 problem. It is a several-thousand-dollar problem, and the version you build in three months will do less than the one you can install this afternoon.

If you want to see it before deciding, there is a [live demo platform](https://saas.botble.com) with the operator console open, and the [product documentation](https://docs.botble.com/ecommerce-saas/) covers installation, billing and the API in full.

## Frequently asked questions

**Is this a Shopify clone?**
No. It is a platform for hosting stores, the way Shopify hosts stores. The merchant-facing storefront is Botble ecommerce, not a Shopify reimplementation.

**Can each store use its own domain?**
Yes, with ownership verification. Whether a given plan allows it is up to you — the sample plans ship with custom domains turned off on the cheapest tier, which you can change.

**Do I need a queue worker?**
Not necessarily. Set `TENANCY_PROVISION_SYNC=true` and stores are provisioned inline during signup, in about one to two seconds. A worker is the better choice once you have real volume.

**How do merchants pay me?**
Stripe Checkout for cards, with the Stripe Billing Portal for self-service. For markets where cards are awkward, there are offline orders, invoices and manual activation — a bank transfer flow, essentially.

**Can I charge in my own currency, with my own plans?**
Yes. Plans, prices, trial lengths, limits and coupons are yours to define in the operator console.

**What happens when a subscription fails?**
The store moves through the lifecycle — past due, then suspended — with lifecycle emails sent by a scheduled command. Data is not deleted on suspension.

**Is there an API?**
Two, in fact. A control-plane API with 39 endpoints and 27 signed webhook events for running the platform, and the per-store ecommerce API for anything a merchant wants to build on their own shop.

## Where to go next

- [Ecommerce SaaS — full product introduction](https://botble.com/ecommerce-saas-run-your-own-store-hosting-platform-on-botble-cms), with screenshots of the operator console and the signup flow
- [Best Laravel ecommerce scripts in 2026](https://botble.com/best-laravel-ecommerce-scripts-in-2026-top-6-ranked-compared), if you need one store rather than a platform
- [Buy direct and save 30%](https://botble.com/buy-botble-products-direct-and-save-30-vs-codecanyon), on why the direct price is lower

If you are weighing this against building it yourself, work through the seven-item checklist and put an honest number of days next to each line. That number is the real comparison, not $69 against $0.
