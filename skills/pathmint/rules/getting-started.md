# Getting Started

## Setup

1. **Install the agentcash CLI:**
   ```bash
   npm install -g agentcash
   ```

2. **Register the origin (optional):**
   ```bash
   npx agentcash add https://pathmint.edge.report
   ```

3. **Read the free catalog:**
   ```bash
   curl -sS https://pathmint.edge.report/v1/catalog
   ```

4. **Check wallet:**
   ```bash
   npx agentcash@latest balance
   ```

5. **Fund wallet** (if needed):
   - Redeem invite: `npx agentcash@latest redeem YOUR_CODE`
   - Or run `npx agentcash@latest accounts` for Base deposit links

## Discovery (read-only)

```bash
npx agentcash@latest discover https://pathmint.edge.report
npx agentcash@latest check "https://pathmint.edge.report/v1/ping?url=https%3A%2F%2Fsignals.edge.report%2Fv1%2Fgas"
```

Free planning endpoints: `GET https://pathmint.edge.report/v1/paths`, `GET .../quote?stake_usd=...`, `GET https://pathmint.edge.report/v1/clearance`

OpenAPI: https://pathmint.edge.report/openapi.json · Linked from Edge Signals discovery at https://signals.edge.report/v1/catalog (same payee ecosystem; Pathmint pings resources such as `https://signals.edge.report/v1/gas` before you pay).

Payment: x402 USDC on Base. Do **not** buy pay-clearance ($0.25) to protect a $0.001 dust call — use ping ($0.01) or listing-check ($0.02) instead.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "Command not found" | Run `npm install -g agentcash` |
| "Insufficient balance" | Fund wallet with USDC |
| Compile fee rejected | Ensure stake ≥ $0.50 before pay-clearance; use ping for cheap SKUs |
| "Payment failed" | Check balance, retry |