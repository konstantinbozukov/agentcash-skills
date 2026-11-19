# Getting Started

## Setup

1. **Install the agentcash CLI:**
   ```bash
   npm install -g agentcash
   ```

2. **Register the origin (optional):**
   ```bash
   npx agentcash add https://signals.edge.report
   ```

3. **Read the free catalog** (prices and example URLs):
   ```bash
   curl -sS https://signals.edge.report/v1/catalog
   ```

4. **Check wallet:**
   ```bash
   npx agentcash@latest balance
   ```

5. **Fund wallet** (if needed):
   - Redeem invite: `npx agentcash@latest redeem YOUR_CODE`
   - Or run `npx agentcash@latest accounts` for Base or Solana deposit links

## Discovery (read-only)

```bash
npx agentcash@latest discover https://signals.edge.report
npx agentcash@latest check "https://signals.edge.report/v1/token-verdict?token=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913&chain=base"
```

OpenAPI: https://signals.edge.report/openapi.json · Agent card: https://signals.edge.report/.well-known/agent.json

Payment: x402 USDC on Base (`eip155:8453`). Rate limit: 100 requests per IP per day per path.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC on Base |
| "Payment failed" | Check balance, retry (transient errors) |
| Unsure if an x402 listing is live | Use the [pathmint](../pathmint/) skill: `GET https://pathmint.edge.report/v1/ping?url=...` @ $0.01 |