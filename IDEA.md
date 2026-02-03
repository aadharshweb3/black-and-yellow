# BLACK AND YELLOW - Private Perpetuals Exchange

> "CEX speed, DEX trust, privacy by default."

---

## Overview

Black And Yellow is a **private perpetuals exchange** where your orders are hidden from everyone — including the operator. Orders are encrypted to a TEE (Trusted Execution Environment) that runs verified matching code. When orders match, both parties sign, balances update instantly via Yellow Network, and batched ZK proofs make everything verifiable on-chain.

---

## Markets

| Pair | Type | Chain |
|------|------|-------|
| BTC-USDC | Perpetual | Base |
| ETH-USDC | Perpetual | Base |
| SOL-USDC | Perpetual | Base |

**Leverage Options:** 1x, 2x, 5x, 10x (conservative)

---

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                 │
│                         USER DEVICE                             │
│   ┌─────────────────────────────────────────────────────────┐   │
│   │                                                         │   │
│   │   1. order = {side, token, amount, price, leverage}    │   │
│   │   2. secret = random()                                  │   │
│   │   3. commitment = hash(order || secret)                 │   │
│   │   4. encrypted_payload = encrypt(order || secret, TEE)  │   │
│   │                                                         │   │
│   │   User keeps NOTHING sensitive locally                  │   │
│   │   (optional local backup, but not required)             │   │
│   │                                                         │   │
│   └─────────────────────────────────────────────────────────┘   │
│                              │                                  │
│          commitment          │         encrypted_payload        │
│              │               │               │                  │
│              ▼               │               ▼                  │
│   ┌──────────────────────┐   │   ┌──────────────────────────┐   │
│   │    YELLOW NETWORK    │   │   │   BLACK AND YELLOW TEE   │   │
│   │                      │   │   │                          │   │
│   │  • Lock margin       │   │   │  • Decrypt payloads      │   │
│   │  • Against commit    │   │   │  • Store orders+secrets  │   │
│   │  • Instant updates   │   │   │  • Run matching engine   │   │
│   │                      │   │   │  • Code hash on-chain    │   │
│   │                      │◄──┼───│  • Release secrets       │   │
│   │  State channel       │   │   │    after match sigs      │   │
│   │  updates on match    │   │   │                          │   │
│   │                      │   │   │  ATTESTATION PROVES:     │   │
│   │                      │   │   │  "Running code X"        │   │
│   │                      │   │   │  X = fair matching algo  │   │
│   └──────────────────────┘   │   └──────────────────────────┘   │
│                              │               │                  │
│                              │               │ secrets          │
│                              │               ▼                  │
│                              │   ┌──────────────────────────┐   │
│                              │   │     ZK BATCH PROVER      │   │
│                              │   │                          │   │
│                              │   │  • Receives secrets      │   │
│                              │   │  • Generates proofs      │   │
│                              │   │  • Batches N trades      │   │
│                              │   │  • Posts proof on-chain  │   │
│                              │   │                          │   │
│                              │   └──────────────────────────┘   │
│                              │               │                  │
│                              │               │ ZK proof         │
│                              │               ▼                  │
│                              │   ┌──────────────────────────┐   │
│                              │   │       BASE MAINNET       │   │
│                              │   │                          │   │
│                              │   │  • Matching code hash    │   │
│                              │   │  • ZK proofs stored      │   │
│                              │   │  • Final settlements     │   │
│                              │   │  • Adjudication fallback │   │
│                              │   │                          │   │
│                              │   └──────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## The Secret Management Solution

### The Problem
```
PROBLEM:
├── User creates secret locally
├── User might lose secret (browser clear, device change)
├── Without secret, can't generate reveal
├── Without reveal, ZK proof incomplete
└── Need a backup that doesn't compromise privacy
```

### The Solution: TEE as Secret Escrow
```
SOLUTION:
├── User encrypts order + secret to TEE public key
├── TEE holds secrets securely in isolated memory
├── Secrets ONLY released after both parties sign match
├── User doesn't need to keep secret locally
└── ZK proofs generated asynchronously from released secrets
```

---

## Complete Trade Lifecycle

### Step 1: Order Creation (client-side, ~10ms)
```
order = {LONG, BTC, 0.5, $97,200, 10x}
secret = 0x7a3f...random
commitment = keccak256(order || secret) = 0xabc123...
encrypted = encrypt({order, secret}, TEE_PUBKEY)
```

### Step 2: Submit to System (~50ms)
```
→ Yellow: "Lock $4,860 margin against commitment 0xabc123"
→ TEE: encrypted payload

Yellow locks funds instantly (state channel)
TEE decrypts, stores order+secret, adds to book

PUBLIC SEES: "Someone placed an order" (no details)
```

### Step 3: Counterparty Places Order (~50ms)
```
Bob: SHORT BTC @ $97,180, 10x
Same process: commitment + encrypted payload
```

### Step 4: TEE Matches (~10ms)
```
TEE matching engine (code hash: 0xdef456 on-chain):
├── Alice: LONG 0.5 BTC @ $97,200
├── Bob: SHORT 0.5 BTC @ $97,180
├── CROSS! Execute at midpoint: $97,190
└── Create match proposal

Match proposal:
{
  match_id: "0x789...",
  alice_commitment: "0xabc123",
  bob_commitment: "0xdef456",
  price: $97,190,
  size: 0.5 BTC,
  alice_position: LONG,
  bob_position: SHORT
}
```

### Step 5: Both Sign Match (~100ms)
```
TEE sends match proposal to Alice and Bob

Alice reviews:
"I committed to LONG@$97,200, getting filled at $97,190? ✓"
Alice signs: sig_alice

Bob reviews:
"I committed to SHORT@$97,180, getting filled at $97,190? ✓"
Bob signs: sig_bob

MATCH IS NOW LOCKED
Neither can back out
```

### Step 6: State Update (~50ms)
```
TEE sends signed state update to Yellow:
├── Alice: -$4,859.50 margin locked, LONG position opened
└── Bob: -$4,859.50 margin locked, SHORT position opened

Yellow updates state channel balances instantly

✅ TRADE COMPLETE
Alice and Bob see new positions immediately
Total time: ~270ms
```

### Step 7: Secret Release (async, ~10ms)
```
TEE has both signatures → releases secrets:
├── Alice's secret: 0x7a3f...
└── Bob's secret: 0x9b2e...

Sends to ZK batch prover
```

### Step 8: Batched ZK Proof (async, every ~30 seconds)
```
ZK Prover collects last N trades (e.g., 100)

Generates proof:
"For each trade:
 - hash(order_i, secret_i) == commitment_i
 - match_price was between bid and ask
 - position updates are correct"

Proof posted on-chain (or DA layer)
```

---

## Security Properties

| Property | Guarantee | Mechanism |
|----------|-----------|-----------|
| Order privacy from traders | ✅ Cryptographic | Commitment hides until match |
| Order privacy from observers | ✅ Cryptographic | Amount encrypted to TEE |
| Order privacy from operator | ✅ Hardware | TEE isolation |
| Matching fairness | ✅ Verifiable | Code hash on-chain + attestation |
| Trade integrity | ✅ Cryptographic | Both parties sign |
| Balance correctness | ✅ Cryptographic | ZK proof |
| Secret availability | ✅ Guaranteed | TEE holds, releases on match |
| Fund safety | ✅ Protocol | Yellow adjudication fallback |

---

## Trust Assumptions

### You Trust:
1. **TEE hardware is secure** - Intel/AMD/AWS haven't backdoored it
2. **Matching code is what's committed** - Verified via attestation
3. **Yellow protocol works** - State channels + adjudication
4. **ZK proofs are sound** - Math

### You Don't Trust:
- ❌ Black And Yellow operator (can't see orders - TEE)
- ❌ Black And Yellow operator (can't manipulate matching - verified code)
- ❌ Other traders (can't see your orders - commitments)
- ❌ Network observers (can't see amounts - encrypted)

---

## Tech Stack

| Component | Technology |
|-----------|------------|
| State Channels | Yellow Network / Nitrolite |
| TEE | AWS Nitro Enclaves (mock for hackathon) |
| ZK Proofs | Batched SNARKs |
| Cross-chain Deposits | LI.FI Composer |
| Settlement Chain | Base Mainnet |
| Frontend | React + TypeScript |
| Price Feeds | Pyth / Chainlink |

---

## Prize Targets

| Sponsor | Track | Integration |
|---------|-------|-------------|
| **Yellow Network** ($15k) | State Channels | Core settlement infrastructure |
| **LI.FI** ($6k) | Cross-chain | Deposit from any chain |
| **Uniswap** ($5k) | Privacy DeFi | Private orderbook concept |

**Max Potential: $26k**

---

## Hackathon Scope

### Must Have
- [ ] Order creation: commitment + encrypted payload
- [ ] Yellow integration: margin locking against commitment
- [ ] Matching engine: in simulated/mock TEE
- [ ] Match signing flow: both parties confirm
- [ ] State channel update on match
- [ ] Secret release after match
- [ ] LI.FI cross-chain deposits
- [ ] Basic UI with hidden orderbook visualization
- [ ] 3 perpetual markets: BTC, ETH, SOL

### Nice to Have
- [ ] Actual TEE deployment (AWS Nitro)
- [ ] ZK batch prover
- [ ] On-chain code hash commitment
- [ ] Position management (close, add margin)
- [ ] Liquidation engine

---

## Demo Script

1. "Here's Black And Yellow - a private perpetuals exchange"
2. Alice deposits via LI.FI (any chain → Base USDC)
3. Alice places hidden LONG order (show commitment only)
4. Bob places hidden SHORT order
5. Match found! Both sign
6. Positions open instantly (Yellow state channels)
7. "In production: TEE ensures operator can't see orders, verified code ensures fair matching, ZK proofs verify all trades"

---

## The Pitch

> "Black And Yellow is a private perpetuals exchange where your orders are hidden from everyone — including us. Orders are encrypted to a TEE that runs verified matching code. When orders match, both parties sign, balances update instantly via Yellow, and batched ZK proofs make everything verifiable on-chain. The result: CEX speed, DEX trust, and privacy by default."
