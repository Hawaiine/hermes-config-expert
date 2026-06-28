# Hyper (Charm) Custom Provider

Single-key custom provider proxying models through `https://hyper.charm.land/v1` (Charm Hyper).

## Config Shape

```yaml
custom_providers:
  - name: hyper
    base_url: https://hyper.charm.land/v1
    api_mode: chat_completions
    models:
      - id: deepseek-v4-flash
        name: deepseek-v4-flash
      - id: deepseek-v4-pro
        name: deepseek-v4-pro
      # ... 18 more models (see below)
```

## .env

```bash
HYPER_API_KEY=*** ````

## Available Models (20)

| Model | Speed (TTFT) | Notes |
|-------|-------------|-------|
| qwen3.7-plus | 0.55s 🏆 | Fastest overall |
| glm-5 | 0.57s | |
| glm-5.1 | 0.57s | |
| qwen3.6-flash | 0.57s | |
| kimi-k2.5 | 0.58s | |
| kimi-k2.7-code | 0.58s | |
| qwen3.6-plus | 0.60s | |
| deepseek-v4-pro | 0.60s | |
| qwen3.6-max | 0.71s | |
| qwen3.7-max | 0.75s | |
| deepseek-v4-flash | 0.76s | |
| glm-5.2 | 0.76s | |
| qwen3-coder-480b | 0.73s | |
| ... (all under 1s) | | |

Full list: `deepseek-v4-flash`, `deepseek-v4-pro`, `gemma-4-26b-a4b-it`, `glm-5`, `glm-5.1`, `glm-5.2`, `gpt-oss-120b`, `kimi-k2.5`, `kimi-k2.6`, `kimi-k2.7-code`, `llama-3.3-70b-instruct`, `llama-4-maverick-17b-128e-instruct-fp8`, `minimax-m2.7`, `qwen3.6-flash`, `qwen3.6-max`, `qwen3.6-plus`, `qwen3.7-max`, `qwen3.7-plus`, `qwen3-coder-480b-a35b-instruct-int4-mixed-ar`, `qwen3-next-80b-a3b-instruct`

## Verified

- `GET /v1/models` with `Authorization: Bearer` returns 20 models
- All models respond <1s TTFT
- Standard OpenAI-compatible chat completions endpoint