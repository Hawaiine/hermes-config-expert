# AgentKey MCP Setup Reference

AgentKey provides a unified gateway to ~1800+ live APIs (web search, social media, crypto, on-chain data, news, and more) through 4 MCP tools. Setup requires an API key from https://console.agentkey.app/.

## MCP Config

```yaml
mcp_servers:
  agentkey:
    url: https://api.agentkey.app/v1/mcp
    headers:
      Authorization: Bearer ${MCP_AGENTKEY_API_KEY}
    enabled: true
```

## Installation

### Via Hermes MCP CLI

```bash
hermes mcp add agentkey --url https://api.agentkey.app/v1/mcp
```

Prompts:
1. "Does this server require authentication?" → `y`
2. "API key / Bearer token" → `ak_...`
3. "Enable all N tools?" → `y`

The key is saved to `.env` as `MCP_AGENTKEY_API_KEY`.

### Non-Interactive (subprocess/script)

```python
import subprocess
api_key = "ak_..."
result = subprocess.run(
    ['hermes', 'mcp', 'add', 'agentkey',
     '--url', 'https://api.agentkey.app/v1/mcp'],
    input=f'y\n{api_key}\ny\n',
    capture_output=True, text=True, timeout=30
)
```

### Manual Config (for clients without `hermes mcp add` support)

For agents where `hermes mcp add` doesn't work, add this JSON to the agent's MCP config file:

```json
{
  "mcpServers": {
    "agentkey": {
      "type": "http",
      "url": "https://api.agentkey.app/v1/mcp",
      "headers": { "Authorization": "Bearer ak_..." }
    }
  }
}
```

## MCP Tools (4 total)

| Tool | Purpose |
|------|---------|
| `list_tools` | Browse tool tree by prefix. Empty prefix → top categories. `social` → platforms. `social/twitter` → endpoints |
| `find_tools` | Semantic search across all ~1800 tools. Supports natural language queries (CN/EN/mixed) and platform aliases (推特→twitter, 小红书→xiaohongshu) |
| `describe_tool` | Get full parameters + examples + per-call credit cost. **Required before execute.** |
| `execute_tool` | Execute any tool by name + params. All calls go through this single gateway |

## Discovery Pattern

```
list_tools()                                     → top categories
list_tools(prefix="social/xiaohongshu")          → xiaohongshu endpoints
describe_tool(name="xiaohongshu/search_notes")   → params + cost
execute_tool(name="agentkey_social", params={path: "xiaohongshu/search_notes", params: {keyword: "防晒霜"}})
```

Or use natural-language semantic search:
```
find_tools(q="帮我在小红书上搜防晒霜的笔记") → matched endpoints with scores
```

## Common Calls (No Discovery Needed)

```python
# Web search
execute_tool(name="agentkey_search", params={query: "AI news", type: "news", num: 5})

# Scrape URL
execute_tool(name="agentkey_scrape", params={url: "https://example.com"})

# Crypto prices
execute_tool(name="agentkey_crypto", params={type: "market/quotes", params: {symbol: "BTC"}})
```

## Cost Awareness

Each call costs credits. Before bulk operations (≥3 calls or ≥10 estimated credits):

1. Call `agentkey_account` (free) to read balance
2. Call `describe_tool` to read `cost.credits_per_call`
3. Estimate total = credits_per_call × N
4. Present plan to user, wait for confirmation

Key patterns:
- Failed calls (4xx/5xx) are **not** billed
- Use `describe_tool` for path-dependent costs (social/crypto top-level tools have path-dependent pricing)
- Switch providers in search tools (brave, perplexity, serper, tavily) for cheaper bulk runs

## Error Handling

| Error | Action |
|-------|--------|
| `Authentication failed` | Key invalid. Get new one at https://console.agentkey.app/ |
| `Insufficient credits` | Top up at https://console.agentkey.app/ |
| `Rate limited` | Wait and retry |
| `not_found` | Report to user. Do NOT retry with guessed IDs |
| Missing required param | Fix using `suggestion` field, retry once |

## Skill File

The AgentKey skill lives at `skills/agentkey/SKILL.md` and provides the full workflow: upgrade checks, setup, status, and query modes. The skill is the authoritative usage guide — this reference covers only the MCP connectivity setup.
