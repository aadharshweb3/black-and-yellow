# BLACK AND YELLOW - UI/UX DESIGN SYSTEM

> Private Perpetuals Exchange - "CEX speed, DEX trust, privacy by default."

---

## TABLE OF CONTENTS

1. [Design Philosophy](#design-philosophy)
2. [Color System](#color-system)
3. [Typography](#typography)
4. [Component Library](#component-library)
5. [Page Specifications](#page-specifications)
6. [Animations & Interactions](#animations--interactions)

---

## DESIGN PHILOSOPHY

### CORE PRINCIPLES

- **STEALTH AESTHETIC** - Dark, mysterious, secure
- **YELLOW ACCENTS** - Electric highlights on black canvas
- **PRIVACY-FIRST UX** - Visual cues showing what's hidden
- **TRADING FOCUSED** - Clean, information-dense layouts
- **INSTANT FEEDBACK** - Real-time state channel updates

### VISUAL LANGUAGE

```
THEME: Black dominates, yellow highlights
FEEL: Underground exchange, exclusive access
MOOD: Secure, fast, professional
```

---

## COLOR SYSTEM

### CSS VARIABLES (globals.css)

```css
@layer base {
  :root {
    /* BLACK AND YELLOW THEME */
    --background: 0 0% 4%;              /* Near black #0a0a0a */
    --foreground: 45 10% 95%;           /* Off-white with warmth */

    --card: 0 0% 7%;                    /* Slightly elevated #121212 */
    --card-foreground: 45 10% 95%;

    --popover: 0 0% 6%;
    --popover-foreground: 45 10% 95%;

    --primary: 45 100% 50%;             /* ELECTRIC YELLOW #FFD700 */
    --primary-foreground: 0 0% 4%;

    --secondary: 45 80% 15%;            /* Muted gold surface */
    --secondary-foreground: 45 100% 80%;

    --muted: 0 0% 12%;
    --muted-foreground: 0 0% 50%;

    --accent: 45 100% 60%;              /* BRIGHT GOLD #FFDF33 */
    --accent-foreground: 0 0% 4%;

    --destructive: 0 84% 50%;           /* Red for liquidations */
    --destructive-foreground: 0 0% 100%;

    --success: 142 76% 45%;             /* Green for profits */
    --success-foreground: 0 0% 100%;

    --border: 0 0% 15%;
    --input: 0 0% 15%;
    --ring: 45 100% 50%;

    /* CUSTOM BRAND COLORS */
    --yellow-glow: 45 100% 50%;         /* Neon yellow for glows */
    --yellow-dim: 45 80% 30%;           /* Dimmed yellow */
    --profit-green: 142 76% 45%;        /* PnL positive */
    --loss-red: 0 84% 50%;              /* PnL negative */
    --hidden-purple: 270 60% 50%;       /* Privacy indicator */

    --glass-bg: 0 0% 7% / 0.8;
    --glass-border: 45 100% 50% / 0.2;

    --radius: 0.5rem;
  }
}
```

### COLOR USAGE GUIDELINES

| Element | Color | Hex |
|---------|-------|-----|
| Background | Near Black | `#0a0a0a` |
| Cards/Surfaces | Dark Gray | `#121212` |
| Primary Text | Off White | `#F5F5F0` |
| Secondary Text | Gray | `#808080` |
| Yellow Accent | Electric Yellow | `#FFD700` |
| Yellow Glow | Bright Gold | `#FFDF33` |
| Profit | Green | `#22C55E` |
| Loss | Red | `#EF4444` |
| Hidden/Private | Purple | `#8B5CF6` |

---

## TYPOGRAPHY

### FONT STACK

#### PRIMARY: INTER (UI & BODY)
- **WHY**: Clean, readable, professional trading aesthetic
- **USE**: All body text, labels, data
- **WEIGHT**: 400, 500, 600, 700

```tsx
import { Inter } from 'next/font/google'

const inter = Inter({
  subsets: ['latin'],
  variable: '--font-inter',
})
```

#### MONO: JETBRAINS MONO (NUMBERS & DATA)
- **WHY**: Perfect for prices, amounts, addresses
- **USE**: All numerical data, commitments, addresses
- **WEIGHT**: 400, 500, 700

```tsx
import { JetBrains_Mono } from 'next/font/google'

const jetbrainsMono = JetBrains_Mono({
  subsets: ['latin'],
  variable: '--font-mono',
})
```

### TYPOGRAPHY SCALE

```css
.text-display {
  @apply font-inter text-4xl font-bold tracking-tight;
}

.text-h1 {
  @apply font-inter text-2xl font-bold tracking-tight;
}

.text-h2 {
  @apply font-inter text-xl font-semibold;
}

.text-h3 {
  @apply font-inter text-lg font-semibold;
}

.text-body {
  @apply font-inter text-base;
}

.text-small {
  @apply font-inter text-sm text-muted-foreground;
}

.text-price {
  @apply font-mono text-lg font-bold tabular-nums;
}

.text-amount {
  @apply font-mono text-base tabular-nums;
}

.text-commitment {
  @apply font-mono text-xs text-muted-foreground;
}
```

---

## COMPONENT LIBRARY

### SHADCN/UI COMPONENTS TO INSTALL

```bash
# Core UI
npx shadcn@latest add button
npx shadcn@latest add card
npx shadcn@latest add badge
npx shadcn@latest add separator

# Forms
npx shadcn@latest add input
npx shadcn@latest add select
npx shadcn@latest add slider
npx shadcn@latest add tabs

# Overlays
npx shadcn@latest add dialog
npx shadcn@latest add sheet
npx shadcn@latest add tooltip
npx shadcn@latest add popover

# Data Display
npx shadcn@latest add table
npx shadcn@latest add skeleton
npx shadcn@latest add progress

# Feedback
npx shadcn@latest add toast
npx shadcn@latest add alert
```

---

### CUSTOM COMPONENTS

#### 1. HIDDEN ORDERBOOK

```tsx
// components/hidden-orderbook.tsx
"use client"

import { cn } from "@/lib/utils"

interface OrderbookLevel {
  depth: number // 0-100 percentage
  count: number // number of hidden orders
}

interface HiddenOrderbookProps {
  asks: OrderbookLevel[]
  bids: OrderbookLevel[]
  markPrice: number
  spread: number
}

export function HiddenOrderbook({ asks, bids, markPrice, spread }: HiddenOrderbookProps) {
  return (
    <div className="bg-card rounded-lg p-4 border border-border">
      <div className="flex items-center justify-between mb-4">
        <h3 className="text-h3">HIDDEN ORDERBOOK</h3>
        <div className="flex items-center gap-2">
          <div className="w-2 h-2 rounded-full bg-hidden-purple animate-pulse" />
          <span className="text-small">PRIVACY ENABLED</span>
        </div>
      </div>

      {/* ASKS (SELLS) */}
      <div className="space-y-1 mb-2">
        {asks.map((level, i) => (
          <div key={`ask-${i}`} className="relative h-6 flex items-center">
            <div
              className="absolute right-0 h-full bg-loss-red/20"
              style={{ width: `${level.depth}%` }}
            />
            <span className="relative z-10 text-small text-muted-foreground ml-auto">
              ???
            </span>
          </div>
        ))}
      </div>

      {/* MARK PRICE */}
      <div className="py-3 border-y border-border flex items-center justify-between">
        <span className="text-price text-primary">${markPrice.toLocaleString()}</span>
        <span className="text-small">SPREAD: {spread}%</span>
      </div>

      {/* BIDS (BUYS) */}
      <div className="space-y-1 mt-2">
        {bids.map((level, i) => (
          <div key={`bid-${i}`} className="relative h-6 flex items-center">
            <div
              className="absolute left-0 h-full bg-profit-green/20"
              style={{ width: `${level.depth}%` }}
            />
            <span className="relative z-10 text-small text-muted-foreground">
