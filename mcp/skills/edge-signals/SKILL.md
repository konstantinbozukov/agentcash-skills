---
name: edge-signals
description: |
  Pre-trade crypto agent signals on Base via x402 at signals.edge.report. USDC micropayments, no API keys.

  USE FOR:
  - Graded token due diligence before buy, bridge, or treasury allocation (Token Verdict)
  - Honeypot and token-security screening on EVM chains
  - Live gas fee snapshot before broadcasting a transaction
  - Wallet balances, nonce, ERC20 balanceOf on Base/Ethereum
  - Token spot price, crypto news brief, Polymarket odds
  - DeFi funding-rate and spread signals

  TRIGGERS:
  - "token verdict", "token risk", "honeypot", "scam token", "rug check"
  - "before I buy", "pre-trade", "due diligence", "SAFE CAUTION AVOID"
  - "gas on Base", "maxFeePerGas", "nonce", "wallet balance"
  - "funding rate", "Hyperliquid", "crypto brief", "Polymarket"

  Use agentcash.fetch with exact paths from the Quick Reference table. Prefer Token Verdict ($0.01) over token-risk ($0.001) when you need a graded band and score.
homepage: https://signals.edge.report
mcp:
  - agentcash
metadata:
  version: 1
---

# Edge Signals (x402 on Base)

Agent-oriented market and chain signals at `https://signals.edge.report`. Pay with USDC on Base via x402.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation and discovery.

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| **Token Verdict (lead SKU)** | `GET https://signals.edge.report/v1/token-verdict?token={0x}&chain={chain}` | $0.01 | Graded A–F, SAFE\|CAUTION\|AVOID, score 0–100 |
| Token risk (light) | `GET https://signals.edge.report/v1/token-risk?address={0x}&chain={chain}` | $0.001 | Honeypot / tax / owner flags |
| Token risk (POST) | `POST https://signals.edge.report/v1/token-risk` | $0.001 | Same check with JSON body |
| Gas snapshot | `GET https://signals.edge.report/v1/gas` | $0.001 | maxFeePerGas / priority fee gwei |
| Wallet bundle | `GET https://signals.edge.report/v1/wallet/full?address={0x}` | $0.005 | ETH + USDC + nonce + gas |
| Wallet | `GET https://signals.edge.report/v1/wallet?address={0x}` | $0.001 | Balances + nonce |
| ERC20 balance | `GET https://signals.edge.report/v1/erc20-balance?address={0x}&token={0x}&chain=base` | $0.001 | balanceOf + decimals |
| Nonce | `GET https://signals.edge.report/v1/nonce?address={0x}&chain=base` | $0.001 | Transaction nonce |
| Token price | `GET https://signals.edge.report/v1/token?id={id}` | $0.001 | USD price / mcap |
| News brief | `GET https://signals.edge.report/v1/brief?topic=crypto` | $0.001 | Headlines + sentiment |
| Polymarket | `GET https://signals.edge.report/v1/polymarket?q={query}` | $0.001 | Prediction-market odds |
| Funding extremes | `GET https://signals.edge.report/v1/signals/funding` | $0.001 | Hyperliquid funding APR |
| Funding spreads | `GET https://signals.edge.report/v1/signals/spreads` | $0.001 | HL vs Bitget spreads |
| Signal bundle | `GET https://signals.edge.report/v1/signals` | $0.002 | Combined DeFi signals |
| Free catalog | `GET https://signals.edge.report/v1/catalog` | Free | Machine-readable prices |

`chain`: `base`, `ethereum`, `bsc`, `arbitrum`, `optimism`, `polygon`. `brief` topic: `crypto`, `base`, `defi`.

## Token Verdict (pre-trade, Base)

```mcp
agentcash.fetch(
  url="https://signals.edge.report/v1/token-verdict?token=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913&chain=base",
  method="GET"
)
```

**Query parameters:**
- `token` — ERC20 contract (required)
- `chain` — EVM chain (use `base` for Base)

## Token risk

```mcp
agentcash.fetch(
  url="https://signals.edge.report/v1/token-risk?address=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913&chain=base",
  method="GET"
)
```

```mcp
agentcash.fetch(
  url="https://signals.edge.report/v1/token-risk",
  method="POST",
  body={
    "address": "0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913",
    "chain": "base"
  }
)
```

## Gas, wallet, and market SKUs

```mcp
agentcash.fetch(url="https://signals.edge.report/v1/gas", method="GET")
```

```mcp
agentcash.fetch(
  url="https://signals.edge.report/v1/wallet/full?address=0xYOUR_WALLET",
  method="GET"
)
```

```mcp
agentcash.fetch(
  url="https://signals.edge.report/v1/brief?topic=base&limit=10",
  method="GET"
)
```

```mcp
agentcash.fetch(url="https://signals.edge.report/v1/signals", method="GET")
```

## Workflows

### Pre-trade token check

- [ ] Optional: `agentcash.get_balance()`
- [ ] Token Verdict on contract + chain
- [ ] Stop on CAUTION or AVOID

### Submit an EVM transaction

- [ ] Gas snapshot, then nonce for sender

## Related

[pathmint](../pathmint/) — ping listings before pay. OpenAPI: https://signals.edge.report/openapi.json