# BLINK — Rork App Prompt

Paste this entire prompt into Rork to generate the BLINK native app.

---

## App Overview

Build a React Native (Expo) app called **BLINK** — a GPS-based creature catching game inspired by Pokémon Go. Players walk around in the real world, find creatures spawned at GPS coordinates, and catch them. No crypto, no wallets, no financial mechanics inside the app — it's a pure game. Caught creatures generate a **redemption code** the user can take to our website (blinkworld.xyz) to claim rewards.

---

## Design System — LOCKED, DO NOT DEVIATE

**Colors:**
- Background: `#0a0a0f` (near-black)
- Primary accent: `#00FF88` (neon green)
- Secondary accent: `#88FF00` (lime green)
- Text primary: `#FFFFFF`
- Text secondary: `rgba(255,255,255,0.6)`
- Card background: `rgba(255,255,255,0.05)` with `backdropFilter: blur(24px)`
- Border: `rgba(255,255,255,0.08)`
- Danger/rare: `#ff8ae0` (pink/mythic)
- Legendary: `#ffd166`

**Typography:**
- Font: System default (SF Pro on iOS, Roboto on Android)
- Headlines: Bold, white, letter-spacing -0.5
- Body: Regular, `rgba(255,255,255,0.8)`

**Glass card style (use everywhere):**
```
backgroundColor: 'rgba(255,255,255,0.05)',
borderWidth: 1,
borderColor: 'rgba(255,255,255,0.08)',
borderRadius: 20,
overflow: 'hidden',
// iOS shadow
shadowColor: '#00FF88',
shadowOpacity: 0.08,
shadowRadius: 20,
```

**Buttons:**
- Primary: `backgroundColor: '#00FF88'`, `borderRadius: 50`, text black bold
- Secondary: glass card style with green border

**Rarity colors:**
- Common: `#9aa3b2`
- Uncommon: `#00FF88`
- Rare: `#88FF00`
- Legendary: `#ffd166`
- Mythic: `#ff8ae0`

---

## Screens

### 1. Splash / Loading
- Full black screen
- Animated BLINK eye logo (the eye with lightning bolt) pulses in neon green
- "BLINK" text fades in below
- Brief (1.5s) then auto-navigates to Map

### 2. Map Screen (Main Screen)
This is the core screen — modeled after Pokémon Go / Apple Maps style.

**Layout:** Full-screen map (MapView from `react-native-maps`) fills 100% of the screen. Everything else floats as absolute layers on top.

**Top bar (floating, frosted glass pill row):**
- Left: BLINK eye logo (24px green)
- Center: "SENSE" pill — glows green with pulsing dot when creatures are nearby, grey when nothing around
- Right: Creature count badge + profile avatar circle

**Map:**
- Dark map style (use `mapStyle` with dark/night mode JSON)
- User location shown as a glowing green dot with pulsing ring
- Creature markers on map — each shows the creature's image in a circular frame with a glow ring colored by rarity
- 50m catch radius shown as a subtle green circle around the player

**Creature markers:**
- Show creature image (from `image_url`)
- Rarity-colored glow ring
- Tap opens creature catch sheet

**Bottom sheet (floating, glass style):**
- Shows list of nearby creatures
- Each row: creature image + name + rarity badge + distance
- Swipe up to expand full list
- Swipe down to collapse

**Tool rail (right side, vertical):**
- 🎯 Filter button (glass pill)
- 📡 Radar button (opens radar overlay)
- 🔔 Notifications button

### 3. Catch Screen / CinematicCatch
Full-screen overlay when player taps a creature within range.

**3 phases:**

**Phase 1 — Aim:**
- Creature floats in center with breathing animation
- Rarity-colored aura pulses behind it
- Expanding radar rings animate outward
- Large "CATCH" button at bottom (neon green, pill shape)
- Creature name + rarity badge shown above

**Phase 2 — Throw (1.8 seconds):**
- BLINK eye icon flies from bottom to creature in an arc
- Screen shakes 3 times
- White flash on impact
- Green edge flash
- Creature wobbles and shrinks

**Phase 3 — Caught:**
- Confetti burst (40 particles, green/white/rarity color)
- Creature card flips in with spring physics (rotateY)
- Shine sweep effect across card
- "CAUGHT!" headline
- BLINK reward count animates up
- Shows: `X free catches remaining today`
- Shows: **redemption code** in a copyable box (e.g. `BLINK-7F3K-9XM2`)
- "Copy Code" button
- "Go to blinkworld.xyz to claim your reward" text
- Share button + Done button

**Error state:**
- If catch fails (out of range, already caught)
- Shows error message with dismiss

### 4. Radar Screen
Overlay (modal) showing directional radar.

- Dark circular radar with rotating green sweep line
- 3 distance rings
- Spring-animated arrow pointing toward nearest creature
- Distance labels: Far / Nearby / Close / Very Close / In Reach (no exact GPS)
- Creature name + rarity shown in center when in range
- Tap anywhere to dismiss

### 5. My Creatures Screen
- Grid of all creatures the player has caught
- Each card: creature image, name, rarity color border, catch date
- Tap creature: full detail modal
  - Image (large)
  - Name, rarity, description
  - Redemption code (copyable)
  - "Claim at blinkworld.xyz" button (opens Safari)
  - Already claimed? Shows claimed badge

### 6. Profile Screen
- Avatar (letter-based, green circle)
- Username
- Stats: Total caught / Rarest catch / Catch streak
- Creatures by rarity breakdown (badges)
- Settings section: notifications toggle, location accuracy toggle, about/legal

### 7. Onboarding (first launch only)
- 3-slide walkthrough:
  1. "Explore your world" — map with creatures
  2. "Catch them all" — catch animation preview
  3. "Claim rewards" — website mockup + code concept
- "Let's Go" button leads to permissions flow
- Request: Location (Always / While Using) + Notifications

---

## Data / API

**Base URL:** `https://blinkworld.xyz/api`

**Endpoints used:**
- `GET /creatures/nearby?lat={lat}&lng={lng}&radius=500` — returns array of `{ id, name, tier, image_url, lat, lng, distance_m }`
- `POST /catch` — body: `{ spawnId, userId }` — returns `{ success, redemptionCode, blinkReward, freeCatchesRemaining, creatureName, tier }`
- `GET /profile/:userId` — returns `{ username, totalCaught, rarest, catchStreak }`
- `GET /my-catches/:userId` — returns array of caught creatures with redemption codes

**Auth:**
- Use Supabase anonymous auth or simple UUID device ID stored in AsyncStorage
- No wallet connection, no MetaMask, no crypto in the app

**Rate limiting:**
- 3 free catches per day per user
- API returns 429 when limit hit → show "Come back tomorrow or visit blinkworld.xyz for more"

---

## Creature Data (Rarity Tiers)
- **Common** — grey glow — frequent spawns, small BLINK reward
- **Uncommon** — green glow — moderate spawns
- **Rare** — lime glow — rare spawns, push notification when nearby
- **Legendary** — gold glow — very rare
- **Mythic** — pink/purple glow — extremely rare, screen goes dark on encounter

---

## Key UX Rules
1. **No crypto anywhere in the app** — "BLINK Reward" is just a number shown after catching. The code is how they redeem it.
2. **Location is never shown precisely** — radar uses vague distance labels only
3. **Haptics on everything** — catch, tap, swipe
4. **Sounds** — subtle whoosh on catch throw, success chime on caught, ambient hum when rare nearby
5. **Dark everywhere** — never use white backgrounds
6. **Speed** — map should feel instant; cache creature positions for 30s

---

## Tech Stack
- Expo SDK 52+ with Expo Router
- `react-native-maps` for the map
- `expo-location` for GPS
- `expo-notifications` for push notifications
- `expo-haptics` for haptic feedback
- `expo-clipboard` for copy-to-clipboard
- `@react-native-async-storage/async-storage` for local data
- `react-native-reanimated` for animations
- `react-native-gesture-handler` for swipe/drag
- No Redux — use React Context + hooks

---

## File Structure
```
app/
  _layout.tsx         # Root layout, dark background
  index.tsx           # Splash screen
  (tabs)/
    map.tsx           # Main map screen
    creatures.tsx     # My creatures grid
    profile.tsx       # Profile screen
  catch/
    [id].tsx          # CinematicCatch screen
  radar.tsx           # Radar overlay

components/
  MapScreen.tsx
  CinematicCatch.tsx
  CreatureRadar.tsx
  CreatureMarker.tsx
  BottomSheet.tsx
  GlassCard.tsx
  RarityBadge.tsx
  RedemptionCode.tsx
  CreatureCard.tsx

lib/
  api.ts              # API calls to blinkworld.xyz
  storage.ts          # AsyncStorage helpers
  haptics.ts          # Haptic patterns
  sounds.ts           # Sound effects
  theme.ts            # Colors, typography constants
  types.ts            # TypeScript interfaces

constants/
  colors.ts
  rarity.ts
```

---

## Brand Assets
- Eye/lightning logo: use a simple SVG of an eye with a lightning bolt inside, in neon green (#00FF88) on black
- "BLINK" wordmark: white, bold, wide letter spacing
- No MishMesh branding anywhere — app is 100% BLINK

---

Build this app. Start with the Map screen and CinematicCatch — those are the core loops. Make it feel like a premium, polished iOS app with dark glass UI throughout.
