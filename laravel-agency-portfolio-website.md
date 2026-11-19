---
title: "How to Build an Agency or Portfolio Website with Laravel in 2026"
description: "Six ways to build a studio or portfolio site, from Webflow to a ready-made Laravel script: what each costs in year one, who can edit it, and what you own when you leave."
categories:
  - Development
  - Design
tags:
  - portfolio-script
  - creative-agency-theme
  - laravel-cms
  - agency-website
  - webflow-alternative
  - orisa
  - norio
  - botble-cms
image: https://botble.com/storage/news/laravel-agency-portfolio-website-hero.jpg
status: published
is_featured: false
---

An agency website has a strange brief. It has to look good enough to win work, then get out of the
way so the work is what people remember. Plenty of studio sites get the first half and forget the
second: the site is louder than the projects on it.

The tooling question comes before any of that, and it's where most people lose a week. Webflow or
WordPress? A Laravel CMS? Build it yourself because you're a developer and it's "only a portfolio"?

We sell Laravel themes for agencies and freelancers, so read this knowing which way I lean. But
I've also watched people buy the wrong thing for their situation, and that costs everyone. So this
is the honest version: six routes, what each one costs in the first year, who can actually edit
the site day to day, and what you still own if you walk away.

![Agency and portfolio websites on Laravel: three theme demos pinned up like proof prints](https://botble.com/storage/news/laravel-agency-portfolio-website-hero.jpg)

## What an agency site actually needs

Before comparing tools, it helps to be blunt about the job. A studio or portfolio site needs:

- **Project pages that carry big images without crawling.** This is the whole site. If the case
  studies load slowly, nothing else matters.
- **A way for a non-developer to add a project.** The designer who finished the work should be able
  to publish it on a Friday afternoon without filing a ticket.
- **Services, about, team and a contact or brief form.** Boring, necessary, and the pages that
  actually convert.
- **Basic SEO**: titles, descriptions, a sitemap, clean URLs.
- **Maybe a second language.** More studios need this than expect to.

And it usually doesn't need a shop, memberships, or a custom permission system. Keep that list in
mind, because a lot of the cost in the options below is paying for things on the second list.

## The six routes

### 1. A hosted builder: Webflow or Framer

This is where most design-led studios start, and for good reason. You design visually, you
publish, hosting is somebody else's problem.

The catch is the meter. On [Webflow's pricing page](https://webflow.com/pricing) (checked 19
November 2026), a site that needs a CMS, which a portfolio does, sits on the **Premium** plan
**from $25 a month billed yearly** ($39 monthly), and the price rises with bandwidth. Team members
are billed on top through workspace plans and seats. [Framer](https://www.framer.com/pricing) is
cheaper to start, **$10 a month for Basic and $30 for Pro**, with extra editors at $20 each.

The part that bothers me more than the price is the exit. Webflow can export static HTML and CSS,
but its own page says CMS content "can't be exported" as working code. Framer says plainly it
[doesn't offer HTML export for self-hosting](https://www.framer.com/help/articles/can-i-export-my-website-to-html-and-self-host-it/).
If you stop paying, you rebuild.

**Pick it if** the people running the site are designers, nobody wants to touch a server, and a
yearly bill per site doesn't bother you.

### 2. WordPress, a premium theme and a page builder

Still the default, and it works. A self-hosted WordPress install, a portfolio theme from
ThemeForest, usually Elementor Pro on top.

The theme is cheap: the best-selling portfolio themes there cost **$29 to $99** one-off, most of
them $59 to $79. Elementor Pro's single-site plan is **$60 a year**. Then come the premium plugins
the theme bundles or expects, each with its own renewal.

Where it will annoy you: three bills on three schedules, and an update order you have to respect
(theme, builder, bundled plugins) or the layout breaks. Check the "last update" date before you buy,
too. A few of the long-time best-sellers in that category haven't been updated in years.

**Pick it if** your team already lives in WordPress. If you're thinking about leaving it, we wrote
up [what comes across when you move a WordPress site to Laravel](https://botble.com/wordpress-to-laravel-migration).

### 3. Statamic, a Laravel CMS you design on

[Statamic](https://statamic.com/pricing) is the nicest content editing experience in the Laravel
world, and I mean that. Content is stored in flat files by default, so the whole site can live in
Git.

**Core is free** but allows a single admin user. The moment the client wants a second editor, or
revisions, or a second language, you need **Pro at $349 per site, then $99 a year** for updates.

The bigger point: Statamic gives you a CMS, not a website. Every template is yours to design and
build. For a developer building one client site at a time, that's fine and often the point. For a
three-person studio that wants to be live this month, it's the long way round.

### 4. October CMS

October is older and less fashionable, but it's a real Laravel CMS with a theme and plugin
marketplace. [Its pricing](https://octobercms.com/pricing) is **$39 per site, including a year of
updates, then $39 a year**. There's one free licence, and an unlimited subscription aimed at
agencies.

One thing to know before you start: if a licence lapses, you lose access to the update gateway,
including re-downloading the core. The ecosystem is also much smaller than WordPress's or the
wider Laravel world's, so for anything unusual you'll be writing it.

### 5. Build it yourself on Twill or Filament

If you're a Laravel developer, this is the tempting one. [Twill](https://twillcms.com/) (AREA 17,
free, Apache-2.0) is a toolkit for building a custom admin, with a good media library and
revisions. Note its UI licence asks you to keep "Made with Twill" in the admin footer unless AREA 17
agrees otherwise. [Filament](https://filamentphp.com/) is MIT and excellent at admin panels.

Neither one gives you a public website. With Filament especially, you still build every front-end
page, the responsive layouts, menus, a page or block model, SEO meta and sitemap, forms, a blog,
and multilingual routing. That's not a weekend. It's a product.

I'd only go this way if the site has an unusual data model, say a production company tracking
crews, equipment and shoot dates, where no CMS fits. For a portfolio, the licence is free and the
time is not.

### 6. A ready-made Laravel script

This is the category we're in, so I'll describe it plainly. You buy a complete site: the front end,
an admin panel, demo content, and a web installer. You upload it to ordinary hosting, open your
domain, and you have a finished studio site to fill with your own work. It's Laravel underneath,
so a developer can extend it later without fighting the code.

The cost is a one-off licence per site with free updates afterwards. Ours are listed below. The
trade-off is design freedom: you start from someone else's layout. Most of these themes have enough
homepage variants and block styles that the result doesn't look like the demo, but if your studio's
identity depends on a layout nobody has built yet, routes 3 and 5 exist for that.

## Year-one cost, side by side

For one studio site, checked 19 November 2026. Hosting is extra for every self-hosted route.

| Route | Year one | After that | Self-hosted | You own the code |
|---|---|---|---|---|
| Webflow (Premium, yearly) | from $300 + seats | same again | No | Static export only, not the CMS |
| Framer (Pro) | $360 | same again | No | No |
| WordPress + theme + Elementor Pro | ~$69 + $60 | $60/yr + plugins | Yes | Yes (theme is licensed) |
| Statamic Pro | $349 | $99/yr | Yes | Yes |
| October CMS | $39 | $39/yr | Yes | Yes |
| Twill / Filament / scratch | $0 licence | $0 | Yes | Yes, all of it |
| Laravel script (ours, direct) | $20.30 to $34.30 | $0, updates included | Yes | Yes |

The cheap rows aren't automatically the right ones. A Webflow bill is cheaper than a developer's
month. But the table does show where the money goes over three years, which is a question worth
asking before you commit.

## Which of our themes fits which studio

We have several, and they're not interchangeable. The short version:

| Theme | Built for | What stands out | Direct price |
|---|---|---|---|
| [Orisa](https://marketplace.botble.com/portfolio/orisa) | Creative, digital, marketing agencies | 15 homepage demos, magic cursor, shop pages if you need them | $27.30 |
| [Norio](https://marketplace.botble.com/portfolio/norio-creative-digital-agency-portfolio-laravel-script) | Design studios and freelancers | 3 homepages in light and dark, 5 project layouts | $20.30 |
| [Infinia](https://marketplace.botble.com/portfolio/infinia) | Agencies that sell services to businesses | 5 homepages, more corporate than creative | $34.30 |
| [Zelio](https://marketplace.botble.com/portfolio/zelio) | One person: developer, designer | Portfolio plus resume, 3 homepages | $27.30 |
| [Isak](https://marketplace.botble.com/portfolio/isak) | One person who wants it calm | Single page, 8 sections, 3 colour variants | $20.30 |

Those are our direct prices, 30% under the CodeCanyon list. [Why we price direct lower](https://botble.com/buy-botble-products-direct-and-save-30-vs-codecanyon).

### Orisa: when the agency is the brand

Orisa is the loud one, deliberately. Version 1.0.8 added ten homepages, so there are fifteen now:
creative, digital, marketing, AI and tech, personal, branding, startup, 3D, motion, architecture,
photography, fashion and more. It has dark and light mode that follows the visitor's system
setting, RTL support, and a physics-driven cursor. It also ships product, cart and checkout pages,
which is unusual for an agency theme and handy if the studio sells prints or templates on the side.

![Orisa's Creative Agency homepage: an orange hero with WE CREATE DIGITAL EXPERIENCES](https://botble.com/storage/news/orisa-preset-creative-agency.jpg)

We wrote about [the design decisions inside Orisa](https://botble.com/orisa-creative-agency-theme-what-ai-cannot-design),
down to why the scroll easing is 1.35 and not 1.

### Norio: when the projects should do the talking

Norio is the one I'd hand a design studio. It puts most of its effort into how projects are shown:
five project layouts, from a cinema-style full-screen slider to grids with WebGL hover effects.
Every page has a light and dark scheme, and the heavy motion has off switches in the admin panel,
smooth scrolling, the custom cursor and the mouse-follow effects each separately. It also respects
the visitor's reduced-motion setting automatically.

![The three Norio homepages](https://botble.com/storage/news/norio-three-homepages.jpg)

You can poke at the admin yourself at [norio.botble.com/admin](https://norio.botble.com/admin)
with `admin` / `12345678`. There's a [longer write-up of Norio here](https://botble.com/norio-creative-agency-portfolio-laravel-script-for-studios-and-freelancers).

### Infinia: when your clients are businesses, not art directors

Infinia is for the agency that sells to B2B clients: software, consulting, marketing services.
Five homepages, a calmer corporate look, and blocks built around features, work process, plan
comparisons and statistics rather than showreels.

![Infinia's homepage, a dark business theme with a purple accent](https://botble.com/storage/news/laravel-agency-portfolio-infinia.jpg)

### Zelio and Isak: when the agency is one person

If the "agency" is you, a full agency theme is too much site. Zelio is a portfolio and resume
script with three homepages, aimed at developers and designers. Isak is a single page with eight
animated sections (hero, about, education, work, services, tech stack, testimonials, contact) and
three colour variants, each with a light and dark mode.

![Isak, a single-page personal portfolio with a sidebar photo and a large headline](https://botble.com/storage/news/laravel-agency-portfolio-isak.jpg)

## Where the ready-made route will annoy you

Better you hear it here than after buying:

- **Demo images are not included.** They're licensed for the demo only. You replace them with your
  own work before launch, which you'd do anyway, but plan the time.
- **You start from a layout.** Fifteen homepages is a lot of starting points, but they're still
  starting points. A layout nobody has built yet means a developer, on any route.
- **Big animated themes ask more of your images.** Upload a 6 MB photo and the fanciest theme in
  the world feels slow. Turn on automatic WebP conversion in **Settings → Media** (it's off by
  default), and read [what we learned tuning 112 demo sites](https://botble.com/speeding-up-botble-cms-what-we-learned-tuning-112-demo-sites)
  before you launch.
- **Custom case-study fields need a developer.** If every project needs structured data like budget,
  team size and KPIs, that's a small customisation, not a setting.

## What I'd do

If I were a three-person design studio with no developer, I'd pick between Webflow and a ready-made
script, and the deciding question would be whether I mind paying every year for the hosting and
editor seats to stay comfortable, or would rather pay once and own it.

If I were a Laravel developer building a site for one client with very particular content, I'd use
Statamic and not feel bad about the $349.

If I were a freelancer who just needs a portfolio this weekend, I'd buy Isak or Zelio, spend
Saturday on the photos and Sunday writing the case studies, because the writing is the part that
actually wins work.

And whatever you pick, don't spend a month on the tool. The site that's live with six honest case
studies beats the perfect site that isn't.

## FAQ

### Is Laravel overkill for a portfolio website?

Not if you're using a finished script or CMS on top of it. The visitor never sees Laravel. What you
get is a site you host yourself, that a developer can extend later without a rewrite. If you'd be
writing everything from scratch, then yes, it's a lot of framework for five pages. There's a
broader comparison in [Laravel CMS: the 9 best options in 2026](https://botble.com/laravel-cms-2026).

### Can I run one of these on shared hosting?

Yes. Our themes run on normal cPanel or DirectAdmin hosting with PHP 8.3 or higher and MySQL or
MariaDB. There's a web installer, so no command line, and we install them for free on cPanel and
DirectAdmin hosting if you'd rather not.

### Do I need to know how to code to add projects?

No. Projects, services, team members, blog posts, menus, colours and fonts are all edited in the
admin panel. Code only comes in if you want a layout or a field that doesn't exist yet.

### Can the site be in more than one language?

Yes. Our themes support multiple languages, including right-to-left languages like Arabic, for
both the site and the admin panel.

### What happens when the licence runs out?

Nothing, because there's nothing to run out. You pay once per site, and updates stay free for as
long as the product is maintained. One licence covers one domain, and a subdomain counts as a
separate site.

---

Not sure which theme fits your studio? Open a ticket at
[botble.ticksy.com](https://botble.ticksy.com) and tell us what kind of work you show. We'll point
you at one, or tell you honestly if a different route suits you better.
