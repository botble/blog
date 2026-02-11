---
title: "License Manager - Self-Hosted License & Update Manager for Your Products"
description: "Introducing License Manager, a self-hosted Laravel-based solution for managing software licenses, activations, and updates. No third-party dependencies."
categories:
  - Announcements
tags:
  - license
  - license-manager
  - codecanyon
  - laravel
  - self-hosted
image: https://botble.com/storage/envato/license-manager/license-manager-hero-section.jpg
status: published
is_featured: true
---

# License Manager - Self-Hosted License & Update Manager for Your Products

If you're vibe coding and building products to sell on CodeCanyon, SaaS, or your own scripts, you'll probably find this useful.

Our team has just released **License Manager**. It's a Laravel-based solution for managing licenses, activating/deactivating licenses, and checking updates for your products. The core is still built on Botble CMS, just like our other products.

Self-hosted, lightweight, and no third-party dependency.

## Why License Manager?

If you've ever sold a script or plugin, you know the pain: tracking who bought what, handling activations across multiple domains, dealing with piracy, and delivering updates. Most developers end up duct-taping together a mix of spreadsheets, manual emails, and hope.

License Manager gives you a single, clean dashboard to handle all of that — on your own server, under your control.

## Key Features

### License Management

Create and manage licenses with full flexibility:

- **Multiple license types** — perpetual, subscription, trial, or lifetime
- **Parallel activation limits** — control how many domains can use the same license at once
- **Domain & IP whitelisting** — restrict licenses to specific domains or IPs, with wildcard support (e.g. `*.example.com`)
- **Automatic expiration handling** — set expiry dates for licenses, update support, and support coverage independently

### Activation Tracking

Every activation is logged with full context — domain, IP, timestamp, and user agent. You always know exactly where your licenses are being used.

When the activation limit is reached, the system can either reject the new activation or automatically deactivate the oldest one — your choice.

### Product & Version Management

Define your products, manage versions, and deliver updates through the built-in update system. Each version can include:

- Version number and release date
- Summary and HTML changelog
- Downloadable files (main package + optional SQL migrations)

Your customers check for updates via the API, and the system handles the rest.

### REST API

A complete REST API powers everything. Two API types are available:

- **External API** — for your client applications to activate, verify, deactivate licenses, and check for updates
- **Internal API** — for admin/backend operations like creating products, licenses, and managing activations

Example endpoints:

```
POST /api/external/license/activate
POST /api/external/license/verify
POST /api/external/license/deactivate
POST /api/external/update/check
```

Ready-to-use client examples are available for **PHP**, **Laravel**, and **WordPress** on [GitHub](https://github.com/botble/license-manager-examples).

### Customer Portal

Your customers get a self-service portal where they can:

- View their licenses and expiration dates
- See active installations
- Deactivate installations they no longer need
- Update their profile and password

The portal supports social login via **Envato**, **Google**, **Facebook**, and **GitHub**.

### Webhook Notifications

Get notified in real time when important events happen:

- `license.expiring` — a license is about to expire
- `license.expired` — a license has expired
- `update.expired` — update support has ended

All webhook payloads are signed with HMAC-SHA256 for security. You can use these to trigger your own automations — send reminder emails, update your CRM, or sync with other systems.

### Envato Integration

If you sell on CodeCanyon/Envato, you can verify Envato purchase codes directly. Customers can log in with their Envato account and the system auto-imports their licenses.

### Security

- **AES-128/256 encryption** for all license data
- **Auto-blacklisting** — automatically block domains or IPs after too many failed activation attempts
- **Rate limiting** — configurable per minute, by IP, API key, or both
- **Failed attempt logging** — track and analyze suspicious activity

## How It Works

The workflow is straightforward:

1. **You create a product** and generate licenses in the admin panel
2. **Your customer enters their license code** in your app
3. **Your app calls the API** to activate the license
4. **On each request**, your app verifies the license is still valid
5. **When updates are available**, your app checks the API and downloads them

Here's what activation looks like in PHP:

```php
$client = new LicenseClient($apiKey, $apiUrl, $productId);

// Activate
$result = $client->activate($licenseCode);
if ($result['is_active']) {
    file_put_contents('license.dat', $result['lic_response']);
}

// Verify on each request
$result = $client->verify(file_get_contents('license.dat'));
if (!$result['is_active']) {
    // License invalid — redirect to activation page
}
```

That's it. Five minutes to integrate.

## Migrating from LicenseBox?

If you're currently using the original LicenseBox, License Manager includes a built-in migration tool. It imports your products, licenses, activations, and versions automatically — and the legacy API endpoints keep working so your existing customers don't need to change anything.

## Technical Requirements

| Requirement | Version |
|---|---|
| PHP | 8.2+ |
| MySQL | 5.7+ or MariaDB 10.2+ |
| Laravel | 12 |
| Composer | 2.x |

## Links

- **Purchase**: [License Manager on CodeCanyon](https://codecanyon.net/item/license-manager-laravel-php-licenser-and-updates-manager/61852432)
- **Documentation**: [docs.botble.com/license-manager](https://docs.botble.com/license-manager/)
- **Live Demo**: [license-manager.botble.com](https://license-manager.botble.com/admin) (admin / 12345678)
- **API Playground**: [license-app-demo.botble.com](https://license-app-demo.botble.com)
- **Integration Examples**: [github.com/botble/license-manager-examples](https://github.com/botble/license-manager-examples)
- **Support**: [botble.ticksy.com](https://botble.ticksy.com)

If you're tired of managing licenses manually, feel free to [check it out](https://codecanyon.net/item/license-manager-laravel-php-licenser-and-updates-manager/61852432).
