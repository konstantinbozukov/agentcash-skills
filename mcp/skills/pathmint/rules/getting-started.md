# Getting Started

## Setup

1. **Install the agentcash MCP:**
   ```bash
   npx agentcash@latest install --client cursor -y
   ```

2. **Check wallet:**
```mcp
agentcash.get_balance()
```

3. **Fund wallet** (if needed):
   - Redeem invite: `agentcash.redeem_invite(code="YOUR_CODE")`
   - Or call `agentcash.list_accounts()` for Base deposit links

4. **Discover endpoints (read-only):**
```mcp
agentcash.discover_api_endpoints(url="https://pathmint.edge.report")
```

Free: `GET https://pathmint.edge.report/v1/paths`, quote URLs, `GET https://pathmint.edge.report/v1/clearance`

Do not buy pay-clearance ($0.25) to protect $0.001 dust payments.

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "MCP tool not found" | Run install with `--client cursor`, restart Cursor |
| "Insufficient balance" | Fund wallet with USDC |
| Compile not worth it | Use free quote; use ping for cheap SKUs |