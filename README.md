# BLINK — Native App (iOS / Android)

> Catch creatures. Earn crypto. No wallets required in-app.

## What This Is

The native App Store / Google Play version of [blinkworld.xyz](https://blinkworld.xyz).

- Players explore the real world via GPS and catch BLINK creatures
- Each catch generates a **redemption code**
- Users bring codes to blinkworld.xyz to claim BLINK tokens, NFTs, or ETH
- Zero crypto in the app — 100% Apple-compliant

## Tech Stack

- **Expo** (React Native) — built and iterated via [Rork](https://rork.com)
- **Backend:** blinkworld.xyz API (same Supabase DB as web)
- **Auth:** Device UUID / Supabase anon auth (no wallet needed)

## Design

Dark glass UI — neon green (#00FF88) on deep black (#0a0a0f).  
Mirrors the web app experience but native-optimized.

## Dev Setup

```bash
npm install
npx expo start
```

## Connect to Rork

See `RORK_PROMPT.md` for the full AI generation prompt.

In Rork: **Integrations → Connect with GitHub → squalayz/blink-app**
