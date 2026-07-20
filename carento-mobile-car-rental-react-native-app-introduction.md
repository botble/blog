---
title: "Carento Mobile: a React Native Car Rental App for Your Carento or Auxero Site"
description: "Carento Mobile is a React Native (Expo SDK 54) app for car rental and car dealer businesses. 35 screens, a 4-step booking flow, hosted checkout, FCM push, biometric unlock, 4 languages with RTL, and .env white-labeling. Works with Carento or Auxero as the backend."
categories:
  - Announcements
  - Mobile Development
tags:
  - react-native
  - expo
  - mobile-app
  - car-rental
  - car-dealer
  - carento
  - auxero
  - botble
image: https://landing.botble.com/carento-react-native/images/carento-home-light.webp
status: published
is_featured: true
---

![Carento Mobile - Car Rental & Dealer React Native App](https://landing.botble.com/carento-react-native/images/carento-home-light.webp)

# Carento Mobile: a React Native Car Rental App for Your Carento or Auxero Site

I'm Sang from Botble. We just released Carento Mobile on CodeCanyon — a React Native app for car rental and car dealer businesses. It's the mobile client for a site you already run on [Carento](https://codecanyon.net/item/carento-car-dealer-rental-booking-laravel-system/55782539) or [Auxero](https://codecanyon.net/item/auxero-car-dealer-listing-laravel-system/62730624).

Let me start with the part people usually find out too late: **this app is not standalone.** It has no database and no admin panel of its own. It talks to your Laravel backend over the Car Manager REST API. If you don't have Carento or Auxero, buy one of those first — the app is useless without it.

With that out of the way, here's what's actually in it.

**[View on CodeCanyon](https://codecanyon.net/item/carento-mobile-car-rental-dealer-react-native-app/64277973)** · **[Watch the demo video](https://youtu.be/1S6liILg5ls)** · **[Download the Android APK](https://drive.google.com/file/d/1XC5ANX_23LyYE_mh3_WhgFz415RoU9vJ/view)**

## Rent or buy, in one app

Most car rental apps assume every listing is rentable. Carento and Auxero don't work that way — a dealer can list a car for rent or for sale, and the app respects that split.

A rental listing goes into the booking flow. A sale listing skips booking entirely and shows a **Contact Dealer** action that puts the customer in touch with the listing dealer. Same catalog, two different intents, no confusing dead ends where someone tries to "book" a car that's for sale.

## The booking flow

Booking is four steps: **Trip → Driver → Payment → Review**. Trip dates and service add-ons are on step 1; step 3 picks the payment method; step 4 confirms everything before submitting.

Along the way it does the things that actually cause support tickets when they're missing:

- Live price calculation as dates and add-ons change, hitting `POST /calculate-price` on your backend so the number the customer sees is the number your server computed.
- Coupon validation before checkout, not after.
- Security deposit shown separately and deliberately excluded from the total, with a "held at pickup" note — so nobody thinks they're being charged twice.
- Driver's licence approval — you can require an approved licence before a booking goes through, with pending and rejected states surfaced on step 1 instead of failing at submit.
- One-way rental support (toggleable).
- Guest booking (also toggleable) if you don't want to force account creation.

![Booking - trip details](https://landing.botble.com/carento-react-native/images/carento-booking-trip.webp)

### How payment works, honestly

Once the wizard is submitted, the app opens a **hosted WebView checkout** that loads your backend's own checkout page.

I want to be straight about this because it's a real design decision with real trade-offs. The upside is large: every payment gateway you've configured in your Botble admin works in the app on day one — Stripe, PayPal, Razorpay, bank transfer, whatever you've enabled. No gateway SDK to wire into the mobile build, no separate mobile payment config, no divergence between what your website accepts and what your app accepts.

The trade-off is that checkout is a web page inside the app rather than a fully native screen. For a rental booking — where the customer is already entering dates, licence details and card data — this has been a fair trade in practice. But you should know it's how it works before you buy, not after.

Booking creation returns a PENDING booking with no payment URL; the WebView then opens the backend checkout route for it.

## Browsing and search

The catalog side is the part customers spend the most time in, so it got the most screens.

- Home with sliders, featured cars, and browse-by-make and browse-by-type entry points
- Full-text search plus filters: make, type, transmission, fuel, amenities, price range, location
- Car detail with specs, amenities, reviews, similar cars, and a full-screen image gallery
- Dealer directory and dealer detail pages with ratings and fleets
- Favorites, server-backed with an optimistic toggle so the heart responds instantly
- In-app blog with posts, categories and search

![Search and filters](https://landing.botble.com/carento-react-native/images/carento-search.webp)

![Car detail](https://landing.botble.com/carento-react-native/images/carento-car-detail.webp)

![Dealers](https://landing.botble.com/carento-react-native/images/carento-dealers.webp)

One honest caveat: the Car Manager API doesn't currently expose a public dealer-list endpoint, so in this version the **Dealers tab derives its list client-side from the cars feed**. It works, and it's what shipped in 1.0.0. A dedicated endpoint is the cleaner fix and it's on our list.

## Accounts, notifications and after-booking

- Social login with Google, Apple and Facebook. Apple auto-hides on Android. Each provider only appears if you've configured its keys, and if you configure none, the whole social block disappears cleanly instead of leaving a stray divider.
- Biometric unlock via Face ID or fingerprint after the first sign-in.
- Notification inbox with unread badges, pull-to-refresh, infinite scroll, mark-all-read, and an app icon badge.
- Push notifications through **Firebase Cloud Messaging**, registered per device after sign-in and unregistered on sign-out. Tapping a notification deep-links to the right screen, including from a cold start.
- Booking history and detail, with trip status updates.
- 1–5 star reviews with comments, writable from a booking detail once the booking is processing or completed.
- Profile management with avatar upload.

![Sign in](https://landing.botble.com/carento-react-native/images/carento-login.webp)

On push: version 1.1.0 fixed a genuine bug here. 1.0.0 registered an Expo push token, but the Botble backend sends via FCM HTTP v1, which only accepts FCM registration tokens — so push could never actually be delivered. The app now mints a real FCM token and re-registers it on rotation. Sign-out also failed to unregister the device, meaning a signed-out phone kept receiving the previous account's notifications. Both are fixed. If you're evaluating the app, you're getting the fixed version.

![Bookings](https://landing.botble.com/carento-react-native/images/carento-bookings.webp)

## Languages, currency and dark mode

Four languages ship with the app: **English, Vietnamese, Arabic and French** — 561 translation keys each, kept in sync by a CI check that fails the build if the locales drift apart.

Arabic runs in full **RTL**. That's not a token gesture: layout direction is applied through `I18nManager`, and the RTL language list already covers Arabic, Hebrew, Persian and Urdu, so adding one of those is a locale file away.

Currency is handled server-side. The app sends `X-CURRENCY` on every request and your backend returns converted prices, so there's no second source of truth for money.

Dark mode follows the system preference or can be set manually.

![Dark mode](https://landing.botble.com/carento-react-native/images/carento-home-dark.webp)

## Rebranding without touching code

This is the part I'd push hardest on if you're an agency shipping this for a client.

The app name, brand colors, fonts, API endpoint, contact details, social links, splash color and several feature toggles all come from `.env`:

```bash
APP_NAME=Carento
API_BASE_URL=https://your-site.com
PRIMARY_COLOR=84cc16
PRIMARY_DARK_COLOR=4d7c0f
ON_PRIMARY_COLOR=FFFFFF
APP_FONT_HEADING=InstrumentSans
APP_FONT_BODY=Inter
ENABLE_GUEST_BOOKING=true
ENABLE_ONE_WAY_RENTAL=true
```

The app name is never hardcoded anywhere. Every string that mentions it uses i18next interpolation, so setting `APP_NAME` rebrands the app across all screens in all four languages at once. There's a script (`npm run i18n:check`) that fails CI if the brand literal ever sneaks into a locale file.

You can also tune the home screen composition (`HOME_FEATURED_COUNT`, `HOME_DEALERS_COUNT`, `HOME_BLOG_COUNT`), image thumbnail size, and help/support URLs the same way. Icons and splash art are drop-in file replacements in `assets/`.

Colors, fonts, name, endpoint — no code changes. Anything that affects the native layer needs `npx expo prebuild` and a rebuild, which is normal for Expo.

## Technical specifications

### Stack

| | |
|---|---|
| Framework | React Native 0.81.5, React 19, Expo SDK 54 |
| Language | TypeScript, strict mode |
| Architecture | New Architecture enabled |
| Routing | expo-router v6 (file-based) |
| Server state | TanStack Query v5 |
| App state | React Context |
| Styling | NativeWind v4 + Tailwind, with a design-token theme file |
| i18n | react-i18next + expo-localization |
| Storage | expo-secure-store (tokens), AsyncStorage (preferences) |
| Push | @react-native-firebase/messaging |
| Icons | lucide-react-native |
| Testing | jest-expo + @testing-library/react-native |

### By the numbers

- **35 screens** across 5 tabs, auth, account, settings, car, booking, dealer, brand, blog and review stacks
- **75 components**, 72 of them organised into 12 feature groups
- **19 service modules** and 11 custom hooks
- **561 translation keys** × 4 languages
- Current version: **1.1.0**

### API integration

The app talks to `{API_BASE_URL}/api/v1`, mostly under `/car-manager`. Auth is **Laravel Sanctum** personal access tokens on the customer guard, stored in secure storage. Every response uses the `{ error, data, message }` envelope, with Laravel pagination metadata on lists.

Every request carries `X-LANGUAGE` and `X-CURRENCY`, plus `X-API-KEY` if you've set one. A 401 with a token clears the session, a 503 flips the app into a maintenance state, and other 5xx errors show a server-error screen rather than a blank page. Requests time out at 30 seconds.

### Requirements

- A **Carento** or **Auxero** installation with the Car Manager plugin active and the API enabled at Admin → Settings → API
- Node.js 20+ and npm (install with `npm install --legacy-peer-deps`)
- Xcode for iOS, Android Studio for Android
- An Expo account for EAS builds
- Apple Developer ($99/yr) and Google Play ($25 one-time) accounts to publish

Expo Go won't run it — there are native modules, so you need a development build.

## Getting it running

```bash
cp .env.example .env
# set API_BASE_URL to your Carento or Auxero site
npm install --legacy-peer-deps
npm start
```

Builds and store submissions go through EAS, with development, preview and production profiles already configured:

```bash
eas build --platform all --profile production
eas submit --platform all --profile production
```

The docs include full store checklists for both Apple and Google — icon sizes, screenshot dimensions, privacy policy URL, the demo account reviewers will ask for, IARC rating, data safety form. Review typically takes 1–3 days, longer for a brand new Google Play account.

## What's included

- Full React Native (Expo) source code, TypeScript strict
- iOS and Android project configuration
- 35 screens, 75 components
- 4 language files with RTL support
- EAS build and submit configuration
- Complete documentation with setup, API, FCM, social login and publishing guides
- 6 months support
- Free updates forever

## Links

- **[Buy on CodeCanyon](https://codecanyon.net/item/carento-mobile-car-rental-dealer-react-native-app/64277973)**
- **[Documentation](https://docs.botble.com/carento-react-native)**
- **[Demo video](https://youtu.be/1S6liILg5ls)**
- **[Android demo APK](https://drive.google.com/file/d/1XC5ANX_23LyYE_mh3_WhgFz415RoU9vJ/view)**
- **[Backend demo](https://carento.botble.com)**
- **[Support](https://botble.ticksy.com)**

---

*Carento Mobile requires [Carento](https://codecanyon.net/item/carento-car-dealer-rental-booking-laravel-system/55782539) or [Auxero](https://codecanyon.net/item/auxero-car-dealer-listing-laravel-system/62730624) as its backend. Both ship the Car Manager plugin and expose the same REST API, so the app works against either one.*
