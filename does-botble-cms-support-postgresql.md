---
title: "Does Botble CMS Support PostgreSQL?"
description: "Officially, no - we test on MySQL and MariaDB. But the app layer is pure Eloquent, so PostgreSQL almost works. We went through the codebase and found every MySQL-only line."
categories:
  - Documentation
  - Development
tags:
  - postgresql
  - mysql
  - database
  - botble-cms
  - laravel
  - eloquent
image: https://botble.com/storage/news/does-botble-cms-support-postgresql-hero.jpg
status: published
is_featured: false
---

# Does Botble CMS Support PostgreSQL?

We get this on product pages constantly, and the answer we give in support is "no, use MySQL". Which is true, but it never ends the conversation, because the follow-up is usually sharper than the original question:

> "But why not? If you used the Eloquent model, then it should work with other databases too. Or did you use plain SQL in the code?"

That's a reasonable thing to ask, so we went through the codebase and wrote down every place that isn't database-agnostic.

Turns out the app layer is fine. It's the edges that aren't. Two raw MySQL queries will throw on PostgreSQL, three features quietly stop working, and the installer won't let you pick anything but MySQL.

## What's officially supported

| Database | Status |
|---|---|
| MySQL >= 5.7 | Officially supported and tested |
| MariaDB >= 10.3 | Officially supported and tested |
| PostgreSQL | Not officially supported |
| SQLite | Development only |
| SQL Server | Not supported |

"Officially supported" means we run our test suite against it and our help desk covers it. PostgreSQL gets neither.

## Why it almost works

Botble CMS is a Laravel app and the data layer is Eloquent and the query builder throughout. Schema comes from Laravel migrations, which PostgreSQL reads natively. Nothing at the model level is bound to a MySQL driver.

So a developer can point `DB_CONNECTION=pgsql` at an empty database, run `php artisan migrate --seed`, and get a mostly working site. That's just what you get for using the framework as intended.

Everything that breaks sits outside the ORM: dump and restore tooling, a couple of hand-written aggregate queries, and the install wizard.

## What breaks

### 1. Two raw MySQL queries

These throw a syntax error on PostgreSQL. They're the only two in the stack.

**Blog archive sitemap** - `platform/packages/theme/src/Supports/SiteMapManager.php`

```php
->selectRaw('YEAR(created_at) as created_year, MONTH(created_at) as created_month, ...')
```

`YEAR()` and `MONTH()` are MySQL functions. PostgreSQL wants:

```sql
EXTRACT(YEAR FROM created_at), EXTRACT(MONTH FROM created_at)
```

**Product variation attributes (ecommerce)** - `platform/plugins/ecommerce/src/Models/Product.php`

```php
DB::raw("GROUP_CONCAT(CONCAT(pas.title, ': ', pa.title) ORDER BY pas.order, pa.order SEPARATOR ', ') as variation_attributes")
```

`GROUP_CONCAT` with a `SEPARATOR` clause is MySQL-only. Swap it for `STRING_AGG(expression, ', ')`.

That's the whole list of hard failures. Two queries. Smaller than most people expect when they ask about porting.

### 2. Three features that quietly stop working

These check the driver first, so nothing crashes. They just do nothing.

**Product search relevance ordering.** `ProductRepository` keeps search results in relevance order with `orderByRaw("FIELD(id, $ids)")`, wrapped in a `config('database.default') == 'mysql'` check. On PostgreSQL you still get the products, just in default order. On a big catalogue your customers will notice.

**System information panel.** Database version, charset, collation and max connections under **Admin → Platform Administration → System Information** come from `SHOW VARIABLES`, which PostgreSQL doesn't have. Those rows come back empty.

**`sql_require_primary_key`.** A MySQL 8 session flag we clear before migrations run, so hosts with the strict setting don't reject our pivot tables. Skipped on other drivers. Harmless.

### 3. The installer won't offer PostgreSQL

The install wizard's database dropdown lists MySQL and nothing else. The validation enum behind it already accepts `pgsql` - we just don't expose the option, because we're not going to hand you an install path we don't test.

So you skip the wizard and do it from the command line:

```bash
cp .env.example .env
php artisan key:generate
```

Set your connection:

```dotenv
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=your_database
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

Then build the schema and create your admin user:

```bash
php artisan migrate --seed
php artisan cms:user:create
```

### 4. Backup works, the backup screen doesn't

The backup engine already handles PostgreSQL - it dispatches to `pg_dump` and `pg_restore` next to the MySQL path. The admin panel screen is gated to MySQL and returns "Database driver is not supported".

So you've got the feature, you just can't reach it from the UI. Run it from the console:

```bash
php artisan cms:backup:create
php artisan cms:backup:restore
```

Both take `mysql` and `pgsql`. You'll need the PostgreSQL client binaries installed and on the server `PATH`.

Export and import split the same way, but unevenly: `php artisan cms:db:export` handles `pgsql` through `pg_dump`, while `php artisan cms:db:import` is MySQL-only and refuses to run. Restore with `pg_restore` or `psql` directly.

### 5. One config line in ecommerce

`platform/plugins/ecommerce/config/cart.php` hardcodes the cart storage connection:

```php
'connection' => 'mysql',
```

Override it to `pgsql`. Miss this one and your cart writes to a different connection than everything else, and you'll lose an afternoon working out why carts and sessions disagree.

## So should you do it?

The port is small. Two queries, one config line, one installer bypass. A decent Laravel developer has it running in an afternoon.

Keeping it running is the expensive part. We don't test PostgreSQL, so nothing stops the next release shipping a third raw MySQL query. You re-audit and re-patch on every upgrade, forever. And when something breaks, support can't help - we can't reproduce it, and we won't ship a fix for a setup we don't test.

Our answer hasn't changed: **use MySQL 8 or MariaDB 10.6+** unless PostgreSQL is a hard requirement somewhere above you and you've got a developer to own the patches.

If that's your situation, at least you now know the exact size of the job.

## Reference

We've documented this properly so it stays in sync with the codebase:

**[Database Support - Botble CMS Documentation](https://docs.botble.com/cms/database-support.html)**

Found a MySQL-specific query we missed? Tell us and we'll add it.
