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
agentcash.discover_api_endpoints(url="https://signals.edge.report")
```

```mcp
agentcash.check_endpoint_schema(url="https://signals.edge.report/v1/token-verdict?token=0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913&chain=base")
```

OpenAPI: https://signals.edge.report/openapi.json

## Troubleshooting

| Issue | Solution |
|-------|----------|
| "MCP tool not found" | Run install with `--client cursor`, restart Cursor |
| "Insufficient balance" | Fund wallet with USDC on Base |
| "Payment failed" | Check balance, retry |
| Unsure if listing is live | [pathmint](../pathmint/) ping @ $0.01 |