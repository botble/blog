---
title: "Laravel SMS Gateway and OTP: What Breaks, and How We Built Ours"
slug: laravel-sms-gateway-otp-botble
description: "Sending SMS from a Laravel store looks like a two-hour job until you hit retries, consent, per-country routing and double-sends. Here is what our SMS Gateways plugin does about it. $20.30 direct."
categories:
  - Development
  - Ecommerce
tags:
  - laravel-sms-gateway
  - sms-otp
  - otp-login
  - msg91
  - twilio
  - botble-cms
  - laravel
image: https://botble.com/storage/news/laravel-sms-gateway-otp-botble-hero.jpg
status: published
is_featured: false
---

Sending an SMS from Laravel is a two-hour job. You sign up with Twilio, drop their SDK in, write a
job, and the first message lands on your phone. Done.

Then you ship it, and the two-hour job starts eating weeks.

I know this because the free SMS plugins on our marketplace are some of the most-downloaded things
we host, and the review threads under them are all the same shape. Someone installs one, it works,
and then they come back asking for a provider it doesn't have. Msg91. Fast2SMS. SSL Wireless.
SMSC. Beem Africa. They're not asking for features. They're asking for *their* provider, because
the one in the plugin doesn't sell to their country or doesn't accept their business.

That's the first thing nobody plans for.

## The provider problem is a geography problem

Twilio is the default because it's the one everyone has heard of. It's also expensive in a lot of
places, and in some countries it either can't deliver to local networks or needs sender ID
registration you can't get as a foreign entity.

So the real list of providers you need isn't "Twilio". It's whichever one works where your
customers are. Ours ships eight built in:

Twilio, Vonage, AWS SNS, Plivo, Msg91, Fast2SMS, BulkSMSBD and eSMS.vn.

Four of those are there because of exactly one thing: India (Msg91, Fast2SMS), Bangladesh
(BulkSMSBD) and Vietnam (eSMS.vn) are where the requests came from.

That list will still be wrong for somebody. It was wrong for the person who asked for SSL Wireless
and the one who asked for SMSC. So there's a ninth driver that is just a form:

```
endpoint_url      http_method (GET / POST_FORM / POST_JSON)
headers_template  body_template
auth_type         auth_header_name / api_key / username / password
success_http_min  success_http_max
success_json_path success_json_value
provider_message_id_path
phone_format      region_hint
status_poll_url   status_success_path
```

You fill that in from your provider's API docs and you have a driver. No code, no deploy. If your
provider speaks HTTP and returns something you can point a JSON path at, it works.

I'd rather give you that than keep adding drivers forever.

## Where it will annoy you

**It doesn't do WhatsApp.** People ask, because they have a Twilio account with WhatsApp enabled
and assume it carries over. It doesn't. This is an SMS plugin. If WhatsApp is the channel you
actually need, this isn't the thing to buy, and I'd rather you heard that here than after.

**You still need a provider account, and you still pay them.** Nothing here makes SMS free. It
makes it survivable.

**Sender ID rules are yours to deal with.** India needs DLT registration. Several countries need a
pre-approved sender ID. The plugin sends what you tell it to; it can't get you registered.

## OTP is not "send a random number"

This is where the two-hour estimate really falls apart.

An OTP flow that survives contact with real users needs: a code that expires, a limit on how many
you'll send to one number, a limit on how many wrong guesses you'll accept, invalidation of old
codes when a new one is issued, and a way to tell "wrong code" apart from "expired code" in the UI
without telling an attacker which it was.

We have `smsg_otps` as its own table, with request, verify and invalidate-all as the three
operations, keyed on guard plus phone plus purpose. The purpose matters: a login code and a
phone-verification code are not interchangeable, and if you key only on the phone number they
become so.

It works across four account types, because Botble sites aren't all shops: ecommerce customers,
CMS members, real estate accounts and job board accounts each got a `phone_verified_at` column.

Two bugs from the last few releases, since they're the kind you only find in production. In 1.0.34
OTP codes were expiring the instant they were issued on MariaDB — a datetime comparison that
behaved differently than on MySQL. In 1.0.32 a wrong code on the login form rendered the page
without the field state it needed. Neither showed up in testing. Both showed up within a day of
someone using it properly.

## The parts you only think of after launch

**Delivery isn't a boolean.** A provider accepting your API call is not the message arriving. There's
a `smsg_delivery_logs` table, webhook handling for providers that report status back, and a status
poll URL on the custom driver for providers that make you ask.

**Retries need to be deliberate.** A failed send that retries immediately, forever, is how you turn
a provider outage into a bill. There's a retry scheduler and a rate limiter, and they are separate
things on purpose.

**Consent is a table, not a checkbox.** `smsg_consents` exists because "did this person agree to
receive marketing SMS" is a question you will eventually have to answer with a timestamp, not a
shrug.

**Per-country routing.** Once you have more than one provider, you want the Indian numbers going to
Msg91 and everything else to Twilio. That's a resolver, not an if-statement in a job.

**Inbound replies.** People reply STOP. Somebody has to process that.

**And the one I didn't see coming:** some Botble installs already send SMS. The FriendsOfBotble SMS
plugin, the car rental plugin's own notifications. Install ours on top and the customer gets two
messages for one order. So there's a guard that detects those at boot and defers the conflicting
listeners instead of double-sending. It re-reads the setting at event time rather than caching it,
because on a multi-tenant install one worker serves many stores and anything read at boot is frozen
for all of them.

That last paragraph is the honest shape of this kind of plugin. The interesting work isn't sending
the message.

## What it hooks into

On a Botble ecommerce store, these fire SMS without you writing anything: order placed, order
confirmed, payment processed, shipping status changed, order cancelled. Templates live in
`smsg_templates` so the wording is editable without a deploy.

## Price

$29 on CodeCanyon. **$20.30 buying direct**, which is the permanent 30% direct discount, not a sale.

[SMS Gateways on our marketplace →](https://marketplace.botble.com/portfolio/sms-gateways)

If you're weighing up whether to build this yourself: the sending part is genuinely a two-hour job.
Budget for the other three weeks.

## FAQ

### Does it support WhatsApp?

No. It's SMS only. A Twilio account with WhatsApp enabled doesn't change that.

### My provider isn't in the list. Am I stuck?

No, that's what the configurable HTTP driver is for. You set the endpoint, method, headers, body
template and how to read success out of the response, all from the admin. If your provider has a
normal HTTP API, it works. Send us their docs if you want a second opinion before you buy.

### Can I use more than one provider at once?

Yes, with per-country routing, so numbers in one country go to the provider that's cheapest or most
reliable there.

### Does it work outside ecommerce?

Yes. OTP works for CMS members, real estate accounts and job board accounts as well as ecommerce
customers. The ecommerce event hooks are the part that's shop-specific.

### What if I already have an SMS plugin installed?

It detects the common ones and defers to them rather than sending twice. You'll want to move the
settings across and disable the old one, but the overlap period won't spam your customers.

---

Related: [Botble License Manager](https://botble.com/botble-license-manager-license-server) if
you're selling software rather than buying it, and
[the best Laravel ecommerce scripts in 2026](https://botble.com/best-laravel-ecommerce-scripts-2026)
if you're still choosing a base to build on.
