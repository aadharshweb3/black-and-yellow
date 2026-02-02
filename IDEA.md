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
