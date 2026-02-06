# BLACK AND YELLOW

> CEX speed, DEX trust, privacy by default.

A private perpetuals exchange where orders are encrypted, matched in a TEE, and settled via batched ZK proofs — built for HackMoney 2026.

---

## Overview

BLACK AND YELLOW is a perpetual futures exchange where:

- **Orders are encrypted** — Submitted to an AWS Nitro TEE, invisible to the operator
- **Matching is verifiable** — TEE code hash is publicly attestable
- **Settlement is instant** — Yellow Network state channels update balances off-chain
- **Proofs are batched** — ZK SNARKs verify trade integrity on-chain

---

## Tech Stack

### Core Infrastructure
| Component | Technology |
|-----------|-----------|
| **State Channels** | Yellow Network (instant settlement) |
| **Order Privacy** | AWS Nitro Enclaves (TEE) |
| **On-chain Verification** | Batched SNARKs |
| **Cross-chain Deposits** | LI.FI |
| **Price Feeds** | Chainlink / Pyth |

### Frontend
- **Framework:** React + TypeScript
- **Styling:** Tailwind CSS
- **Web3:** wagmi + viem

---

## How It Works

1. **Deposit** — User deposits collateral from any chain via LI.FI
2. **Open Channel** — Yellow Network state channel is opened
3. **Place Order** — Order is commitment-hashed locally, encrypted payload sent to TEE
4. **Match** — TEE matches orders fairly, both parties co-sign the state update
5. **Settle** — Batch of trades is ZK-proven and verified on-chain

---

## Markets

| Market | Leverage | Fee |
|--------|----------|-----|
| BTC-PERP | 1x - 10x | 0.05% / 0.02% |
| ETH-PERP | 1x - 10x | 0.05% / 0.02% |
| SOL-PERP | 1x - 10x | 0.08% / 0.03% |

---

## Project Structure

```
black-and-yellow/
├── IDEA.md                  # Detailed concept and architecture
├── PROMPT.md                # AI development prompts
├── UI_DESIGN_SYSTEM.md      # Design tokens, components, animations
└── WIREFRAMES.md            # Page layouts and interaction flows
```

---

## Documentation

- [IDEA.md](./IDEA.md) — Full concept, architecture, and trade lifecycle
- [UI_DESIGN_SYSTEM.md](./UI_DESIGN_SYSTEM.md) — Design system specification
- [WIREFRAMES.md](./WIREFRAMES.md) — Page wireframes and interaction flows

---

## License

MIT
