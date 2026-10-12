---
title: "A Model Can Write the Page. It Cannot Decide How It Feels"
description: "AI generates layout and copy in seconds. It does not pick 1.35 seconds of scroll easing. Inside Orisa, where the creative work is visible in the constants."
categories:
  - Development
  - Design
tags:
  - orisa
  - creative-agency-theme
  - laravel-cms
  - web-animation
  - gsap
  - botble-cms
image: https://botble.com/storage/news/orisa-creative-agency-theme-what-ai-cannot-design-hero.jpg
status: published
is_featured: false
---

We have written here about pointing AI at Botble and letting it scaffold plugins, follow our
conventions and generate CRUD. That work is real and it keeps getting better. A model will give
you a landing page in one prompt now: hero, three feature cards, testimonials, pricing, footer.
It will be clean, responsive, and it will look exactly like every other page produced that way.

Which is the interesting part. Not that AI builds pages badly. That it builds the same page.

I have been looking at our Orisa theme with that in mind, because it sells to the one audience
that cannot ship the same page as everyone else: creative agencies and people with portfolios.
Their site is the product. And reading its source is a decent way to see where the human work
actually sits, because it is sitting right there in the numbers.

## The argument is in the constants

Here is the scroll setup:

```js
ScrollSmoother.create({ smooth: 1.35, effects: true, smoothTouch: 0, ignoreMobileResize: true })
```

One point three five. Not one, which is the number you reach for. Not two, which is the number
that feels luxurious for a week and then feels slow. Somebody set that value, scrolled the page,
changed it, scrolled again, and stopped at 1.35.

`smoothTouch: 0` in the same line is the other half of the same judgement: smooth scrolling feels
expensive on a trackpad and feels broken on a phone, so on touch it is simply off.

The cursor is the same story. Orisa replaces the pointer with a 14-pixel dot that chases the mouse
with a lerp factor of 0.15, grows to double size near anything marked magnetic, and pulls that
element toward the pointer within a 25-pixel radius:

```js
var p = .15;                      // how far the dot closes the gap each frame
gsap.to(c, { duration: .3, x: (l - a.width/2) / a.width * o, ease: Power2.easeOut });
```

Lower the lerp to 0.05 and the dot lags like it is underwater. Raise it to 0.5 and it snaps,
which kills the whole illusion that it has weight. The 25-pixel magnetic radius has the same
quality: at 10 you cannot feel it, at 60 buttons feel like they are grabbing at you.

None of these are correct answers. There is no dataset where 1.35 wins. They are judgements, made
by looking, and they are the actual product.

## Where a model is genuinely good, and where it stops

To be fair about this, because the hype runs both ways.

Ask a model to build the Orisa cursor and it will write you a working magnetic cursor. The maths
is standard, it is in a thousand tutorials, and a model will produce it faster and with fewer
bugs than most people. It will pick 0.1 for the lerp, because 0.1 is a reasonable round number
that appears in the training data.

What it will not do is open the page, feel that 0.1 is slightly dead, and go to 0.15. Not because
it lacks the concept, but because nothing in the loop is looking. Taste is a feedback process
against a thing you perceive, and the perceiving is the part that is missing.

The same split shows up across the whole theme. Twenty-nine shortcodes, each with multiple layout
styles, all configurable from the admin: `hero-banner`, `projects`, `content-block`,
`image-mosaic`, `video-showreel`, `keyword-ticker`, `our-journey`, `skills-carousel`, and so on.
A model could write any one of those registrations. Deciding that a creative agency site needs a
`keyword-ticker` and an `image-mosaic` rather than two more card grids is the part somebody had to
decide.

## The coming-soon page, which nobody needed to do

My favourite thing in this codebase is a file called `throwable.js`, and it runs on exactly one
template: coming-soon.

It loads the Matter.js physics engine. The words on the page are not text, they are rigid bodies.
They drop in under gravity of 0.8, 80 milliseconds apart, bounce with a restitution of 0.3, pile
up against invisible walls, and you can pick them up and throw them with the mouse:

```js
this.engine.gravity.y = .8;
const a = Matter.Bodies.rectangle(u, M, i.width, i.height,
    { chamfer: { radius: b }, angle: h, isStatic: true, restitution: .3 });
```

There is an option, off by default, that ties gravity to scroll velocity, so the pile lurches when
you scroll.

A coming-soon page is a page that says a site is not ready. The rational amount of effort to spend
on it is close to zero. Somebody spent a physics engine on it, and that decision, more than any
feature list, is what an agency is buying when it buys a theme like this.

It is also well built, which is worth saying because gratuitous and sloppy usually travel
together. The physics runner is gated behind an `IntersectionObserver` and only ticks while the
section is on screen:

```js
this.runnerObserver = new IntersectionObserver(([t]) => {
    this.runner.enabled = t.isIntersecting;
}).observe(this.DOM.element);
```

So the simulation is not burning a core while you read the footer. Someone cared about the effect
and about the battery.

## What you can change without touching code

The practical question, if you are an agency handing a site to a client who will edit it.

From the admin: all colours and typography, three header styles, five footer styles, every
shortcode's content and style variant, logos, breadcrumbs, the preloader. That covers most of what
a client will ever ask for.

From the code only: new shortcode types, blade template changes, animation timing, SCSS overrides.
So the 1.35 is not a settings field. If you want 1.2, you are editing JavaScript.

There are five presets, and this is the part people misread. Creative Agency, Digital Agency,
Marketing Agency, AI and Tech Agency, Personal Creative are not a dropdown. Each is a full demo
import with its own content, options and layouts. You pick one at install. Switching later means
reimporting a database, which is destructive. Decide at the start.

## The honest caveats

**No `prefers-reduced-motion` support.** I checked the theme's own stylesheets and it is not there;
the only matches in the whole theme are inside vendored Bootstrap. If a visitor has asked their
operating system to reduce motion, Orisa does not listen. For a theme whose entire pitch is motion
that is a real gap, and if you are selling to a client with accessibility requirements you should
know before you buy, not after. It is fixable in a child theme, and it should be fixed upstream.

**The motion stack is not free.** GSAP, ScrollTrigger, ScrollSmoother, Splitting.js, Matter.js,
Swiper, Isotope and Magnific Popup all ship. On a mid-range Android, smooth scroll and the custom
cursor are both disabled on purpose, which is the right call, but the bytes still travel.

**There is no drag-and-drop page builder.** Composition is shortcodes placed in the editor. More
predictable than a builder, less immediate.

**And the obvious one:** buying a theme means buying somebody else's taste. The argument of this
post is that those judgements are the valuable part, and the flip side is that they are now yours
whether or not you agree with them. A studio with a strong house style should build, not buy. A
studio that needs a good-looking site next week should buy.

## Where this actually leaves AI

Not nowhere. Point a model at this codebase and it will add your shortcode, wire your contact
form, write your case study copy, fix your layout bug, port a section to a new page. That is most
of the hours in a real project and it is the work we have been automating on purpose.

The 1.35 is not most of the hours. It might be twenty minutes of someone's week. It is also the
reason the site feels like anything at all, and it is the part that is still yours.

Orisa is **$39** right now, which is the intro price, and **30% below that buying direct**, which
is our permanent direct discount rather than a sale. Seven people have bought it in the ten weeks
since launch, which puts it sixth across our whole catalogue in that window and ahead of several
ecommerce products, so the appetite for this is apparently real.

[Orisa on our marketplace →](https://marketplace.botble.com/portfolio/orisa)

The live demo is at [orisa.botble.com](https://orisa.botble.com). Move your mouse around before you
read anything. That is the pitch.

## FAQ

### Can I switch presets after installing?

Not cleanly. Each preset is a full demo import, so switching means reimporting a database and
losing content. Pick at install.

### Does it respect reduced-motion accessibility settings?

No. The theme's own CSS has no `prefers-reduced-motion` rules. Motion plays regardless. Add it in
a child theme if you need it.

### Will the animations slow down phones?

Smooth scroll and the custom cursor are disabled on mobile by design. The JavaScript still
downloads. On desktop the physics runner is gated to on-screen only.

### Can my client edit the site without a developer?

Colours, fonts, header and footer styles, and every shortcode's content and layout variant are
admin-editable. Animation timing and new component types need code.

### Is there a page builder?

No. Composition is through 29 shortcodes placed in the editor, not a drag-and-drop canvas.

### Does it need extra plugins?

No required plugins. Features degrade gracefully when optional ones are absent.

---

Related: [What is Botble CMS](https://botble.com/what-is-botble-cms-the-laravel-cms-thats-built-for-vibe-coding)
if you want the platform underneath, and
[building Claude Code skills for Botble](https://botble.com/how-to-build-claude-code-skills-for-botble-cms-development)
for the half of the work a model genuinely should be doing.
