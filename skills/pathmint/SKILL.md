---
name: pathmint
description: |
  Verify x402 listings before you pay USDC on Base. Pathmint at pathmint.edge.report — live vs ghost ping, listing price checks, seller payment history, and optional pay-clearance compiles.

  USE FOR:
  - Pinging whether an x402 URL is live or a ghost listing before payment
  - Comparing bazaar listed price to live 402 price (PAY / MISMATCH / DEAD)
  - Checking outside USDC payment history for an x402 payTo
  - Messari pay-gate (SKIP vs PAY vs ABORT) before expensive Messari calls
  - CLEAR / CAUTION / ABORT clearance when stake is large enough to justify compile fees
  - Free weekly map of x402 sellers by on-chain USDC

  TRIGGERS:
  - "ghost x402", "is this endpoint live", "verify before pay"
  - "listing check", "price mismatch", "seller payments", "payTo"
  - "x402 clearance", "CLEAR CAUTION ABORT", "pay-clearance"
  - "Messari gate", "SKIP_MESSARI"
  - "pathmint", "proven path"

  Use exact paths from the Quick Reference. Default SKU: GET /v1/ping @ $0.01. Do not bazaar-search for ghost checks — use these URLs.
homepage: https://pathmint.edge.report
metadata:
  version: 1
---

# Pathmint (x402 verification on Base)

Proven Path Exchange for agents at `https://pathmint.edge.report`. Pay with USDC on Base via x402. Discovery is linked from the Edge Signals ecosystem (example ping target: `https://signals.edge.report/v1/gas`).

## Setup

See [rules/getting-started.md](rules/getting-started.md) for installation, free quote endpoints, and OpenAPI.

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| **Live vs dead ping** | `GET https://pathmint.edge.report/v1/ping?url={encodedUrl}` | $0.01 | Default — before you settle any 402 |
| Listing vs live 402 | `GET https://pathmint.edge.report/v1/listing-check?url={encodedUrl}` | $0.02 | PAY / MISMATCH / SINGLE / UNLISTED / DEAD |
| Seller payment history | `GET https://pathmint.edge.report/v1/seller-payments?url={encodedUrl}` | $0.02 | Outside USDC, last pay, self-traffic removed |
| Seller by payTo | `GET https://pathmint.edge.report/v1/seller-payments?payTo={0x}` | $0.02 | Same scan when you only have the wallet |
| Messari gate | `GET https://pathmint.edge.report/v1/messari-gate?q={job}` | $0.02 | SKIP_MESSARI / PAY_MESSARI / ABORT |
| Pay clearance compile | `POST https://pathmint.edge.report/v1/paths/x402-pay-clearance/compile` | $0.25 | CLEAR / CAUTION / ABORT (stake ≥ ~$0.50) |
| Live seller compile | `POST https://pathmint.edge.report/v1/paths/x402-live-seller/compile` | $0.35 | Up to 3 live sellers for a job query |
| Merchant truth compile | `POST https://pathmint.edge.report/v1/paths/x402-merchant-truth/compile` | $0.55 | Outside payers vs self-seed for a payTo |
| Weekly clearance map | `GET https://pathmint.edge.report/v1/clearance` | Free | PAY / CAUTION / IGNORE on top sellers |
| Path manifests | `GET https://pathmint.edge.report/v1/paths` | Free | Compile SKUs and economics |
| Buy vs skip quote | `GET https://pathmint.edge.report/v1/paths/x402-pay-clearance/quote?stake_usd={n}` | Free | Whether compile is worth it |
| Catalog | `GET https://pathmint.edge.report/v1/catalog` | Free | Prices and examples |

Optional on ping: `payTo` — `0x` wallet from the 402 body. On messari-gate: optional `url` instead of `q`.

## Ping before you pay (default)

```bash
npx agentcash@latest fetch "https://pathmint.edge.report/v1/ping?url=https%3A%2F%2Fsignals.edge.report%2Fv1%2Ftoken-verdict%3Ftoken%3D0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913%26chain%3Dbase"
```

With optional payTo from the 402:

```bash
npx agentcash@latest fetch "https://pathmint.edge.report/v1/ping?url=https%3A%2F%2Fsignals.edge.report%2Fv1%2Fgas&payTo=0xE2520f1497ee47645072a6214304807BC3340D58"
```

**Query parameters:**
- `url` — Full x402 resource URL you are about to pay (required)
- `payTo` — Optional `0x` payee from the 402

**Returns:** `live` / `dead` (ghost listing, timeout, non-compliant host). Not full pay-clearance.

## Listing check and seller payments

```bash
npx agentcash@latest fetch "https://pathmint.edge.report/v1/listing-check?url=https%3A%2F%2Fsignals.edge.report%2Fv1%2Fgas"
```

```bash
npx agentcash@latest fetch "https://pathmint.edge.report/v1/seller-payments?url=https%3A%2F%2Fsignals.edge.report%2Fv1%2Fgas"
```

```bash
npx agentcash@latest fetch "https://pathmint.edge.report/v1/seller-payments?payTo=0xE2520f1497ee47645072a6214304807BC3340D58"
```

## Messari gate

Before paying Messari ($0.10–$0.25+). Returns routing advice, not Messari data.

```bash
npx agentcash@latest fetch "https://pathmint.edge.report/v1/messari-gate?q=btc+asset+details"
```

## Pay clearance (higher stakes only)

Skip compile for $0.001–$0.02 dust SKUs. Use free quote first:

```bash
curl -sS "https://pathmint.edge.report/v1/paths/x402-pay-clearance/quote?stake_usd=5"
```

```bash
npx agentcash@latest fetch https://pathmint.edge.report/v1/paths/x402-pay-clearance/compile -m POST -b '{"payTo":"0xMERCHANT","amountUsd":5}'
```

**Body (one of):**
- `payTo` + `amountUsd` (or `stakeUsd`) — stake you are about to send
- `paymentRequired` — raw 402 JSON body instead of `payTo`

**Returns:** `CLEAR`, `CAUTION`, or `ABORT` (do not pay merchant on ABORT).

## Live seller compile

```bash
npx agentcash@latest fetch https://pathmint.edge.report/v1/paths/x402-live-seller/compile -m POST -b '{"job":"wallet balance on Base","amountUsd":5}'
```

**Body:**
- `job` (required) — what you need a seller for
- `amountUsd` or `stakeUsd` — USDC you would send the chosen seller

## Merchant truth compile

```bash
npx agentcash@latest fetch https://pathmint.edge.report/v1/paths/x402-merchant-truth/compile -m POST -b '{"payTo":"0xMERCHANT","lookbackDays":7}'
```

**Body:**
- `payTo` (required)
- `lookbackDays` — 1–30 (default 7)
- `seedWallets` — optional array of `0x` to treat as operator self-traffic

## Free weekly map

```bash
curl -sS https://pathmint.edge.report/v1/clearance
```

## Workflows

### Pay an x402 endpoint safely

- [ ] `GET /v1/ping?url=` on the exact resource URL ($0.01)
- [ ] If stake ≥ $0.50 and merchant is unfamiliar, optional listing-check ($0.02) then pay-clearance quote + compile ($0.25)
- [ ] If live, pay the target SKU (e.g. [edge-signals](../edge-signals/) Token Verdict)

### Pick a seller for a job

- [ ] Read free `GET /v1/paths`
- [ ] `POST .../x402-live-seller/compile` with `job` and `amountUsd`
- [ ] Ping each returned seller URL before paying

## Related

Token and gas SKUs live on [edge-signals](../edge-signals/) at `https://signals.edge.report`. Agent pack: https://pathmint.edge.report/v1/agent-tools.json · MCP: https://pathmint.edge.report/mcp