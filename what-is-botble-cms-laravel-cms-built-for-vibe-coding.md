---
title: "What Is Botble CMS? The Laravel CMS That's Built for Vibe Coding"
description: "A complete guide to Botble CMS - a modular Laravel 13 content management system with full source code, 24 bundled plugins, a CRUD generator, and conventions that make AI coding assistants like Claude Code actually productive."
categories:
  - Documentation
  - Development
tags:
  - botble-cms
  - laravel
  - php
  - cms
  - vibe-coding
  - ai
  - claude-code
image: https://landing.botble.com/botble/images/admin-dashboard.png
status: published
is_featured: true
---

# What Is Botble CMS? The Laravel CMS That's Built for Vibe Coding

![Botble CMS admin dashboard](https://landing.botble.com/botble/images/admin-dashboard.png)

Botble CMS is a modular content management system built on Laravel. It hands you a finished admin panel on day one: pages, blog, media library, menus, roles and permissions, SEO, multilingual content, themes and plugins. Underneath it's still a plain Laravel codebase that you own outright and can change however you like.

We shipped the first version in 2016. Ten years on, it's the base for 47 production-ready scripts in our own catalog, it has over 500 five-star ratings on CodeCanyon at a 4.92 average, and it ships as unencrypted PHP. No ionCube, nothing compiled.

The current release runs on Laravel 13 and PHP 8.3 or newer.

This post walks through what you actually get, and then spends a good chunk of time on something we didn't plan for: the codebase turns out to be a very comfortable place to work with an AI assistant like Claude Code, Cursor, or Copilot.

## What this guide covers

- What Botble CMS is, and who it's for
- The modular architecture: core, packages, plugins, themes
- A feature tour of the admin panel and content tools
- Developer tooling: generators, form and table builders, hooks
- Why it works well for vibe coding, with eight specific reasons
- The product ecosystem, installation, licensing, and FAQ

## Botble CMS in one paragraph

Botble CMS is a Laravel application, not a framework wrapped around a framework. Routing, Eloquent, Blade, queues, Artisan: all standard. What we add is a `platform/` directory holding the CMS itself, split into core modules, packages, plugins, and themes. Each module is a self-contained Composer package with its own migrations, routes, models, service providers, and assets. Turn on what you need, delete what you don't, and write your own modules against conventions that are written down.

Live demo: [cms.botble.com](https://cms.botble.com), admin area at `/admin`, login `admin` / `12345678`.

## Who Botble CMS is for

| You are | What Botble CMS gives you |
|----------|---------------------------|
| A Laravel developer building client sites | A finished admin panel, so you skip two or three months of CRUD, auth, media, and permission work |
| An agency shipping many similar projects | A plugin and theme system that turns per-client work into something reusable |
| A startup founder | 47 ready-made scripts (eCommerce, real estate, hotel, job board, news) to launch with and customize later |
| Someone vibe coding with an AI assistant | A predictable, documented, unencrypted codebase that agents can read and extend correctly |

If you're after a hosted no-code website builder, this isn't it. Botble assumes you have PHP hosting and can run a Composer install, or at minimum upload files and follow a web installer.

## What's inside: the modular architecture

Everything CMS-related lives under `platform/`:

```
platform/
├── core/       # ACL, base, dashboard, media, setting, table, chart, icon, js-validation, support
├── packages/   # menu, page, theme, widget, shortcode, slug, seo-helper, sitemap,
│               # revision, optimize, installer, get-started, plugin-management
├── plugins/    # blog, contact, gallery, faq, member, newsletter, social-login,
│               # backup, audit-log, request-log, language, translation, analytics,
│               # captcha, cookie-consent, custom-field, simple-slider, block,
│               # testimonial, location, note, rss-feed, fob-comment, language-advanced
└── themes/     # your frontend themes
```

That's 10 core modules, 13 packages, and 24 bundled plugins in the base CMS. They all follow the same internal layout (`src/Models`, `src/Http/Controllers`, `src/Forms`, `src/Tables`, `src/Providers`, `database/migrations`, `resources/lang`, `routes/`), which is documented in the [plugin structure guide](https://docs.botble.com/cms/plugin-structure.html).

That sameness matters more than it sounds. Read one plugin and you can read all of them.

## Feature tour

### Admin panel

Built on [Tabler UI](https://docs.tabler.io/ui) with Bootstrap 5 and 4,200+ Tabler icons. It's responsive, and you can configure color schemes, switch between vertical and horizontal navigation, adjust container width, and run RTL. The dashboard shows recent content, Google Analytics data, and error logs.

![Plugin management](https://landing.botble.com/botble/images/admin-plugins.png)

### Content management

- Pages with a template system
- Blog with categories, tags, and authors, plus threaded comments through the bundled FOB Comment plugin
- Drag-and-drop menu builder
- Widgets you can drop into any sidebar area your theme registers
- Shortcodes: reusable content blocks with options editable from the admin ([docs](https://docs.botble.com/cms/shortcode.html))
- Theme options, so non-technical site owners can change things without touching code

![Theme options](https://landing.botble.com/botble/images/admin-theme-options.png)

### Media library

Categories, tags, bulk operations, chunked upload, image cropping, watermarking, and thumbnail generation through GD or Imagick. Storage drivers cover local disk, Amazon S3, DigitalOcean Spaces, Cloudflare R2, Wasabi, BunnyCDN, and Backblaze B2.

### Users, roles, and permissions

Define roles and assign permissions per module and per action. There's an Artisan command to rebuild the whole permission tree when you add new modules. Team management and activity logging come with it, the latter through the audit-log plugin.

### Multilingual and RTL

Two systems that work independently. One handles interface translations, with 42+ languages shipped. The other handles multilingual content, so you can translate posts, pages, and any model you register. RTL layouts are supported in both the admin and themes.

### SEO

The `seo-helper` package manages meta titles and descriptions, Open Graph tags, Twitter cards, and canonical URLs for any model you hook up. The `sitemap` package writes XML sitemaps for you. Slugs get their own package, with per-model URL prefixes.

### REST API

Sanctum token authentication, routes versioned under `/api/v1`, an optional `X-API-KEY` header for a second layer of protection, and server-side token management from the admin panel. Browsable API docs are optional: install Scribe with `composer require knuckleswtf/scribe`, then run `php artisan scribe:generate`. More in the [API docs](https://docs.botble.com/cms/api.html).

### Operations

- System updater for one-click updates from the admin panel
- Backup plugin for database and file backups, restorable from the UI
- Request log to catch 404s and errors
- Cache management and public cache-control headers
- Cron job support for scheduled tasks

## Developer tooling

### Generators

You rarely write boilerplate by hand:

```bash
# Scaffold a whole plugin
php artisan cms:plugin:create my-plugin

# Generate a full CRUD module inside it: model, migration, form, table,
# controller, routes, requests, permissions
php artisan cms:plugin:make:crud my-plugin my-module

# Scaffold a theme, a widget, a package
php artisan cms:theme:create my-theme
php artisan cms:widget:create my-widget
php artisan cms:package:create my-package
```

The [commands reference](https://docs.botble.com/cms/commands.html) has the full list.

### Declarative forms

Forms are PHP classes, not Blade soup:

```php
$this->setupModel(new MyModel)
    ->setValidatorClass(MyRequest::class)
    ->columns(12)
    ->add('name', TextField::class, NameFieldOption::make()->colspan(8)->required()->toArray())
    ->add('status', SelectField::class, StatusFieldOption::make()->colspan(4)->toArray());
```

There are 35+ field types: rich text editors, media pickers, date and time pickers, color pickers, repeaters, AJAX autocomplete, tree category selectors, and so on. See the [form builder docs](https://docs.botble.com/cms/form-builder-get-started.html).

### Declarative tables

```php
$this->model(MyModel::class)
    ->addColumns([
        IdColumn::make(),
        NameColumn::make()->route('my-plugin.edit'),
        StatusColumn::make(),
        CreatedAtColumn::make(),
    ]);
```

Sorting, filtering, search, bulk actions, and export come with it. See the [table builder docs](https://docs.botble.com/cms/table-builder.html).

### Hooks

An action and filter system, similar in spirit to WordPress, lets plugins extend each other without anyone editing core files:

```php
add_filter(BASE_FILTER_BEFORE_RENDER_FORM, function ($form, $data) {
    if ($data instanceof \Botble\Blog\Models\Post) {
        $form->add('reading_time', TextField::class, ...);
    }

    return $form;
}, 120, 2);
```

See [actions](https://docs.botble.com/cms/actions.html) and [filters](https://docs.botble.com/cms/filters.html).

### Quality tooling

The repo comes with Laravel Pint (PSR-12), Larastan and PHPStan, Rector, PHPUnit 12, and a git commit checker already configured. Assets build with Vite through per-module build descriptors.

## Why Botble CMS is great for vibe coding

Vibe coding means describing what you want and letting an AI agent write it. Whether that works comes down to how well the model can read and predict your codebase. Botble happens to do well on most of what matters, and honestly that's a side effect of decisions we made long before AI assistants were a thing.

### 1. You can read all of the source code

There's no ionCube and no obfuscation, so every line of PHP under `platform/` is plain text an agent can grep and learn from. Products that ship encrypted admin panels are basically invisible to an AI assistant. The model has nothing to go on except guesswork about the API surface.

### 2. The structure repeats, so context stays cheap

Every plugin has the same shape. Ask an agent to add a Testimonials module and it can open one existing plugin, say `platform/plugins/faq`, and work out the whole pattern from there: where models go, how the service provider registers permissions and menu entries, how migrations are named, where translations live. One small read does the job of a repo-wide scan, and the context window stays free for the actual work.

### 3. Declarative builders leave less room for mistakes

Writing a raw Laravel admin means inventing a Blade form, wiring validation, building a DataTable, handling sorting and pagination, and remembering CSRF. That's a few hundred lines, and plenty of them can be subtly wrong.

The Botble version is about 15 lines of field and column definitions. Less code means fewer opportunities to hallucinate, and you can review it properly in under a minute.

### 4. There's an official AI Assistant Guide

We maintain a documentation page written for models rather than people: the [AI Assistant Guide](https://docs.botble.com/cms/ai-assistant-guide.html). It covers naming conventions, model rules, the translation systems, form and table patterns, security requirements, and the specific mistakes AI assistants keep making in this codebase.

Save it into your project as `CLAUDE.md`, `AGENTS.md`, `.cursorrules`, or a Copilot instructions file, and your assistant stops writing generic Laravel.

Two of the traps it heads off:

```php
// Botble uses a custom Enum class, not PHP 8.1 native enums.
// Wrong: always false, because you're comparing an object to a string
if ($model->status === BaseStatusEnum::PUBLISHED) { }

// Right
if ($model->status->getValue() === BaseStatusEnum::PUBLISHED) { }
```

```php
// Wrong: Column::make() ignores renderUsing() without complaining
Column::make('price')->renderUsing(fn ($col, $value) => format_price($value));

// Right: FormattedColumn handles custom rendering
FormattedColumn::make('price')->formatted(fn ($value) => format_price($value));
```

Both of these fail silently. The code runs, nothing errors, and the output is wrong. That's exactly the kind of bug an assistant produces and a reviewer skims straight past, which is why they're written down.

### 5. Laravel Boost is already set up

The CMS ships Laravel Boost as a dev dependency, with a `boost.json` that already lists `claude_code`, `codex`, `opencode`, and `phpstorm`. Boost exposes your application's live state to agents over MCP: routes, models, config, database schema, logs. Your assistant can look at what's actually there instead of inferring it from files.

### 6. Generators give agents a safe starting point

The workflow that works best here is to let the generator build the skeleton and let the AI handle the domain logic.

```
You:   Create a "Job Listings" plugin with title, company, location,
       salary range, description, and a published status.

Agent: php artisan cms:plugin:create job-listings
       php artisan cms:plugin:make:crud job-listings job
       ...then edits the generated form, table, model, and migration
```

Whatever the generator produces is correct by construction: right namespaces, right service provider bindings, right permission registration. The AI only writes the parts that are actually specific to your project, which is the part it's good at.

### 7. You can verify the output

Vibe coding without verification is just gambling. The checks are already configured, so an agent can grade its own work before handing it back:

```bash
vendor/bin/pint                  # format to PSR-12
composer analyse                 # PHPStan / Larastan static analysis
php artisan test                 # PHPUnit
```

Give your agent those three commands plus an instruction not to stop until they pass. The quality difference is obvious.

### 8. You can teach your assistant once and reuse it

Beyond the AI Assistant Guide, you can turn Botble conventions into reusable skills covering plugin development, theme development, code review, and marketplace compliance. There's a full walkthrough in [How to Build Claude Code Skills for Botble CMS Development](https://botble.com/how-to-build-claude-code-skills-for-botble-cms-development).

### A few honest caveats

- PHP 8.3 and Laravel 13 are recent enough that models trained on older data will suggest deprecated patterns. The AI Assistant Guide fixes most of that, and a glance at `composer.json` catches the rest.
- Our custom Enum class really is unusual. If you skip the guide, expect to correct it.
- `platform/` is big. Point your agent at specific plugins rather than letting it wander the whole tree.

## The product ecosystem

Botble CMS is the foundation. On top of it we build and sell complete systems:

| Category | Products |
|----------|----------|
| eCommerce | SnapCart, Shofy, Farmart, Nest, Wowy, Ninico, HASA, Shopwise, Amerce |
| Real estate | Homzen, Flex Home |
| News and magazine | Athena, Stories, LaraMag |
| CMS and corporate | Botble, Cloudify, Zelio, Infinia, Gerow |
| Booking and services | Miranda (hotel), Carento (car rental), Travlla (tours), Velura (spa), Restoria (restaurant) |
| Job board | Jobcy |
| Plugins | Live Chat, KYC Verification, SMS Gateways, Wholesale B2B, Product Gifts, Loyalty Points, License Manager, and more |

They all share the same core, the same conventions, and the same documentation, so what you learn on one carries over to the rest. The full catalog is at [marketplace.botble.com/portfolio](https://marketplace.botble.com/portfolio).

## Installing Botble CMS

### Requirements

- PHP 8.3 or newer (8.4 works)
- MySQL 5.7+ or MariaDB 10.2+
- Extensions: `pdo`, `openssl`, `mbstring`, `exif`, `fileinfo`, `xml`, `ctype`, `json`, `tokenizer`, `curl`, `zip`, `iconv`, `gd`, `bcmath`
- `mod_rewrite` enabled on Apache
- `memory_limit = 256M` and `max_execution_time = 300`

Full list in the [installation requirements](https://docs.botble.com/cms/installation-requirements.html).

### Three ways to install

Web installer: upload the files, open your domain, follow the wizard. No terminal needed.

Command line:

```bash
php artisan cms:install
```

It's interactive. It runs migrations, creates a super user, activates plugins, optionally seeds demo data, and publishes assets.

Composer:

```bash
composer create-project botble/cms your-project
cd your-project
php artisan cms:install
```

Docker works too, using the included `docker-compose.yml`.

## Licensing and pricing

Products are sold under the Envato Regular or Extended license. The difference is your charging model, not the features you get:

- Regular, if your site doesn't charge end users for access or services.
- Extended, if you do charge them, whether that's subscriptions, SaaS, or commission per sale. You can start on Regular and upgrade later.

The terms worth knowing:

- One license covers one website: one domain, subdomain, subfolder, or IP. More sites need more licenses.
- Lifetime updates, and six months of support that you can extend.
- Development, staging, and UAT environments don't need activation. Everything works unactivated except the System Updater and installing plugins from the Botble Marketplace.
- Every purchase includes the full unencrypted source code.

Activate under `Settings` -> `General` in the admin panel, or from the CLI:

```bash
php artisan cms:license:activate <license-key>
```

You can buy on CodeCanyon, or buy direct from us at 30% off with free installation at [marketplace.botble.com/portfolio](https://marketplace.botble.com/portfolio). Full terms are in the [license documentation](https://docs.botble.com/cms/license.html).

## FAQ

**Is Botble CMS free?**
No. It's a commercial product with a one-time fee per website, lifetime updates, and six months of support. No subscription, nothing recurring.

**Do I get the source code?**
Yes, all of it, unencrypted. No ionCube, no obfuscation. Read it, change it, extend it.

**Which Laravel version does it use?**
Laravel 13, on PHP 8.3+. We follow Laravel releases closely and push upgrades through the built-in system updater.

**Is it a WordPress alternative?**
For developers, yes. If your team writes PHP and would rather live in Laravel's ecosystem (Eloquent, queues, Blade, Composer packages), Botble gives you comparable content management without WordPress's older architecture. If you need thousands of third-party plugins and no code at all, WordPress still wins on catalog size.

**Can I use it with AI coding assistants?**
Yes, and that's half of what this article is about. Load the [AI Assistant Guide](https://docs.botble.com/cms/ai-assistant-guide.html) into your assistant's context, use Laravel Boost's MCP integration, and let the generators handle scaffolding.

**Can I customize the code and still receive updates?**
Yes, with some care. Keep your changes in your own plugin or a child theme instead of editing `platform/core`. Updates run through the in-app System Updater, `php artisan cms:update`, or a manual file replacement. See the [upgrade guide](https://docs.botble.com/cms/upgrade.html).

**Does it support multiple languages?**
Yes: 42+ interface translations, multilingual content for any model, and RTL layouts.

**Is there an API?**
Yes. REST with Sanctum token auth, versioned routes under `/api/v1`, and optional API key protection. Add browsable docs by installing Scribe.

## Where to go next

- Try the demo: [cms.botble.com](https://cms.botble.com) with `admin` / `12345678`
- Read the docs: [docs.botble.com/cms](https://docs.botble.com/cms/)
- Browse the catalog: [marketplace.botble.com/portfolio](https://marketplace.botble.com/portfolio)
- Feed your AI assistant: [AI Assistant Guide](https://docs.botble.com/cms/ai-assistant-guide.html)
- Get support: [botble.ticksy.com](https://botble.ticksy.com)

We built Botble CMS so we'd stop rebuilding the same admin panel for every client. The things that made it reusable for us (consistent structure, declarative APIs, conventions written down, nothing hidden) are the same things that make it easy for an AI assistant to work in. We didn't plan that part, but we'll take it.

Questions about any of this? Send them over, I read every message.

Thanks,
Sang, Botble Technologies
