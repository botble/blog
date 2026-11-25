---
title: "Botble AI Kit: Teach Claude Code, Codex and Gemini to Write Real Botble Code"
description: "Describe the plugin you need and get a working, tested Botble plugin you can ship to a client or sell on marketplace.botble.com. For Claude Code, Codex and Gemini."
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

A client asks for a booking plugin for their spa: services, staff, time slots, a deposit at checkout, and a reminder email the day before. On a normal week that's three or four days of work before anyone tests it.

With the **[Botble AI Kit](https://marketplace.botble.com/ai-kit)** loaded, you describe that in a paragraph. The assistant comes back with a plan: the database tables, the admin screens, the permissions, the settings page, the emails, the translations, and the tests it will write. You read it, cut what you don't need, and approve it. Then it builds the plugin one piece at a time, checks each piece, and tells you when something failed and how it fixed it. By the end of the afternoon you're clicking through a working plugin in your own admin panel, not reading a wall of generated code and hoping.

That's the job we built the kit for.

## Why plain AI assistants get Botble wrong

AI assistants know Laravel very well. They don't know Botble. So they write code that looks fine and breaks in ways only someone who has shipped a few Botble plugins would spot: a status column that shows "Draft" on every row, a plugin that crashes the second time you activate it, a settings page that loses its values, text that can't be translated, a theme that gets soft-rejected on CodeCanyon because it loads a script from a CDN.

We've been building our own plugins and themes with AI assistants for most of this year and kept fixing the same mistakes by hand. So we wrote the rules down where the assistant reads them before it writes a line. The kit is those rules, plus the checks we run before we ship anything.

## From idea to marketplace

This is the whole loop, and you can stop at any step:

1. **Describe it** in plain words, the way you'd brief a developer.
2. **It plans**: files, database, admin screens, permissions, settings, translations, tests. You approve or change the plan.
3. **It builds** the plugin step by step, following the conventions the Botble team uses in its own products.
4. **It tests**: PHP checks, code style, template checks, automated tests. Whatever fails, it fixes before moving on.
5. **It reviews** like a senior Botble developer would: security, permissions, whether it survives an upgrade or a re-activation, and the CodeCanyon review list.
6. **It fills in the rest**: demo data that looks real, translations into the languages you need, and the docs.
7. **You ship it**: to your client, or zip it and upload it to [marketplace.botble.com](https://marketplace.botble.com) to sell or share with other Botble users.

## What people build with it

A few of the jobs we've been running through it, and what you get back:

- **A booking plugin for a client site.** "Spa booking with services, staff, time slots and a deposit at checkout." You get the admin screens, a calendar view, the deposit wired into Botble's checkout, reminder emails, and tests.
- **A local payment gateway.** "Add a bank QR payment method to my store, with webhook confirmation and refunds." You get a gateway that shows up in the payment settings like the built-in ones, handles the callback safely, and doesn't mark an order paid twice.
- **A product import.** "Import 5,000 products from the supplier's Excel file, with images and categories." You get an importer that runs in batches, skips duplicates, and tells you which rows failed and why.
- **An HTML template turned into a theme.** "Convert this template into a Botble theme." You get editable homepage sections, theme options for colours and logos, and demo content, instead of a hard-coded copy of the HTML.
- **Something broke after an update.** "The checkout total is wrong since I updated Botble." It looks for the cause first, from a list of real causes we've hit, before it touches anything, so you don't end up with three new bugs.
- **Getting a plugin ready to sell.** "Review my plugin before I upload it." You get a list of what would get it rejected, fixes for each, translations, and demo data for the screenshots.

None of these are one prompt and done. You still read the plan, try the result, and push back when it's wrong. What changes is how much of the Botble-specific work you have to know and remember yourself.

## We build our own plugins this way

This isn't a kit we wrote for other people and never used. For the last six months our team has planned, built, tested and reviewed the Botble plugins we sell with these same skills. Between late May and late November we shipped **4 new plugins** and **98 releases across 28 Botble and FriendsOfBotble plugins**, every one of them through plan, build, test and review.

The new ones:

- **[Landing Funnel](https://marketplace.botble.com/products/botble/landing-funnel)** (June): single-product ad landing pages with a one-page checkout for Botble Ecommerce. It has had 14 releases since.
- **[BB Popup](https://marketplace.botble.com/products/botble/bb-popup)** and **[BB Store Locator](https://marketplace.botble.com/products/botble/bb-store-locator)** (August): popups with triggers, targeting and conversion stats, and a store locator that works with Google Maps or OpenStreetMap, no API key needed.
- **[Website Translator](https://marketplace.botble.com/products/botble/website-translator)** (October): translates a whole Botble site into 45 languages with a flag switcher.

And the ones that kept moving: [Live Chat](https://marketplace.botble.com/products/botble/live-chat) had 12 releases, [Loyalty Points](https://marketplace.botble.com/products/botble/loyalty-points) 11, [Wholesale](https://marketplace.botble.com/products/botble/ecommerce-wholesale) 7 and [E-Wallet](https://marketplace.botble.com/products/botble/e-wallet) 6. Most of those releases are small and unglamorous: an edge case a customer reported, planned, fixed with a test, and reviewed before it goes out.

You can see all of them, with their changelogs, on [marketplace.botble.com/plugins](https://marketplace.botble.com/plugins).

## Does it make the code bug-free?

No, and I'd be wary of anything that says it does. AI assistants still make mistakes. What the kit changes is that the assistant checks its own work the way we check ours, so most of the Botble-specific mistakes get caught and fixed before you ever see them. Read the changes before you ship them. You'd do that with a new developer on the team too.

## What's inside

Twenty skills and six specialist agents, for Claude Code, OpenAI Codex and Gemini / Antigravity:

- **The workflow**: plan, build, debug, review. In Claude Code they're `/bb:plan`, `/bb:build`, `/bb:debug` and `/bb:review`; or just describe the task and the right one loads.
- **How Botble works**: plugins, themes, ecommerce, admin forms and settings, tables and dashboards, the API, media, SEO, multilingual sites, seeders, translations, and running a site in production.
- **Troubleshooting**: installs, upgrades, emails that don't send, images that vanish after a domain move, checkout totals that look wrong.
- **Agents** for the bigger jobs: scaffolding a full plugin, writing safe database changes, building theme sections, adding a payment gateway, and auditing an item before you submit it.

It all runs on your machine. There's no server and no account, and your code doesn't go anywhere it wasn't already going.

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

## Price

**$49** for one developer, or **$149** for a team of up to five. That's a one-time payment with 12 months of updates, and you keep the version you have after that. Buyers also get access to the private GitHub repository, which is where updates land first.

You can use it on as many projects as you like, including client work. You just can't share the kit itself.

It's on the [Botble AI Kit page](https://marketplace.botble.com/ai-kit), with examples of what changes when the assistant has the kit loaded.

If you're still choosing what to build on, start with [what Botble CMS is and why it suits vibe coding](https://botble.com/what-is-botble-cms-the-laravel-cms-thats-built-for-vibe-coding), our [Laravel CMS comparison](https://botble.com/laravel-cms-2026), or the roundup of [Laravel ecommerce scripts](https://botble.com/best-laravel-ecommerce-scripts-in-2026-top-6-ranked-compared). The kit is for the part after that: building on Botble without reading half the source first.
