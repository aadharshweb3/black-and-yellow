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

