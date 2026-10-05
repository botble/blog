---
title: "Botble License Manager: A Self-Hosted License Server for Laravel Products"
slug: botble-license-manager-license-server
description: "Botble License Manager is a self-hosted license server built on Laravel 13 that issues licences, verifies activations, blocks domain abuse and delivers signed updates. $39 introductory, $59 regular, one-time. Here is what it does, how a licence check works, and what it does not do."
categories:
  - Development
  - Buyer Guides
tags:
  - botble-license-manager
  - license-server
  - license-and-updates
  - software-licensing
  - laravel
  - licensebox-alternative
  - self-hosted
image: https://botble.com/storage/news/botble-license-manager-license-server-hero.jpg
status: published
is_featured: false
---

# Botble License Manager: A Self-Hosted License Server for Laravel Products

![A self-hosted license server: issue, verify, sign, deliver](https://botble.com/storage/news/botble-license-manager-license-server-hero.jpg)

**Botble License Manager is a self-hosted license server and update manager for software vendors.** You run it on your own domain. It issues licence keys, verifies activations from your customers' installations, limits how many domains a licence may run on, detects and blocks abuse, hosts your release files, and delivers updates signed with an Ed25519 key so a tampered archive cannot pass as yours. It is built on **Laravel 13** and **PHP 8.3+**, it is a one-time purchase at **$39 introductory, $59 regular**, and it ships with ready integrations for **Envato, Gumroad and Lemon Squeezy** so purchases on those platforms issue licences automatically.

That is the short version. The rest of this page is the detail, including what it does not do.

## The category emptied out, and most people have not noticed

If you sell a PHP or Laravel script and you want licensing, you used to buy **LicenseBox** on CodeCanyon. It went years without meaningful maintenance, and the author has since removed it from Envato. **It is gone.** Searching CodeCanyon for "licensebox" today returns zero results.

What replaced it on CodeCanyon is nothing. I searched the Envato API on 5 October 2026:

| Search on CodeCanyon | What comes back |
|---|---|
| `licensebox` | **0 results** |
| `license manager` in PHP Scripts | POS systems, HR software, inventory tools. No licensing product in the top results |
| `license server` | Email platforms, affiliate scripts, media apps |
| `envato license verification` | 3 results: two WordPress plugins last updated in **2020**, with 37 and 3 sales, and one with 4 sales |

So the marketplace that used to serve this need no longer serves it. If you sell software and you need licensing today, you have three options: build it, rent a SaaS, or buy a self-hosted system direct from a vendor. We build and sell the third one, which I will be upfront about, and I will also be upfront about when building it yourself is the better call.

## What a license server actually has to do

Before any product comparison, this is the list. If you are evaluating anything, including ours, check it against this.

1. **Issue licences**, in bulk and on demand, tied to a product and a customer.
2. **Verify an activation** from a customer's installation, over an API, and answer definitively: valid, invalid, expired, or over its domain limit.
3. **Bind to domains**, with a limit, and handle the awkward cases: staging sites, a customer moving domains, a developer activating on localhost.
4. **Detect abuse** without punishing honest customers. One licence appearing on forty domains is piracy. The same licence moving from `old.com` to `new.com` is a customer who relaunched their site.
5. **Expire and renew**, if you sell support windows or subscriptions.
6. **Host and deliver updates**, so the customer's admin panel can say "version 2.3 is available" and fetch it, with the download authorised against the licence.
7. **Prove the download is yours.** If someone replaces the archive on your server, every customer installs their code and not yours. This is the step almost everybody skips.
8. **Survive being public.** It is an internet-facing API that gates revenue, so it needs rate limiting, logging and blacklists.

Points 4 and 7 are where cheap implementations fall over, and point 7 is the one that turns a licensing problem into a security incident.

## What Botble License Manager does

All of the following is from the source, not the sales page.

### Licences and activations

Products, product versions, licences, and activation records are separate entities, so one licence can carry several activations and you can see each one. Licences have domain limits, expiry dates, and a verification type: **Envato, direct, or non-Envato**, which matters if you sell the same product on a marketplace and from your own site.

Licence keys are **encrypted at rest**. There is a bulk `GenerateLicensesCommand` for issuing keys in batches, and a `SendLicenseDetailsCommand` for mailing them out.

### Abuse handling, which is the interesting part

This is where the design shows. Four scheduled commands do the work:

- **`ScanDomainViolationsCommand`** looks for licences being used on domains they are not entitled to.
- **`NotifyDomainSwitchCommand`** notices when a licence changes domain and notifies, rather than silently killing the installation. A customer who relaunched their site is not a pirate, and treating them like one costs you a refund and a one-star review.
- **`ProcessAutoBlacklistCommand`** blacklists domains and IPs after a configurable number of failed attempts, with separate thresholds for each.
- **`ProcessLicenseExpirationsCommand`** handles the expiry lifecycle.

The settings behind these will look familiar if you came from LicenseBox: log failed activation attempts, log failed update downloads, deactivate old activations when a new one arrives, add the first activation's domain as a licensed domain automatically, blacklist a domain after N failures, blacklist an IP after N failures, and rate-limit the API by IP or by key. That vocabulary is deliberate.

### Updates, and signing them properly

Versions carry **checksums** and **Ed25519 signatures**. Downloads are authorised through **single-use download tokens** rather than a guessable URL.

The part worth dwelling on is the keypair generator's own documentation, quoted verbatim:

> The threat this whole feature addresses is an attacker who can alter the update archive. If the signing private key lives on the same machine as the archives, that attacker can simply re-sign whatever they substituted, and the signature proves nothing while appearing to prove everything. That is worse than shipping no signatures at all, because operators stop looking.
>
> For the same reason there is deliberately no "generate a keypair" button in the admin panel. A UI affordance that writes a private key onto the license server would be used by people who did not read the warning, and we would then own the false assurance.

So the command tells you, in capitals, to run it on the release box and not on the license server, and the convenient button that would undermine the whole feature was left out on purpose. A signing scheme that is honest about its own threat model, and that refuses to make the wrong thing easy, is rarer than it should be.

### API and integrations

38 API routes, with API keys that have **separate internal and external scopes** — the external surface is deliberately narrow, because that is the one facing the internet. Every API call is logged. There are 51 admin routes and a customer-facing portal with login, password reset, profile and language.

Three marketplace plugins ship with it: **Envato, Gumroad and Lemon Squeezy**. A purchase on any of them can issue a licence automatically, which is the difference between licensing being a system and licensing being a job somebody does by hand every morning.

## How a licence check works, concretely

1. Your product is installed on `customer-site.com` and the admin enters a licence key.
2. Your product calls your license server's verify endpoint with the key and the domain.
3. The server checks the key, the product, the expiry, and how many domains are already activated.
4. It answers valid or not, and records an activation.
5. Later, your product asks the server whether a newer version exists. If one does, the server issues a single-use download token.
6. Your product downloads the archive and checks the signature against your public key before unpacking it.

Step 6 is what separates an update system from a remote code execution vulnerability you built yourself.

## Build it yourself instead?

Sometimes you should, and here is the honest test.

**Build it if** licensing is one endpoint for one product, you have no update delivery, you do not sell through a marketplace, and you will never need a blacklist. That is genuinely a weekend of work and a vendor product is overkill.

**Buy it if** the list above looks like your situation: several products, several versions, update delivery, marketplace purchases, and abuse you have already seen. Items 4, 7 and 8 in that list — abuse detection, signed releases, and surviving a public API — are each larger than they look, and all three are the kind of thing you get wrong quietly.

The honest arithmetic: at $39 one-time, the question is not whether it is worth the money. It is whether you want to own and maintain this particular piece of infrastructure for the next five years. If licensing is not your product, probably not.

## Where it will annoy you

- **There is no automatic importer from LicenseBox.** The settings vocabulary matches, so the concepts transfer, but your existing licence data does not move itself. You re-issue through the bulk generate command or push your records in through the API. Plan for it.
- **The customer portal is deliberately small.** Login, dashboard, password, avatar, language. It is somewhere for your customers to see their licences, not a full account area with billing and invoices.
- **You are running an internet-facing service that gates your revenue.** If it goes down, your customers cannot activate. That is the real cost of self-hosting here, and no feature list changes it.
- **Signing requires `sodium`** and a release process disciplined enough to keep the private key off the license server. The tooling pushes you the right way, but it cannot make you do it.

## Pricing

**$39 introductory, $59 regular. One-time, not a subscription.** Six months of support and lifetime updates. Buy it at [marketplace.botble.com/license-manager](https://marketplace.botble.com/license-manager).

Compare that to a licensing SaaS billed monthly, forever, on a server you do not control, holding the keys to your own product's activation.

## Questions people ask

**What is Botble License Manager?**
A self-hosted license server and update manager, built on Laravel 13 and PHP 8.3+. You install it on your own domain and it issues licences, verifies activations, blocks abuse and delivers signed updates for the software you sell.

**Is it a replacement for LicenseBox?**
It is built to cover the same ground, with the same settings vocabulary, and it is current rather than abandoned. LicenseBox was removed from CodeCanyon, so there is no like-for-like upgrade path and no automatic data import. You migrate by re-issuing licences or pushing them through the API.

**Does it work with Envato purchase codes?**
Yes. There is an Envato integration plugin, and licences carry a verification type of Envato, direct or non-Envato, so you can sell the same product on a marketplace and from your own site without two systems.

**Can it handle Gumroad or Lemon Squeezy sales?**
Yes, both ship as integration plugins.

**Does it deliver updates, or only check licences?**
Both. It stores product versions with checksums and Ed25519 signatures, and serves them through single-use download tokens authorised against the licence.

**Can one licence be used on several domains?**
Yes, within a limit you set per licence, with activations recorded individually and a domain-switch notification when a customer moves.

**Is it a subscription?**
No. $39 introductory, $59 regular, paid once.

**What does it need to run?**
PHP 8.3 or 8.4, Laravel 13, a database, and cron for the scheduled commands that handle expiry, blacklisting and violation scans. The `sodium` extension is needed for release signing.

**Is there a demo?**
Yes, linked from the product page.

## Links

- [Botble License Manager on the marketplace](https://marketplace.botble.com/license-manager) — $39 introductory
- [The original introduction post](https://botble.com/license-manager-self-hosted-license-update-manager-for-your-products), with integration examples
- [Laravel CMS: the 9 best options in 2026](https://botble.com/laravel-cms-2026)

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "SoftwareApplication",
  "name": "Botble License Manager",
  "applicationCategory": "DeveloperApplication",
  "applicationSubCategory": "License server and update manager",
  "operatingSystem": "Linux, self-hosted",
  "description": "Self-hosted license server and update manager for software vendors. Issues licence keys, verifies activations, enforces domain limits, detects abuse, and delivers product updates signed with Ed25519. Built on Laravel 13 and PHP 8.3+.",
  "softwareVersion": "Laravel 13",
  "url": "https://marketplace.botble.com/license-manager",
  "offers": {
    "@type": "Offer",
    "price": "39.00",
    "priceCurrency": "USD",
    "url": "https://marketplace.botble.com/license-manager",
    "availability": "https://schema.org/InStock",
    "description": "One-time purchase, introductory price. Regular price $59. Includes six months of support and lifetime updates."
  },
  "featureList": [
    "Licence key issuing, in bulk or on demand",
    "Activation verification over API",
    "Per-licence domain limits and activation records",
    "Domain violation scanning and domain-switch notifications",
    "Automatic domain and IP blacklisting after failed attempts",
    "Licence expiry and renewal processing",
    "Product version hosting with checksums",
    "Ed25519-signed release archives",
    "Single-use download tokens",
    "API keys with separate internal and external scopes, with request logging",
    "Envato, Gumroad and Lemon Squeezy purchase integrations",
    "Customer portal"
  ],
  "publisher": {
    "@type": "Organization",
    "name": "Botble Technologies",
    "url": "https://botble.com"
  }
}
</script>

<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "FAQPage",
  "mainEntity": [
    {
      "@type": "Question",
      "name": "What is Botble License Manager?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Botble License Manager is a self-hosted license server and update manager built on Laravel 13 and PHP 8.3+. Software vendors install it on their own domain to issue licence keys, verify activations from customer installations, enforce domain limits, detect and block abuse, and deliver product updates signed with an Ed25519 key. It costs $39 introductory, $59 regular, as a one-time purchase."
      }
    },
    {
      "@type": "Question",
      "name": "Is Botble License Manager a replacement for LicenseBox?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "It covers the same ground and uses the same settings vocabulary, and unlike LicenseBox it is actively maintained on Laravel 13. LicenseBox has been removed from CodeCanyon by its author, so there is no like-for-like upgrade path and no automatic data importer. Migration means re-issuing licences through the bulk generate command or pushing existing records in through the API."
      }
    },
    {
      "@type": "Question",
      "name": "Does Botble License Manager work with Envato purchase codes?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. An Envato integration plugin ships with it, and every licence carries a verification type of Envato, direct or non-Envato, so the same product can be sold on a marketplace and from your own website through one system. Gumroad and Lemon Squeezy integrations also ship with it."
      }
    },
    {
      "@type": "Question",
      "name": "Does it deliver software updates as well as verifying licences?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "Yes. It stores product versions with checksums and Ed25519 signatures and serves them through single-use download tokens authorised against the licence, so a customer's installation can check for a newer version, download it, and verify the signature before unpacking."
      }
    },
    {
      "@type": "Question",
      "name": "How much does Botble License Manager cost?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "$39 as an introductory price and $59 regular, paid once rather than as a subscription. It includes six months of support and lifetime updates, and is sold at marketplace.botble.com/license-manager."
      }
    },
    {
      "@type": "Question",
      "name": "What does Botble License Manager need to run?",
      "acceptedAnswer": {
        "@type": "Answer",
        "text": "PHP 8.3 or 8.4, Laravel 13, a database, and cron for the scheduled commands that process licence expiry, blacklisting and domain violation scans. The sodium PHP extension is required for signing release archives."
      }
    }
  ]
}
</script>
