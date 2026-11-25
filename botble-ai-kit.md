---
title: "Botble AI Kit: Teach Claude Code, Codex and Gemini to Write Real Botble Code"
description: "AI assistants know Laravel, not Botble. The kit gives Claude Code, Codex and Gemini the rules we use to build Botble, and makes them check their own work."
categories:
  - Announcements
  - Development
tags:
  - botble-ai-kit
  - claude-code
  - ai-coding
  - botble-plugin
  - laravel
  - botble
image: https://botble.com/storage/news/botble-ai-kit-hero.jpg
status: published
is_featured: false
---

![Botble AI Kit: an AI-written Botble table column corrected in red pen, with the checks it runs before it says done](https://botble.com/storage/news/botble-ai-kit-hero.jpg)

Ask an AI assistant to add a status column to a Botble admin table and you'll usually get something that looks right.

It uses `Column::make('status')`, adds a `renderUsing()` closure, compares the status with `=== 'published'`, and tells you it's done. Then you open the page and every row says "Draft".

Two things went wrong, and neither is the AI being stupid. `Column` silently ignores `renderUsing()`; you need `FormattedColumn`. And `status` on a Botble model isn't a string, it's an enum object, so `=== 'published'` is never true. The assistant knows Laravel very well. It just doesn't know Botble.

We've been using AI coding assistants on our own plugins and themes for most of this year, and that pattern kept repeating. The code compiled, passed a quick look, and was wrong in a way only someone who's built a few Botble plugins would spot. So we started writing the rules down where the assistant would read them. That pile of notes is now the **[Botble AI Kit](https://marketplace.botble.com/ai-kit)**.

## What it actually is

It's a set of **skills**: plain instruction files, plus a few scripts, that your coding assistant reads before it touches Botble code. A skill says things like "tables that format a column use `FormattedColumn`", "compare enums with `->getValue()`", "guard every plugin migration with `Schema::hasTable()`, because plugins get activated and deactivated more than once", and "never load a script from a CDN, Envato will reject the item".

There's no server and no account. The kit sits on your machine and the assistant reads it like it reads your code. Your code doesn't go anywhere it wasn't already going.

It works with three assistants:

- **Claude Code**, as a plugin. You get commands like `/bb:plan`, `/bb:build`, `/bb:debug` and `/bb:review`.
- **OpenAI Codex**, as skills it picks up when the task matches.
- **Gemini and Antigravity**, as workflows (`/bb-plan`, `/bb-build` …).

## The part I care about most: it checks its own work

Telling an assistant the rules helps. Making it prove it followed them helps more.

The build skill doesn't let the assistant say "done" after writing files. For every group of changes it runs the checks we run ourselves: `php -l` on PHP files, Pint for formatting, a Blade linter that catches an unclosed `@if` before it blanks a page, the plugin's PHPUnit tests, and the asset build if it touched JS or SCSS. If something fails, it fixes it first and tells you what it fixed.

The review skill is the one I use most. Point it at your uncommitted changes, a commit, or a whole plugin folder, and it goes through Botble-specific problems: enums compared as strings, missing permission checks on routes, unescaped output in Blade, migrations that crash on re-activation, hard-coded English strings, and the list of things that get a CodeCanyon item soft-rejected.

## What's inside

Nineteen skills and six specialist agents. Roughly:

- **The workflow**: plan, build, debug, review. Plan turns "add a booking module to my plugin" into a one-page list of files, migrations, permissions, translations and tests before any code gets written. Debug looks for the cause first, using about thirty causes we've actually hit, instead of trying fixes at random.
- **How Botble works**: plugins, themes, ecommerce, admin forms and settings pages, tables and dashboard widgets, the REST API, media and storage drivers, SEO and slugs, multilingual content, assets and admin JavaScript, seeders, translations, and running a site in production.
- **Troubleshooting**: symptom, cause, fix. Installs, upgrades, emails that don't send, images that vanish after a domain move, checkout totals that look wrong.
- **Agents** for the bigger jobs: scaffolding a plugin with full CRUD, writing migrations that are safe to re-run, building theme shortcodes end to end, adding a payment gateway, and auditing a theme before you submit it to Envato.

While writing it we checked every class, hook and command against the Botble 7.6 source code. We found a few places where even our own documentation had drifted from the code. The kit follows the code.

## Who it's for

If you build client sites on Botble, customise themes and plugins for a living, or sell your own items on CodeCanyon, this is for you. It's most useful when you already use an AI assistant and keep catching it writing generic Laravel where Botble has its own way.

If you've never used Claude Code or Codex, you can still start here, but expect to spend an evening getting used to the assistant itself. The kit makes it better at Botble. It doesn't make it magic.

## Getting started

Unzip the kit somewhere permanent. For Claude Code:

```text
/plugin marketplace add ~/botble-ai-kit/claude-code
/plugin install bb@botble-ai-kit
/reload-plugins
```

Then try something small in a Botble project:

```text
/bb:plan add a settings page to my plugin with an API key and an on/off switch
```

For Codex, run `./install.sh` in the `codex` folder. For Gemini or Antigravity, run `./install.sh /path/to/your/project` in the `gemini` folder. The README in the zip covers updating and removing it.

## Where it will annoy you

It makes the assistant slower. Running Pint, the Blade linter and the tests after every step takes time, and for a one-line copy change that's overkill. You can tell it to skip the plan for small things, and it will.

It also won't fix a bad request. "Make the shop better" still gets you a vague plan. "Add a minimum order amount to checkout, with a setting and a message on the cart page" gets you something you can review in ten minutes.

And AI assistants still make mistakes, with or without the kit. It cuts out the Botble-specific ones we kept seeing, and makes the assistant check its work, but you should still read the diff before you ship it.

## Price

**$49** for one developer, or **$149** for a team of up to five. That's a one-time payment with 12 months of updates, and you keep the version you have after that. Buyers also get access to the private GitHub repository, which is where updates land first.

You can use it on as many projects as you like, including client work. You just can't share the kit itself.

It's on the [Botble AI Kit page](https://marketplace.botble.com/ai-kit), with examples of what changes when the assistant has the kit loaded.

If you're still choosing what to build on, start with [what Botble CMS is and why it suits vibe coding](https://botble.com/what-is-botble-cms-the-laravel-cms-thats-built-for-vibe-coding), our [Laravel CMS comparison](https://botble.com/laravel-cms-2026), or the roundup of [Laravel ecommerce scripts](https://botble.com/best-laravel-ecommerce-scripts-in-2026-top-6-ranked-compared). The kit is for the part after that: building on Botble without reading half the source first.
