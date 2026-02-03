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
              ???
            </span>
          </div>
        ))}
      </div>

      {/* LEGEND */}
      <div className="mt-4 pt-4 border-t border-border">
        <p className="text-small text-center">
          Depth shown • Prices hidden • 🔒 Orders encrypted
        </p>
      </div>
    </div>
  )
}
```

#### 2. MARKET CARD

```tsx
// components/market-card.tsx
"use client"

import { Card } from "@/components/ui/card"
import { Badge } from "@/components/ui/badge"
import { Button } from "@/components/ui/button"
import { TrendingUp, TrendingDown } from "lucide-react"
import { cn } from "@/lib/utils"

interface MarketCardProps {
  pair: string
  price: number
  change24h: number
  volume24h: string
  openInterest: string
  onTrade: () => void
}

export function MarketCard({
  pair,
  price,
  change24h,
  volume24h,
  openInterest,
  onTrade
}: MarketCardProps) {
  const isPositive = change24h >= 0

  return (
    <Card className="p-4 hover:border-primary/50 transition-colors cursor-pointer group">
      <div className="flex items-center justify-between mb-4">
        <div className="flex items-center gap-3">
          <div className="w-10 h-10 rounded-full bg-primary/20 flex items-center justify-center">
            <span className="text-primary font-bold text-sm">
              {pair.split('-')[0]}
            </span>
          </div>
          <div>
            <h3 className="font-semibold">{pair} PERP</h3>
            <Badge variant="outline" className="text-xs">PERPETUAL</Badge>
          </div>
        </div>

        <Button
          onClick={onTrade}
          className="bg-primary text-primary-foreground hover:bg-primary/90"
        >
          TRADE
        </Button>
      </div>

      <div className="flex items-center justify-between">
        <div>
          <p className="text-price">${price.toLocaleString()}</p>
          <div className={cn(
            "flex items-center gap-1 text-sm",
            isPositive ? "text-profit-green" : "text-loss-red"
          )}>
            {isPositive ? <TrendingUp className="w-4 h-4" /> : <TrendingDown className="w-4 h-4" />}
            <span>{isPositive ? "+" : ""}{change24h.toFixed(2)}%</span>
          </div>
        </div>

        <div className="text-right text-small">
          <p>Vol: {volume24h}</p>
          <p>OI: {openInterest}</p>
        </div>
      </div>
    </Card>
  )
}
```

#### 3. LEVERAGE SELECTOR

```tsx
// components/leverage-selector.tsx
"use client"

import { Button } from "@/components/ui/button"
import { cn } from "@/lib/utils"

interface LeverageSelectorProps {
  value: number
  onChange: (leverage: number) => void
}

const LEVERAGE_OPTIONS = [1, 2, 5, 10]

export function LeverageSelector({ value, onChange }: LeverageSelectorProps) {
  return (
    <div className="space-y-2">
      <label className="text-small">LEVERAGE</label>
      <div className="flex gap-2">
        {LEVERAGE_OPTIONS.map((lev) => (
          <Button
            key={lev}
            variant={value === lev ? "default" : "outline"}
            size="sm"
            onClick={() => onChange(lev)}
            className={cn(
              "flex-1 font-mono",
              value === lev && "bg-primary text-primary-foreground"
            )}
          >
            {lev}x
          </Button>
        ))}
      </div>
    </div>
  )
}
```

#### 4. ORDER FORM

```tsx
// components/order-form.tsx
"use client"

import { useState } from "react"
import { Card } from "@/components/ui/card"
import { Button } from "@/components/ui/button"
import { Input } from "@/components/ui/input"
import { Tabs, TabsList, TabsTrigger } from "@/components/ui/tabs"
import { LeverageSelector } from "./leverage-selector"
import { Lock } from "lucide-react"
import { cn } from "@/lib/utils"

interface OrderFormProps {
  pair: string
  markPrice: number
  onSubmit: (order: {
    side: "LONG" | "SHORT"
    type: "LIMIT" | "MARKET"
    price: number
    size: number
    leverage: number
  }) => void
}

export function OrderForm({ pair, markPrice, onSubmit }: OrderFormProps) {
  const [side, setSide] = useState<"LONG" | "SHORT">("LONG")
  const [orderType, setOrderType] = useState<"LIMIT" | "MARKET">("LIMIT")
  const [price, setPrice] = useState(markPrice.toString())
  const [size, setSize] = useState("")
  const [leverage, setLeverage] = useState(1)

  const margin = (parseFloat(size) || 0) * (parseFloat(price) || 0) / leverage

  return (
    <Card className="p-4">
      <h3 className="text-h3 mb-4">PLACE ORDER</h3>

      {/* SIDE SELECTOR */}
      <div className="grid grid-cols-2 gap-2 mb-4">
        <Button
          variant={side === "LONG" ? "default" : "outline"}
          onClick={() => setSide("LONG")}
          className={cn(
            side === "LONG" && "bg-profit-green hover:bg-profit-green/90"
          )}
        >
          LONG
        </Button>
        <Button
          variant={side === "SHORT" ? "default" : "outline"}
          onClick={() => setSide("SHORT")}
          className={cn(
            side === "SHORT" && "bg-loss-red hover:bg-loss-red/90"
          )}
        >
          SHORT
        </Button>
      </div>

      {/* LEVERAGE */}
      <div className="mb-4">
        <LeverageSelector value={leverage} onChange={setLeverage} />
      </div>

      {/* ORDER TYPE */}
      <Tabs value={orderType} onValueChange={(v) => setOrderType(v as "LIMIT" | "MARKET")} className="mb-4">
        <TabsList className="w-full">
          <TabsTrigger value="LIMIT" className="flex-1">LIMIT</TabsTrigger>
          <TabsTrigger value="MARKET" className="flex-1">MARKET</TabsTrigger>
        </TabsList>
      </Tabs>

      {/* PRICE INPUT */}
      {orderType === "LIMIT" && (
        <div className="mb-4">
          <label className="text-small">PRICE</label>
          <Input
            type="number"
            value={price}
            onChange={(e) => setPrice(e.target.value)}
            className="font-mono"
            placeholder="0.00"
          />
        </div>
      )}

      {/* SIZE INPUT */}
      <div className="mb-4">
        <label className="text-small">SIZE ({pair.split('-')[0]})</label>
        <Input
          type="number"
          value={size}
          onChange={(e) => setSize(e.target.value)}
          className="font-mono"
          placeholder="0.00"
        />
        <div className="flex gap-2 mt-2">
          {[25, 50, 75, 100].map((pct) => (
            <Button
              key={pct}
              variant="outline"
              size="sm"
              className="flex-1 text-xs"
            >
              {pct}%
            </Button>
          ))}
        </div>
      </div>

      {/* ORDER SUMMARY */}
      <div className="bg-muted rounded-lg p-3 mb-4 space-y-2">
        <div className="flex justify-between text-small">
          <span>Margin Required</span>
          <span className="font-mono">${margin.toFixed(2)}</span>
        </div>
        <div className="flex justify-between text-small">
          <span>Est. Fee</span>
          <span className="font-mono">~${(margin * 0.0005).toFixed(2)}</span>
        </div>
      </div>

      {/* SUBMIT BUTTON */}
      <Button
        className={cn(
          "w-full",
          side === "LONG"
            ? "bg-profit-green hover:bg-profit-green/90"
            : "bg-loss-red hover:bg-loss-red/90"
        )}
        onClick={() => onSubmit({
          side,
          type: orderType,
          price: parseFloat(price),
          size: parseFloat(size),
          leverage
        })}
      >
        <Lock className="w-4 h-4 mr-2" />
        PLACE {side}
        <span className="ml-2 text-xs opacity-70">ENCRYPTED</span>
      </Button>
    </Card>
  )
}
```

#### 5. POSITION CARD

```tsx
// components/position-card.tsx
"use client"

import { Card } from "@/components/ui/card"
import { Button } from "@/components/ui/button"
import { Badge } from "@/components/ui/badge"
import { cn } from "@/lib/utils"

interface PositionCardProps {
  pair: string
  side: "LONG" | "SHORT"
  leverage: number
  size: number
  entryPrice: number
  markPrice: number
  liquidationPrice: number
  pnl: number
  pnlPercent: number
}

export function PositionCard({
  pair,
  side,
  leverage,
  size,
  entryPrice,
  markPrice,
  liquidationPrice,
  pnl,
  pnlPercent
}: PositionCardProps) {
  const isProfit = pnl >= 0

  return (
    <Card className="p-4 border-l-4 border-l-primary">
      <div className="flex items-center justify-between mb-3">
        <div className="flex items-center gap-2">
          <span className="font-semibold">{pair}</span>
          <Badge
            variant="outline"
            className={cn(
              side === "LONG" ? "border-profit-green text-profit-green" : "border-loss-red text-loss-red"
            )}
          >
            {side} {leverage}x
          </Badge>
        </div>
        <div className={cn(
          "text-right",
          isProfit ? "text-profit-green" : "text-loss-red"
        )}>
          <p className="font-mono font-bold">
            {isProfit ? "+" : ""}{pnl.toFixed(2)} USDC
          </p>
          <p className="text-sm">
            {isProfit ? "+" : ""}{pnlPercent.toFixed(2)}%
          </p>
        </div>
      </div>

      <div className="grid grid-cols-3 gap-4 text-small mb-4">
        <div>
          <p className="text-muted-foreground">Size</p>
          <p className="font-mono">{size}</p>
        </div>
        <div>
          <p className="text-muted-foreground">Entry</p>
          <p className="font-mono">${entryPrice.toLocaleString()}</p>
        </div>
        <div>
          <p className="text-muted-foreground">Liq. Price</p>
          <p className="font-mono text-loss-red">${liquidationPrice.toLocaleString()}</p>
        </div>
      </div>

      <div className="flex gap-2">
        <Button variant="outline" size="sm" className="flex-1">
          ADD MARGIN
        </Button>
        <Button variant="destructive" size="sm" className="flex-1">
          CLOSE
        </Button>
      </div>
    </Card>
  )
}
```

#### 6. PRIVACY STATUS INDICATOR

```tsx
// components/privacy-status.tsx
"use client"

import { cn } from "@/lib/utils"
import { Shield, Lock, Eye, EyeOff } from "lucide-react"

interface PrivacyStatusProps {
  steps: {
    label: string
    status: "pending" | "complete" | "active"
  }[]
}

export function PrivacyStatus({ steps }: PrivacyStatusProps) {
  return (
    <div className="bg-card border border-border rounded-lg p-4">
      <div className="flex items-center gap-2 mb-4">
        <Shield className="w-5 h-5 text-primary" />
        <h3 className="font-semibold">PRIVACY STATUS</h3>
      </div>

      <div className="space-y-3">
        {steps.map((step, i) => (
          <div key={i} className="flex items-center gap-3">
            <div className={cn(
              "w-6 h-6 rounded-full flex items-center justify-center text-xs",
              step.status === "complete" && "bg-profit-green text-white",
              step.status === "active" && "bg-primary text-primary-foreground animate-pulse",
              step.status === "pending" && "bg-muted text-muted-foreground"
            )}>
              {step.status === "complete" ? "✓" : step.status === "active" ? "○" : "○"}
            </div>
            <span className={cn(
              "text-sm",
              step.status === "pending" && "text-muted-foreground"
            )}>
              {step.label}
            </span>
          </div>
        ))}
      </div>

      <div className="mt-4 pt-4 border-t border-border flex items-center gap-2 text-small text-muted-foreground">
        <EyeOff className="w-4 h-4" />
        <span>Your order details are encrypted</span>
      </div>
    </div>
  )
}
```

---

## PAGE SPECIFICATIONS

### PAGE 1: MARKETS DASHBOARD

**Route:** `/` or `/markets`

```
┌─────────────────────────────────────────────────────────────────────────┐
│  ◈ BLACK AND YELLOW              [MARKETS] [POSITIONS] [HISTORY]       │
│                                                    🔍   🌙   [0x1a2b]  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  PERPETUAL MARKETS                                     24H VOL: $2.4M   │
│  ══════════════════                                                     │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │  [BTC LOGO]  BTC-USDC PERP    $97,234.50   +2.34%   [TRADE →]  │   │
│  │              Vol: $1.2M | OI: $4.5M                             │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │  [ETH LOGO]  ETH-USDC PERP    $3,456.78    +1.87%   [TRADE →]  │   │
│  │              Vol: $890K | OI: $2.1M                             │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │  [SOL LOGO]  SOL-USDC PERP    $178.92      -0.45%   [TRADE →]  │   │
│  │              Vol: $340K | OI: $980K                             │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                                                         │
│  ═══════════════════════════════════════════════════════════════════   │
│                                                                         │
│  YOUR POSITIONS                                           Total: $124   │
│  ──────────────                                                         │
│                                                                         │
│  ┌─────────────────────────────────────────────────────────────────┐   │
│  │  ETH-USDC  LONG 2x  │  Entry: $3,420  │  PnL: +$124 (+3.6%)    │   │
│  └─────────────────────────────────────────────────────────────────┘   │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### PAGE 2: TRADING INTERFACE

**Route:** `/trade/[pair]` (e.g., `/trade/btc-usdc`)

```
┌─────────────────────────────────────────────────────────────────────────┐
│  ◈ BLACK AND YELLOW      BTC-USDC PERP           [MARKETS]    [0x1a2b] │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌───────────────────────────┐  ┌─────────────────────────────────────┐│
│  │    HIDDEN ORDERBOOK       │  │         PRICE CHART                 ││
│  │                           │  │                                     ││
│  │  ASKS                     │  │  $97,500 ┤                          ││
│  │  ████████████████  ???    │  │          │    ╱╲                    ││
│  │  ██████████████    ???    │  │  $97,250 ┤   ╱  ╲   ╱╲             ││
│  │  ████████████      ???    │  │          │  ╱    ╲_╱  ╲            ││
│  │  ██████████        ???    │  │  $97,000 ┤_╱           ╲___        ││
│  │  ████████          ???    │  │          └──────────────────       ││
│  │  ─────────────────────    │  │           1H   4H   1D   1W        ││
│  │  $97,234.50  MARK PRICE   │  │                                     ││
│  │  ─────────────────────    │  └─────────────────────────────────────┘│
│  │  ████████          ???    │                                         │
│  │  ██████████        ???    │  ┌─────────────────────────────────────┐│
│  │  ████████████      ???    │  │         PLACE ORDER                 ││
│  │  ██████████████    ???    │  │                                     ││
│  │  ████████████████  ???    │  │  [    LONG    ] [    SHORT    ]     ││
│  │  BIDS                     │  │                                     ││
│  │                           │  │  LEVERAGE                           ││
│  │  🔒 PRIVACY ENABLED       │  │  [1x] [2x] [5x] [10x]              ││
│  │                           │  │                                     ││
│  └───────────────────────────┘  │  ORDER TYPE                         ││
│                                 │  [LIMIT]      [MARKET]              ││
│                                 │                                     ││
│                                 │  PRICE     [__________]             ││
│                                 │  SIZE      [__________]             ││
│                                 │  [25%][50%][75%][MAX]               ││
│                                 │                                     ││
│                                 │  Margin: $4,860  |  Fee: ~$2.43     ││
│                                 │                                     ││
│                                 │  ┌─────────────────────────────┐    ││
│                                 │  │   🔒 PLACE LONG (ENCRYPTED) │    ││
