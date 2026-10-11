---
title: "Marketplace KYC in Laravel: Buyers and Sellers Are Not the Same Job"
slug: laravel-marketplace-kyc-customer-vendor-verification
description: "A passport verifies a buyer. A seller needs a business licence, a VAT certificate and a bank statement. Most KYC plugins build one flow and bolt the other on."
categories:
  - Development
  - Ecommerce
tags:
  - laravel-kyc
  - marketplace-verification
  - vendor-onboarding
  - identity-verification
  - botble-cms
  - laravel
image: https://botble.com/storage/news/laravel-marketplace-kyc-customer-vendor-verification-hero.jpg
status: published
is_featured: false
---

Someone left this on a free KYC plugin on our marketplace, and it is the most useful sentence
anyone has written to us about identity verification:

> the rules should be for the seller, not the customer

They had installed a plugin that asked every buyer for a business licence. On a marketplace, that
is not a configuration mistake. It is the plugin having one flow and two kinds of people.

Four separate people commented on that same plugin asking for things it did not do, and reading all
four together, they were not asking for features. They were asking for the second flow.

## Two problems wearing one name

**Verifying a buyer** answers: is this a real person, and are they who they say they are? One
document. Passport, national ID, driving licence. It happens once, usually at the point where money
or risk appears, and if you ask for more than that you lose the sale.

**Verifying a seller** answers: is this a real business, can it legally trade, and where does its
money go? That is a different pile of paper: business licence, tax certificate, VAT certificate,
articles of incorporation, a bank statement, and usually a director's passport on top. It happens
before they are allowed to list anything, and asking for all of it is normal, because the seller
wants to be paid.

Those two lists barely overlap. The only document on both is a passport, and on the seller side it
means something else entirely: not "this is a real person" but "this is the director of this
company".

So our plugin keeps them as separate sets in code rather than one list with a flag:

```php
public static function customerDocs(): array
{
    return [self::PASSPORT, self::NATIONAL_ID, self::DRIVING_LICENSE];
}

public static function vendorDocs(): array
{
    return [
        self::BUSINESS_LICENSE,
        self::TAX_CERTIFICATE,
        self::UTILITY_BILL,
        self::ARTICLES_OF_INCORPORATION,
        self::BANK_STATEMENT,
        self::VAT_CERTIFICATE,
        self::PASSPORT, // director identity verification
    ];
}
```

Validation reads the set that matches the subject. A buyer cannot submit a VAT certificate into the
identity slot, and a seller is not asked to prove they personally exist before their company can.

## It is not two subjects either. It is five

Once you accept that a buyer and a seller are different subjects, the count does not stop at two.
A Botble install can be a shop, a marketplace, a property portal or a job board, and each of those
has its own idea of who needs checking and when:

| Subject | Who | Blocked from |
|---|---|---|
| Customer | ecommerce buyer | checkout |
| Vendor | marketplace store | listing products |
| Real Estate Agent | property account | publishing listings |
| Employer Company | job-board company | posting jobs |
| Job Seeker | job-board candidate | applying |

Look at the right-hand column, because that is the part people get wrong. Verification is not a
badge. It is a gate, and **the gate is in a different place for every subject.** A buyer is stopped
at checkout; everything before that stays open. A vendor is stopped at listing; they can still sign
in and set up their store. Stopping a vendor at checkout would be meaningless, and stopping a buyer
from browsing would be commercial self-harm.

Each subject is a config entry rather than a class, so a subject whose host plugin is not installed
simply does not appear:

```php
'vendor' => [
    'model'                => \Botble\Marketplace\Models\Store::class,
    'guard'                => 'customer',
    'owner_relation'       => 'store',
    'listing_gate_setting' => 'kyc_vendor_required_for_listing',
    'checkout_gate_setting' => null,
],
```

The `owner_relation` line is there for a reason worth knowing if you ever build this yourself.
Marketplace vendors do not have their own login. They authenticate as a Customer, and the Store is
reached through a relation. So "who is the KYC subject for the logged-in user" is not always "the
logged-in user". For a buyer it is. For a vendor it is one hop away. Getting that wrong gives you a
vendor whose store is verified while the gate reads the Customer, or the reverse.

## The grace period is where this earns its money

Hard-gating verification from day one kills conversion. Nobody uploads a passport to a shop they
have not bought from yet.

So each subject has a grace threshold, and the unit is different per subject, because the thing you
are willing to let someone do unverified is different:

- **Customer**, graced by **amount**. Spend up to a threshold with no documents; the gate appears
  above it. The resolver adds up their actual order total, so "unverified customers can spend up to
  $200" is a real sentence you can configure.
- **Vendor**, graced by **count**. List up to N products, then verify. Enough to get a store set
  up and see whether the platform is worth the paperwork.
- **Employer**, graced by job postings. **Job seeker**, by applications.

```php
public static function used(SubjectDefinition $def, Model $subject): float
{
    return match ($def->graceMode) {
        'amount' => static::resolveAmount($def, $subject),
        default  => static::resolveCount($def, $subject),
    };
}
```

Two modes, one resolver, and the middleware and the helper both read it so the gate and the "you
have X left" message can never disagree.

## The documents are the liability

This is the part that gets skipped, and it is the part that matters, because you are now holding
scans of other people's passports.

**They are not in your public directory.** Files go to the `local` disk under `private/kyc/`, which
is `storage/app/private/kyc/`, never served by the web server. Download goes through a signed URL
with a 15-minute TTL, and file serving is rate-limited to 60 requests a minute.

**The filename is not guessable.** On-disk path is `private/kyc/{subjectHash}/{token}`, where the
token is 32 hex characters from `random_bytes(16)`, so 128 bits. The original filename is kept only
as a display label. If you name uploads after the customer or sequentially, a signed URL leak stops
being one leak.

**You delete them.** Two retention clocks, because rejected and approved are different:

- Rejected: row **and** files gone after 7 days. A rejection is not an audit trail worth keeping
  someone's passport for.
- Approved: files deleted after 7 days, row kept. You keep the proof that you checked, not the
  document you checked.

Accepted types are JPEG, PNG, WebP and PDF, capped at 5 MB, and submissions are rate-limited to 5
a minute. After 3 rejections a subject is locked and an admin has to unlock it by hand, which is
the cheapest brake you can put on someone cycling documents at your reviewer.

Approvals also expire. A nightly command moves a submission to `EXPIRED` once `expires_at` passes,
so "verified in 2024" does not quietly mean "verified" forever.

## Webhooks, because approval usually has to tell something else

Four events (submitted, approved, rejected, expired) POSTed to any endpoint you register, signed:

```php
$headers['X-Kyc-Signature'] = 'sha256=' . hash_hmac('sha256', $payload, $webhook->secret);
```

Verify that signature on your side. An unsigned "this vendor is approved" webhook is an endpoint
that approves vendors for anyone who finds the URL.

## What it does not do

**It does not call an identity-verification API.** No Jumio, Onfido, Sumsub or Persona. A human
reviews submissions in the admin. If you need automated liveness and document forensics, this is
the wrong tool, and I would rather say so here than after you have paid.

**It does not replace car-manager's KYC.** `plugins/car-manager`, which Auxero and Carento are
built on, already ships its own KYC with its own model, its own setting key and its own middleware
alias. Ours detects it at boot and runs in parallel. It will not overwrite that alias or migrate
that data. Two systems coexisting, not one replacing the other. On a car-rental or dealership site
you will probably want the one you already have.

**It is not legal advice and it is not AML compliance.** It collects, stores and reviews documents.
Whether what you collect satisfies your regulator is between you and your regulator.

## Price

$29, or **$20.30 buying direct**, which is our permanent 30% direct discount and not a sale.

[KYC Verification on our marketplace →](https://marketplace.botble.com/portfolio/kyc-verification)

## FAQ

### Can I verify buyers but not sellers, or the other way round?

Yes. Every subject has its own enable setting, and a subject whose plugin is not installed never
appears at all. A plain shop sees the customer subject and nothing else.

### Does it do automatic document checks?

No. Review is manual, in the admin. There is no identity-API integration.

### Where are uploaded documents stored?

On the `local` disk under `storage/app/private/kyc/`, outside the public path, with random 128-bit
filenames. Access is via signed URLs that expire in 15 minutes.

### Do I have to delete documents?

The plugin does it for you: rejected submissions lose the row and the files after 7 days, approved
ones lose the files but keep the row as proof the check happened. Both windows are configurable.

### Will it conflict with the KYC already in car-manager?

No, but it will not merge with it either. It detects car-manager and runs alongside without
touching its settings or data.

### Can I require verification only above a spend threshold?

That is the customer grace mode. Set an amount, and the gate appears at checkout once their order
total passes it.

---

Related: [Laravel SMS gateway and OTP](https://botble.com/laravel-sms-gateway-otp-botble) if you are
verifying phone numbers rather than documents, and
[the best Laravel multivendor marketplace scripts in 2026](https://botble.com/best-laravel-multivendor-marketplace-scripts-2026)
if you are still choosing what to build the marketplace on.
