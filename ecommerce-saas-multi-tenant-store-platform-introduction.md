---
title: "Ecommerce SaaS: Run Your Own Store-Hosting Platform on Botble CMS"
description: "Ecommerce SaaS is a self-hosted, multi-tenant store platform built on Botble CMS and Laravel 13. Customers sign up, choose one of 20 storefront designs and get their own store on its own MySQL database in about a minute. You sell the plans. Stripe or bank transfer billing, custom domains, a REST API with 38 endpoints and 27 signed webhook events."
categories:
  - Announcements
  - Ecommerce
tags:
  - ecommerce-saas
  - multi-tenant
  - saas
  - store-builder
  - laravel
  - laravel-13
  - stancl-tenancy
  - botble
  - codecanyon
image: https://landing.botble.com/ecommerce-saas/images/tenant-storefront-demo1.png
status: published
is_featured: true
---

# Ecommerce SaaS: Run Your Own Store-Hosting Platform on Botble CMS

**Ecommerce SaaS** is for people who want to host online stores for other people: a small Shopify for your country, your city or your niche.

You install it once on your own server. Your customers visit your marketing site, pick a plan and a design, choose a subdomain, and about a minute later they have a working online store with its own admin panel. You charge them monthly. The storefront, checkout, orders and products are the same Botble ecommerce that already runs thousands of shops.

![Demo store 1 on the platform, running the Amerce fashion preset](https://landing.botble.com/ecommerce-saas/images/tenant-storefront-demo1.png)

Before going further, here's who this is **not** for. It needs a VPS. It won't run on shared hosting, and we explain why near the end of this post. If you only need one shop, buy [Amerce](https://marketplace.botble.com/amerce) or another Botble ecommerce theme instead. It's cheaper and simpler.

## Each store gets its own database

This was the first decision we made and everything else follows from it.

Most multi-store scripts put every store in the same tables and tag each row with a `tenant_id`. It works until one query forgets the filter, and then one merchant can see another merchant's orders.

We built on [stancl/tenancy v3](https://tenancyforlaravel.com) and gave every store a separate MySQL database, 183 tables each. A store also gets its own upload folder (`storage/tenants/tenant{id}`), and cache, mail settings and site settings are switched per store on every request. Your own data (plans, subscriptions, operators) sits in a separate central database that stores never touch.

The practical side effect: a single store can be exported, moved or deleted without touching anyone else.

## What your customer sees

Signup is a single form: store name, subdomain, email, phone, password and plan. The customer also picks one of 20 storefront designs. By default the store comes with that design's demo catalogue, so they have something to edit instead of an empty shop. They can start empty instead, or delete the demo products later.

![The signup form: store name, store address, email, phone, password and plan](https://landing.botble.com/ecommerce-saas/images/signup-form-crop.png)

Provisioning runs in a queue job. While it runs, the customer waits on a page that refreshes itself. When it's done they land in a normal Botble admin panel on their own subdomain, with four extra screens:

- **Billing**, to pick or change a plan and pay
- **Apps**, to switch on the features their plan includes
- **Domains**, to connect their own domain
- **Themes**, to switch to another theme if you've added more than one to their plan

![Store owner's Apps screen: marketplace, blog, FAQ, galleries, sale popup and analytics](https://landing.botble.com/ecommerce-saas/images/tenant-apps.png)

Screens that could affect other stores, like Botble's plugin installer and theme installer, are blocked for store owners.

Custom domains come with instructions. The store owner types their domain and gets the exact TXT and CNAME records to add. The platform checks DNS and issues the certificate on demand through Caddy. Their original subdomain keeps working as a fallback.

![Store owner's Domains screen with the TXT and CNAME records for a custom domain](https://landing.botble.com/ecommerce-saas/images/tenant-domains.png)

## 20 storefront designs from one theme

The package includes Amerce with its 20 homepage presets: fashion, sneakers, sport, electronics, furniture, cosmetics, organic food, pet care, jewelry and more. They're all presets of one theme. When we ship an Amerce update, every store gets it, and you're not maintaining 20 codebases.

![The demo marketing site: the preset gallery, with the plans below it](https://landing.botble.com/ecommerce-saas/images/marketing-home-hero.png)

![Second demo store, using the Amerce sneaker preset](https://landing.botble.com/ecommerce-saas/images/tenant-storefront-demo2.png)

You can add other Botble ecommerce themes. Copy the theme into `platform/themes/`, add its demo data, run `php artisan tenancy:register-theme`, and assign it to the plans you want. The [theme guide](https://docs.botble.com/ecommerce-saas/adding-a-theme.html) covers it step by step.

## What you see: the operator console

You get a separate admin, the operator console, on your main domain. The dashboard shows how many stores are live, provisioning or suspended, your monthly recurring revenue, trial conversions and cancellations.

![Operator console dashboard: store counts, MRR, trials, past-due and domains awaiting verification](https://landing.botble.com/ecommerce-saas/images/operator-dashboard.png)

From here you create stores by hand (useful for your first customers or for friends), with or without demo content and with or without a plan.

![Creating a store from the operator console](https://landing.botble.com/ecommerce-saas/images/operator-store-create.png)

### Plans

A plan sets the price, trial length and limits: products, storage, staff accounts, whether a custom domain is allowed, and which apps and themes are included. The live demo runs three plans at $9, $29 and $99 a month with a 14-day trial. You set your own.

![Subscription plans in the operator console](https://landing.botble.com/ecommerce-saas/images/operator-plans.png)

The plan's price and limits are frozen when a customer subscribes. If you raise the price next year, existing subscribers keep what they bought. If a customer tries to downgrade to a plan their store no longer fits into (say 800 products on a 100-product plan), the downgrade is refused and the plan card tells them why.

![A store's subscription with the terms it bought, and buttons to change, extend or cancel](https://landing.botble.com/ecommerce-saas/images/operator-subscription-detail.png)

### Getting paid

There are three ways to take payment, and you can combine them:

1. **Stripe.** Add your own Stripe keys and customers pay through Stripe Checkout and manage their card in the Stripe Billing Portal. Built on Laravel Cashier.
2. **Bank transfer.** Customers ask to pay by transfer and see your bank details. You confirm the payment in the console, the plan activates and a numbered receipt is issued. This matters in countries where Stripe isn't available.
3. **By hand.** Give a friend a free plan, extend someone's trial, or bill a larger client by invoice outside the platform. Every change is logged with who made it.

![Bank transfer settings: payment instructions and receipt numbering](https://landing.botble.com/ecommerce-saas/images/operator-bank-transfer.png)

The platform handles the unpleasant parts too. Failed payments get a grace period while the charge is retried, and only then is the storefront paused. Cancelled stores are kept for a retention period so the owner can change their mind or take an export, then deleted. The demo uses 14 days and 30 days. Customers get emails for store ready, trial ending, payment failed, suspension and upcoming deletion, in the language they signed up in.

Coupons in the console give extra trial days (for example "60-day trial for agency partners"). Percentage or fixed discounts are Stripe promotion codes, which you create in Stripe.

Separate from all this, each store takes payments from its own shoppers through the usual Botble gateways: Stripe, PayPal, Mollie, Razorpay and others. That money goes to the store owner, not to you.

### Apps catalogue

You decide which Botble plugins stores can use and which plans get them. Anything installed on the server but not in the catalogue stays hidden.

![Apps and themes catalogue with the plans each item is assigned to](https://landing.botble.com/ecommerce-saas/images/operator-catalog.png)

### Helping a customer

When a store owner writes "my checkout looks broken", you don't need their password. The console signs you into their admin with a single-use link that expires after 60 seconds, and the login is written to the audit log.

## API and webhooks

If you already run a CRM, a billing system or a Zapier flow, the platform has a REST API at `/api/platform/v1` with 38 endpoints. It covers creating stores, changing plans, suspending, attaching domains, turning apps on and off, and reading usage. You create API keys in the console with `read` or `write` scope, and each key is rate-limited.

```bash
curl -X POST https://your-platform.com/api/platform/v1/stores \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "subdomain": "acme",
    "name": "Acme Supply",
    "owner_email": "owner@acme.test",
    "password": "a-long-password",
    "theme": "amerce",
    "preset": "fashion",
    "plan": "pro"
  }'
```

The call returns `202 Accepted` right away and the store is built in the background. When it's ready, the platform sends a `store.ready` webhook. There are 27 webhook events in total, covering stores, subscriptions, domains, apps and bank-transfer payments (`store.created`, `subscription.past_due`, `domain.verified`, and `order.paid` when you approve a transfer). Each one is signed with HMAC-SHA256. Every delivery is logged, and failed ones retry automatically or when you click Retry.

![Webhook delivery log with status codes, attempts and a retry button](https://landing.botble.com/ecommerce-saas/images/operator-webhook-deliveries.png)

This API manages the platform. It isn't a headless storefront API. Shoppers use the Amerce storefront.

## Languages

Everything a store owner, shopper or operator sees is translated into 43 languages, including right-to-left ones like Arabic. Your marketing site can run in several languages at once, with `/fr/pricing`-style URLs, a language switcher, `hreflang` tags and one sitemap for all of them.

## Server requirements

Here's what you need, and why:

- **A VPS with root access.** PHP 8.3+, MySQL 8+ or MariaDB 10.6+.
- **A MySQL user that can create and drop databases**, because each new store is a new database. Shared hosting almost never allows this, which is the main reason it won't work there.
- **A queue worker** running all the time. Store creation happens in the queue. Without a worker, a new store sits at "pending" forever.
- **Wildcard DNS and a wildcard SSL certificate** so `anything.yourplatform.com` works the moment a store is created. Caddy handles this for you. nginx works too, with a bit more setup.
- **A few cron jobs**, including one every minute for webhook retries.

Redis is optional. The default file cache keeps stores isolated. Switch to Redis when traffic grows.

For setup, open `/install` in a browser and the installer checks your server and creates your operator account. After that, `php artisan tenancy:preflight` tells you whether everything is ready before you open signups. The [installation guide](https://docs.botble.com/ecommerce-saas/installation-requirements.html) has the details, including server sizing.

## What's not included

So nobody is surprised after buying: there's no POS, no mobile app, no drag-and-drop page builder, no wallet or store credit, no loyalty points, no affiliate program, no live chat, no SMS notifications, and no per-store backup (you back up the whole server). Stores run standard Botble, so you can add any of these as a plugin.

## License and price

One license covers one production installation with unlimited stores. Staging and development copies are free.

Which license you need depends on whether you charge for stores:

- **Regular**: you run all the stores yourself, for example a group of brands you own. Nobody pays you for access.
- **Extended**: you charge customers for their stores, in any form (monthly plans, per-product fees, a cut of sales). If you're buying this to run a SaaS business, this is the one you need.

| License | CodeCanyon | Direct from Botble (30% off) |
|---|---|---|
| Regular | $69 | $48.30 ([buy now](https://marketplace.botble.com/portfolio/ecommerce-saas), card or PayPal) |
| Extended | $199 | $139.30 (email [contact@botble.com](mailto:contact@botble.com)) |

Both include the full, unencrypted Laravel source, lifetime updates and 6 months of support. More detail is in the [license guide](https://docs.botble.com/ecommerce-saas/license.html).

## Try the demo

The demo is a real install, not a video. Log in to either side:

| | URL | Login |
|---|---|---|
| Marketing site and signup | [saas.botble.com](https://saas.botble.com) | Sign up and get your own store |
| Operator console | [saas.botble.com/saas-admin/operator](https://saas.botble.com/saas-admin/operator) | `operator@botble.com` / `12345678` |
| Demo store 1 (fashion) | [demo1.saas.botble.com](https://demo1.saas.botble.com) · [admin](https://demo1.saas.botble.com/saas-admin) | `demo1@botble.com` / `12345678` |
| Demo store 2 (sneakers) | [demo2.saas.botble.com](https://demo2.saas.botble.com) · [admin](https://demo2.saas.botble.com/saas-admin) | `demo2@botble.com` / `12345678` |

The best way to judge it is to sign up for a store on [saas.botble.com](https://saas.botble.com) and watch it build.

## Technical details

| | |
|---|---|
| Framework | Laravel 13, Botble CMS |
| Multi-tenancy | stancl/tenancy v3, one MySQL database per store |
| Billing | Laravel Cashier (Stripe), bank transfer, manual |
| PHP | 8.3 or 8.4 |
| Database | MySQL 8+ or MariaDB 10.6+ |
| Web server | Caddy (recommended) or nginx with a wildcard certificate |
| Cache | file, database, Redis or Memcached |
| Storefront | Amerce theme, 20 presets |
| Languages | 43, RTL supported |
| API | 38 REST endpoints, 27 webhook events |

## Links

- Product page: [landing.botble.com/ecommerce-saas](https://landing.botble.com/ecommerce-saas/)
- Buy direct: [marketplace.botble.com/portfolio/ecommerce-saas](https://marketplace.botble.com/portfolio/ecommerce-saas)
- Buy on CodeCanyon: [botble.com/go/ecommerce-saas](https://botble.com/go/ecommerce-saas)
- Documentation: [docs.botble.com/ecommerce-saas](https://docs.botble.com/ecommerce-saas/)
- FAQ: [docs.botble.com/ecommerce-saas/faq](https://docs.botble.com/ecommerce-saas/faq.html)
- Support: [botble.ticksy.com](https://botble.ticksy.com)

Questions before buying? Email [contact@botble.com](mailto:contact@botble.com).
