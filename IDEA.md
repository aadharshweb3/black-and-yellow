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
