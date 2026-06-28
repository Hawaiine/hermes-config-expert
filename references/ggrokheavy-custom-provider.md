# ggrokheavy Custom Provider Reference

Single-key custom provider proxying xAI/Grok models via ggrokheavy.xyz.

## Config Shape

```yaml
custom_providers:
  - name: ggrokheavy
    base_url: https://ggrokheavy.xyz/v1
    api_mode: chat_completions
    models:
      - id: grok-4.20-0309-reasoning-console
        name: grok-4.20-0309-reasoning-console
      - id: grok-4.20-multi-agent-low
        name: grok-4.20-multi-agent-low
      - id: grok-4.20-fast-search
        name: grok-4.20-fast-search
      - id: grok-4.20-0309-non-reasoning-search
        name: grok-4.20-0309-non-reasoning-search
      - id: grok-4.20-multi-agent-xhigh
        name: grok-4.20-multi-agent-xhigh
      - id: grok-4.3-fast
        name: grok-4.3-fast
      - id: grok-4.20-multi-agent-medium
        name: grok-4.20-multi-agent-medium
      - id: grok-4.20-0309-non-reasoning-console
        name: grok-4.20-0309-non-reasoning-console
      - id: grok-4.20-multi-agent-high
        name: grok-4.20-multi-agent-high
```

## .env

```bash
GGROKHEAVY_API_KEY=<your-key>
```

Path: `$HERMES_HOME/.env`

## Env Var Convention

Auto-derived as `{NAME}_API_KEY` = `GGROKHEAVY_API_KEY`. Single-key provider — no `credential_pool_strategies` needed.

## Verified Model IDs (from /v1/models)

All 9 models verified via `GET /v1/models`:

| Model ID | Type |
|----------|------|
| `grok-4.20-0309-reasoning-console` | Reasoning (full) |
| `grok-4.20-0309-reasoning-search` | Reasoning + search |
| `grok-4.20-0309-non-reasoning-console` | Non-reasoning (full) |
| `grok-4.20-0309-non-reasoning-search` | Non-reasoning + search |
| `grok-4.20-multi-agent-xhigh` | Multi-agent (extra high) |
| `grok-4.20-multi-agent-high` | Multi-agent (high) |
| `grok-4.20-multi-agent-medium` | Multi-agent (medium) |
| `grok-4.20-multi-agent-low` | Multi-agent (low) |
| `grok-4.3-fast` | Fast lightweight |
| `grok-4.20-fast-search` | Fast + search |

## Verification

```bash
# List models
curl -s -L https://ggrokheavy.xyz/v1/models \
  -H "Authorization: Bearer $GGROKHEAVY_API_KEY" | python3 -m json.tool
```

The endpoint issues a 301 redirect (missing trailing slash). The `-L` flag is required for curl — without it the request fails silently. Python's `urllib.request` does **not** follow the redirect automatically; use `subprocess.run` with `curl -L` or handle the redirect manually.

## Activation

```bash
hermes config set model.provider custom:ggrokheavy
hermes config set model.default grok-4.20-0309-reasoning-console
```

Then restart gateway.

## Base URL

`https://ggrokheavy.xyz/v1` — OpenAI-compatible `/v1/chat/completions` endpoint. The path requires a trailing slash in the actual request (`/v1/models` redirects to `/v1/models/` via 301).

## Notes

- This is a third-party proxy for xAI Grok models, not an official xAI endpoint.
- Model IDs are case-sensitive as listed.
- The endpoint supports the standard OpenAI chat completions format.
