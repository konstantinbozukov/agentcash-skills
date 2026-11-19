---
name: pathmint
description: |
  Verify x402 listings before you pay USDC on Base. Pathmint at pathmint.edge.report — live vs ghost ping, listing price checks, seller payment history, and optional pay-clearance compiles.

  USE FOR:
  - Pinging whether an x402 URL is live or a ghost listing before payment
  - Comparing bazaar listed price to live 402 price
  - Checking outside USDC payment history for an x402 payTo
  - Messari pay-gate before expensive Messari calls
  - CLEAR / CAUTION / ABORT clearance for larger stakes
  - Free weekly map of x402 sellers by on-chain USDC

  TRIGGERS:
  - "ghost x402", "is this endpoint live", "verify before pay"
  - "listing check", "seller payments", "payTo"
  - "x402 clearance", "Messari gate", "pathmint"

  Use agentcash.fetch with exact paths from the Quick Reference. Default: GET /v1/ping @ $0.01.
homepage: https://pathmint.edge.report
mcp:
  - agentcash
metadata:
  version: 1
---

# Pathmint (x402 verification on Base)

Proven Path Exchange at `https://pathmint.edge.report`. Linked from Edge Signals examples (ping `https://signals.edge.report/v1/gas` and token SKUs).

## Setup

See [rules/getting-started.md](rules/getting-started.md).

## Quick Reference

| Task | Endpoint | Price | Best For |
|------|----------|-------|----------|
| **Live vs dead ping** | `GET https://pathmint.edge.report/v1/ping?url={encodedUrl}` | $0.01 | Default before any 402 payment |
| Listing check | `GET https://pathmint.edge.report/v1/listing-check?url={encodedUrl}` | $0.02 | Bazaar vs live 402 price |
| Seller payments | `GET https://pathmint.edge.report/v1/seller-payments?url=` or `?payTo=` | $0.02 | Outside USDC history |
| Messari gate | `GET https://pathmint.edge.report/v1/messari-gate?q={job}` | $0.02 | SKIP / PAY / ABORT |
| Pay clearance | `POST https://pathmint.edge.report/v1/paths/x402-pay-clearance/compile` | $0.25 | CLEAR / CAUTION / ABORT |
| Live seller | `POST https://pathmint.edge.report/v1/paths/x402-live-seller/compile` | $0.35 | Sellers that answer + get paid |
| Merchant truth | `POST https://pathmint.edge.report/v1/paths/x402-merchant-truth/compile` | $0.55 | PayTo payer stats |
| Clearance map | `GET https://pathmint.edge.report/v1/clearance` | Free | Weekly PAY / CAUTION / IGNORE |

## Ping before you pay

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/ping?url=https%3A%2F%2Fsignals.edge.report%2Fv1%2Ftoken-verdict%3Ftoken%3D0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913%26chain%3Dbase",
  method="GET"
)
```

Optional `payTo` query on the URL when needed.

## Listing check and seller payments

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/listing-check?url=https%3A%2F%2Fsignals.edge.report%2Fv1%2Fgas",
  method="GET"
)
```

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/seller-payments?payTo=0xE2520f1497ee47645072a6214304807BC3340D58",
  method="GET"
)
```

## Messari gate

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/messari-gate?q=btc+asset+details",
  method="GET"
)
```

## Pay clearance (stake ≥ ~$0.50)

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/paths/x402-pay-clearance/compile",
  method="POST",
  body={
    "payTo": "0xMERCHANT",
    "amountUsd": 5
  }
)
```

## Live seller compile

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/paths/x402-live-seller/compile",
  method="POST",
  body={
    "job": "wallet balance on Base",
    "amountUsd": 5
  }
)
```

## Merchant truth compile

```mcp
agentcash.fetch(
  url="https://pathmint.edge.report/v1/paths/x402-merchant-truth/compile",
  method="POST",
  body={
    "payTo": "0xMERCHANT",
    "lookbackDays": 7
  }
)
```

## Workflows

### Pay an x402 endpoint safely

- [ ] Ping ($0.01)
- [ ] Optional listing-check / clearance for large stakes
- [ ] Pay target SKU ([edge-signals](../edge-signals/))

## Related

[edge-signals](../edge-signals/) · Agent pack: https://pathmint.edge.report/v1/agent-tools.json