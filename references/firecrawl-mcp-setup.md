# Firecrawl MCP — MCP Server 集成示例

> 对应的 Hermes 配置模式：**MCP Server 接入 + 工具注入**

## 模式说明

- MCP Server 接入：通过 `hermes mcp add` 连接外部工具服务
- 工具注入：MCP 工具以 `firecrawl_*` 前缀自动成为 Hermes 原生工具
- 凭证管理：API Key 通过 `.env` 注入，config.yaml 中引用 `${FIRECRAWL_API_KEY}`
- 适用场景：需要扩展 Hermes 工具集（搜索、爬虫、监控、研究等）

---

# Firecrawl MCP Setup Reference

Session-specific setup details for Firecrawl CLI + MCP integration with Hermes Agent.

## Quick Install

```bash
npx -y firecrawl-cli@latest init --all --browser
```

If browser auth times out, set the API key manually in `.env`:

```bash
# .env (under $HERMES_HOME)
FIRECRAWL_API_KEY=fc-...
```

## API Key Management

| Source | Value | Status |
|--------|-------|--------|
| User-provided session key | `<your-key>` | ❌ Invalid token |
| User-provided second key | `<your-key>` | ✅ Valid |

Keyless free tier (Path F) also works for search, scrape, and interact — no API key needed, but rate-limited.

## MCP Server

```bash
# Add Firecrawl MCP (non-interactive via stdin piping)
hermes mcp add firecrawl --url https://mcp.firecrawl.dev/v2/mcp
# Then pipe: y\n{api_key}\ny\n  (auth yes / key / enable all tools yes)
```

### 26 Tools Available

- **Scraping**: `firecrawl_scrape`, `firecrawl_parse`
- **Search**: `firecrawl_search`, `firecrawl_search_feedback`
- **Crawl**: `firecrawl_crawl`, `firecrawl_check_crawl_status`, `firecrawl_map`
- **Extract**: `firecrawl_extract`
- **Agent**: `firecrawl_agent`, `firecrawl_agent_status`
- **Interact**: `firecrawl_interact`, `firecrawl_interact_stop`
- **Feedback**: `firecrawl_feedback`
- **Monitors** (8): `firecrawl_monitor_create/list/get/update/delete/run/checks/check`
- **Research** (5): `firecrawl_research_search_papers/inspect_paper/related_papers/read_paper/search_github`

## Verification

```bash
mkdir -p .firecrawl
firecrawl --status
firecrawl scrape "https://firecrawl.dev" -o .firecrawl/install-check.md
```

## Keyless Free Tier (Fallback)

When no API key is available, unset `FIRECRAWL_API_KEY` and use:

```bash
unset FIRECRAWL_API_KEY
npx -y firecrawl-cli@latest scrape "https://firecrawl.dev"
```

MCP endpoint: `https://mcp.firecrawl.dev/v2/mcp` (also works keyless)

## Config Shape (config.yaml)

```yaml
mcp_servers:
  firecrawl:
    url: https://mcp.firecrawl.dev/v2/mcp
    headers:
      Authorization: Bearer ${FIRECRAWL_API_KEY}
    enabled: true
```

## Docs

- API reference: https://docs.firecrawl.dev
- Skills repo: https://github.com/firecrawl/skills
- Workflows: https://github.com/firecrawl/firecrawl-workflows
