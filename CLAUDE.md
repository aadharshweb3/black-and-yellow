# Claude Code Instructions

## Auto-Commit Policy

After every meaningful code change, **automatically create a git commit** with a descriptive message. Do NOT wait for the user to ask you to commit.

### Commit Rules

1. **Commit after every change** — each logical unit of work gets its own commit
2. **Descriptive messages** — clearly describe what changed and why, not just "update file"
3. **Use conventional format**:
   - `feat: <description>` — new feature or functionality
   - `fix: <description>` — bug fix
   - `refactor: <description>` — code restructuring without behavior change
   - `docs: <description>` — documentation changes
   - `style: <description>` — formatting, whitespace, etc.
   - `chore: <description>` — tooling, config, dependencies
4. **Message body** — for complex changes, add a body explaining the reasoning
5. **Stage specific files** — never use `git add -A` or `git add .`, always add files by name
6. **Never commit secrets** — no `.env`, private keys, or credentials

### Examples

```
feat: add market liquidation logic for expired positions

- Calculate PnL based on oracle price at expiry
- Handle partial fills and remaining collateral return
- Emit MarketSettled event with final prices
```

```
fix: prevent double-spending in deposit flow

Check existing pending deposits before creating new ones.
Was allowing users to submit multiple deposits for the same market.
```

```
docs: expand architecture section with sequence diagrams
```
