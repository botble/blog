---
title: "Laravel Multi-Tenancy: Database Per Tenant vs One Shared Database"
description: "The trade-off nobody explains properly: what a database per tenant actually costs you in migrations, backups and cleanup, when a shared table with tenant_id is the right call, and the mistakes that only show up after you have real customers."
categories:
  - Development
  - Laravel
tags:
  - laravel
  - multi-tenancy
  - saas
  - stancl-tenancy
  - database
  - ecommerce-saas
image: https://botble.com/storage/news/laravel-multi-tenancy-database-per-tenant-vs-single-database-hero.jpg
status: published
is_featured: false
---

# Laravel Multi-Tenancy: Database Per Tenant vs One Shared Database

![One app, many tenant databases](https://botble.com/storage/news/laravel-multi-tenancy-database-per-tenant-vs-single-database-hero.jpg)

Every multi-tenant project starts with the same fork in the road. One database with a `tenant_id` column on everything, or a separate database for each customer.

Most articles answer this with a feature table and move on. The table isn't wrong, it's just written from before anyone had customers. What follows is the version I'd want to read: what each choice costs you six months in, when you have a few dozen tenants and someone is paying you.

## The three options, briefly

**Shared database, `tenant_id` column.** One schema, every table carries the tenant. Cheapest to build and cheapest to host.

**Schema per tenant.** One database, a schema per tenant. Comfortable on PostgreSQL, awkward on MySQL where "schema" and "database" are the same thing.

**Database per tenant.** Each customer gets their own database. This is what `stancl/tenancy` does by default on Laravel, and what we use in production.

## What the shared column really costs

The failure mode is famous and it's still worth stating: one query without the filter and tenant A reads tenant B's data.

Yes, global scopes help. Yes, a base model helps. But the leak doesn't come from the code you wrote carefully, it comes from the raw query you wrote at 1am to fix a report, the queued job that doesn't run in a request context, the package that queries its own tables, or the migration that backfills a column for everyone.

The second cost is quieter. Your tables grow to the size of your whole business, not one customer's. Indexes get fatter, `SELECT ... WHERE tenant_id = ?` starts scanning more, and one heavy customer makes every other customer's dashboard slower. You can shard later, but "later" arrives the same week as three other emergencies.

The third is deletion. Someone asks you to delete their data, for real, and now you're writing a script that walks forty tables in the right order, on a live database, hoping nothing references anything you forgot.

## What database-per-tenant really costs

It fixes those three. It hands you a different set, and these are the ones people don't list.

**Migrations run N times.** With 200 tenants, a schema change runs 200 times. It will fail halfway on tenant 137 because someone's table has a row that violates a new unique index. Now you have 136 tenants on the new schema and 64 on the old, and your app has to survive that state until you fix it.

**Backups aren't one file.** This one bit us. Our demo platform restores itself on a schedule, and the restore only covered the central database, because that's what the backup tool was pointed at. The tenant databases were never in the loop, so when a visitor broke one store's language settings, that store stayed broken for weeks. Nothing alerted us. The restore ran every two hours, reported success, and touched nothing that mattered.

If each tenant is a database, then every operational routine you own, backup, restore, migrate, seed, anonymise, has to grow a loop.

**Orphans pile up.** Tenants get created and abandoned, especially if you offer trials. We looked at that same platform and found 35 tenant databases for 8 live tenants, plus 35 storage directories. Nothing was wrong, exactly. Nothing had cleaned up either, and nothing ever would have, because the delete path only removed the row in the central database.

**Connections.** Every tenant database is a connection. MySQL's `max_connections` is finite, and PHP-FPM will happily try to exceed it under load.

**You need real database privileges.** Creating databases at signup means the app's MySQL user can `CREATE DATABASE` and `DROP DATABASE`. Shared hosting will not give you that, so your hosting floor is now a VPS.

## Choosing

| If this is true | Pick |
|---|---|
| Tenants are small and numerous (thousands of hobby accounts) | shared column |
| Tenants are few, large and paying | database per tenant |
| You must delete or export one customer's data on request, cleanly | database per tenant |
| Your customers are in regulated industries or ask where their data lives | database per tenant |
| You are on shared hosting, or cannot get `CREATE DATABASE` | shared column, no choice |
| You're on PostgreSQL and want a middle ground | schema per tenant |
| You're pre-launch and unsure | shared column, and keep every query behind a repository so you can move later |

That last row matters more than the rest. The migration path from shared to isolated is painful but possible. The path back is worse, so if you genuinely don't know, start cheap and keep your data access in one place.

## What isolation means beyond the database

A database per tenant is only a third of the job. The other two thirds are where the subtle bugs live.

**Files.** Uploads have to land in a per-tenant path. Miss it and tenant B overwrites tenant A's logo because both are called `logo.png`.

**Cache.** Cache keys need a tenant prefix, or one customer's dashboard numbers show up on another's. This bug is silent, it never throws, and you hear about it from the customer.

**Queues.** A job serialised under one tenant must come back under the same tenant. Get this wrong and you'll email the right invoice to the wrong company.

`stancl/tenancy` gives you bootstrappers for all three, and they work, but you should know what they're doing rather than trusting them blindly.

## How we run it

[Ecommerce SaaS](https://marketplace.botble.com/ecommerce-saas) is our platform product, and it sits firmly on database-per-tenant: one MySQL database per store, files under `storage/tenants/tenant{id}`, prefixed cache keys, Laravel 13 with `stancl/tenancy` v3.

A few specifics worth stealing whether you buy it or not:

- **Provisioning is queued by default**, so signup returns immediately. A single-server install can set `TENANCY_PROVISION_SYNC=true` instead and provision inline during signup, in a second or two, with no worker to keep alive.
- **The schedule is per tenant.** A command runs across tenants rather than once globally, which is the part people forget when their first cron only touches the central app.
- **Support access is a 60-second single-use token**, not a shared password, so helping a customer doesn't mean holding their credentials.
- **Cleanup is a first-class job**, because of the 27 orphan databases above. If your platform has no prune step, you already have orphans, you just haven't counted them.

If you want the wider build-or-buy picture rather than the database question alone, I wrote that up separately: [build your own Shopify alternative](https://botble.com/build-your-own-shopify-alternative-a-self-hosted-multi-tenant-store-platform-on-laravel).

## Questions we get asked

**Is database-per-tenant slower?**
Per query, no, usually faster, because each database is small. What costs you is connection churn and the operational work around it.

**How many tenants can one server hold?**
It depends more on activity than count. Hundreds of mostly-idle databases are fine on a decent VPS. A dozen busy stores will ask for more RAM long before the count does.

**Can I move from shared to per-tenant later?**
Yes, and it is a project, not an afternoon. Export per tenant, create the databases, point the app at them, then delete the old tables once you're sure. Do it before you have hundreds of customers.

**Does `stancl/tenancy` handle subdomains and custom domains?**
Yes, both, through domain identification. The harder part isn't the package, it's the wildcard DNS and wildcard TLS certificate in front of it.

**What about migrations across hundreds of databases?**
Run them per tenant, in batches, and make them idempotent. Assume a run will fail partway and that you'll need to resume it, because eventually you will.

**Do I need Redis?**
Not to start. You'll want it once queues and cache matter, which is later than most people think.
