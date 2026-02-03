# Generate Pitch Deck: Black And Yellow

Use the `pitch-deck-generator` skill to create a hackathon pitch deck for Black And Yellow.

## Project Info

**App Name:** Black And Yellow
**Tagline:** CEX speed, DEX trust, privacy by default.
**Hackathon:** HackMoney 2026
**Accent Color:** #FFD700 (Golden Yellow)

## Problem Narrative

"You want to trade perpetuals. On a CEX, you get fast execution but your orders are visible to the operator - front-running, market manipulation, privacy breaches. On a DEX, you get trustlessness but slow execution and visible orderbook. The operator sees everything. Other traders see everything. Your trading strategy is exposed. And if you want privacy? There's nothing. Until now."

**Problem Bullets:**
- CEX operators can see your orders - front-running, manipulation, data selling
- DEX orderbooks are fully visible - everyone knows your strategy
- No existing perps solution offers true order privacy
- Trust the operator OR trust the chain - never both

**Problem GIF:** Search for "watching you" or "privacy concerned" or "someone looking over shoulder"

## Solution

**Transformation:** "From exposed trading to private execution"

**Solution Bullets:**
- Orders encrypted to a TEE - operator can't see them
- Hidden orderbook shows depth, not prices - strategies stay private
- Both parties sign matches - no manipulation possible
- Yellow Network for instant settlement, ZK proofs for verification

**Solution GIF:** Search for "locked safe" or "encrypted" or "private"

## How It Works

Flow steps:
1. Create & Encrypt Order
2. TEE Matches Privately
3. Both Sign Match
4. Instant Settlement

## Sponsors & Ecosystem Fit

**Sponsors:** Yellow Network, LI.FI, Uniswap

**Why This Matters:**
- Yellow Network state channels enable instant, trust-minimized settlement
- LI.FI powers cross-chain deposits - trade with assets from any chain
- TEE + ZK = cryptographic privacy guarantees

## Team

- Gabriel (Full Stack + Crypto)

## Links

- github.com/gabrielantonyxaviour/black-and-yellow
- @gabrielaxy
- gabrielaxy.eth

## Tech Stack

Yellow Network / Nitrolite, AWS Nitro Enclaves (TEE), ZK Batch Proofs, LI.FI Composer, Base Mainnet, React + TypeScript, Pyth/Chainlink

---

## Instructions

1. Use `gifgrep` to find appropriate problem/solution GIFs (privacy themed, security themed)
2. Use `/gemini-image` to generate a logo: "Minimalist app logo: abstract hexagon shape with honeycomb pattern, black (#0a0a0a) background with golden yellow (#FFD700) geometric lines forming a shield or lock pattern. Sleek, crypto-native design. No text. Square format."
3. Generate the pitch deck following the 8-slide structure
4. Save to: `/Users/gabrielantonyxaviour/Documents/workspaces/ethglobal/ideas/decks/black_and_yellow_pitch.pptx`
