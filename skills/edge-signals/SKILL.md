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

  Use `npx agentcash@latest fetch` with exact paths from the Quick Reference table. Prefer Token Verdict ($0.01) over token-risk ($0.001) when you need a graded band and score. Free catalog: GET https://signals.edge.report/v1/catalog
homepage: https://signals.edge.report
metadata:
  version: 1
---

# Edge Signals (x402 on Base)

Agent-oriented market and chain signals at `https://signals.edge.report`. Pay with USDC on Base via x402.

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation, catalog, and discovery links.

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| **Token Verdict (lead SKU)** | `GET https://signals.edge.report/v1/token-verdict?token={0x}&chain={chain}` | $0.01 | Graded A–F, SAFE\|CAUTION\|AVOID, score 0–100 before trade |
| Token risk (light) | `GET https://signals.edge.report/v1/token-risk?address={0x}&chain={chain}` | $0.001 | Honeypot / tax / owner flags |
| Token risk (POST) | `POST https://signals.edge.report/v1/token-risk` | $0.001 | Same check with JSON body |
| Gas snapshot | `GET https://signals.edge.report/v1/gas` | $0.001 | maxFeePerGas / priority fee gwei |
| Wallet bundle | `GET https://signals.edge.report/v1/wallet/full?address={0x}` | $0.005 | ETH + USDC + nonce + gas |
| Wallet | `GET https://signals.edge.report/v1/wallet?address={0x}` | $0.001 | Balances + nonce |
| ERC20 balance | `GET https://signals.edge.report/v1/erc20-balance?address={0x}&token={0x}&chain=base` | $0.001 | balanceOf + decimals |
| Nonce | `GET https://signals.edge.report/v1/nonce?address={0x}&chain=base` | $0.001 | eth_getTransactionCount |
| Token price | `GET https://signals.edge.report/v1/token?id={coingeckoIdOrContract}` | $0.001 | USD price / mcap |
| News brief | `GET https://signals.edge.report/v1/brief?topic=crypto` | $0.001 | Headlines + sentiment |
| Polymarket | `GET https://signals.edge.report/v1/polymarket?q={query}` | $0.001 | Prediction-market odds |
| Funding extremes | `GET https://signals.edge.report/v1/signals/funding` | $0.001 | Hyperliquid funding APR |
| Funding spreads | `GET https://signals.edge.report/v1/signals/spreads` | $0.001 | HL vs Bitget spreads |
| Signal bundle | `GET https://signals.edge.report/v1/signals` | $0.002 | Funding + arb + liquidations |
| Free catalog | `GET https://signals.edge.report/v1/catalog` | Free | Machine-readable prices |
| Free proof | `GET https://signals.edge.report/v1/proof.json` | Free | On-chain traction stats |

`chain` for token endpoints: `base`, `ethereum`, `bsc`, `arbitrum`, `optimism`, `polygon`. `brief` topic: `crypto`, `base`, `defi` (optional `limit` 3–20).

## Token Verdict (pre-trade, Base)

Graded due diligence before buy, bridge, or treasury allocation. Richer than token-risk at one cent.

```bash
npx agentcash@latest fetch "https://signals.edge.report/v1/token-verdict?token=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913&chain=base"
```

**Query parameters:**
- `token` — ERC20 contract (`0x` + 40 hex chars, required)
- `chain` — EVM chain (default mindset: `base`)

**Returns:** Letter grade (A–F), band `SAFE|CAUTION|AVOID`, numeric score 0–100, and reason strings combining scanner flags, on-chain metadata, and liquidity hints.

## Token risk (quick screen)

```bash
npx agentcash@latest fetch "https://signals.edge.report/v1/token-risk?address=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913&chain=base"
```

```bash
npx agentcash@latest fetch https://signals.edge.report/v1/token-risk -m POST -b '{"address":"0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913","chain":"base"}'
```

**Returns:** `verdict` avoid|caution|ok, honeypot/tax/blacklist/owner fields.

## Gas before you send a tx

```bash
npx agentcash@latest fetch https://signals.edge.report/v1/gas
```

**Returns:** `base` (and `ethereum` when configured) with `maxFeePerGasGwei` and `maxPriorityFeePerGasGwei`.

## Wallet and chain reads

```bash
npx agentcash@latest fetch "https://signals.edge.report/v1/wallet/full?address=0xYOUR_WALLET"
```

```bash
npx agentcash@latest fetch "https://signals.edge.report/v1/erc20-balance?address=0xYOUR_WALLET&token=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913&chain=base"
```

```bash
npx agentcash@latest fetch "https://signals.edge.report/v1/nonce?address=0xYOUR_WALLET&chain=base"
```

## Market and research SKUs

```bash
npx agentcash@latest fetch "https://signals.edge.report/v1/token?id=ethereum"
```

```bash
npx agentcash@latest fetch "https://signals.edge.report/v1/brief?topic=base&limit=10"
```

```bash
npx agentcash@latest fetch "https://signals.edge.report/v1/polymarket?q=bitcoin"
```

```bash
npx agentcash@latest fetch https://signals.edge.report/v1/signals/funding
```

```bash
npx agentcash@latest fetch https://signals.edge.report/v1/signals
```

## Workflows

### Pre-trade token check (recommended)

- [ ] (Optional) Check balance: `npx agentcash@latest balance`
- [ ] Run **Token Verdict** on the contract and chain you will trade on
- [ ] If band is CAUTION or AVOID, stop or escalate; use token-risk only for a cheaper re-check

```bash
npx agentcash@latest fetch "https://signals.edge.report/v1/token-verdict?token=CONTRACT&chain=base"
```

### Submit an EVM transaction

- [ ] Fetch gas snapshot
- [ ] Fetch nonce for the sender
- [ ] Broadcast tx with fees from the gas response

```bash
npx agentcash@latest fetch https://signals.edge.report/v1/gas
```

```bash
npx agentcash@latest fetch "https://signals.edge.report/v1/nonce?address=SENDER&chain=base"
```

## Related

Before paying unfamiliar x402 listings (including other origins), use the [pathmint](../pathmint/) skill: `GET https://pathmint.edge.report/v1/ping?url=...` @ $0.01.

Agent pack (gas-first): https://signals.edge.report/v1/agent-tools.json · OpenAPI: https://signals.edge.report/openapi.json