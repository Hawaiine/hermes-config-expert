# Sensenova 自定义 Provider — 多 Key 凭证池示例

> 对应的 Hermes 配置模式：**自定义 Provider + 凭证池 + 轮询策略**

## 模式说明

- Provider 类型：`custom_providers`（需手动编辑 config.yaml）
- 凭证类型：**多 Key 凭证池**（multi-key credential pool），使用 `round_robin` 轮询策略
- API Key 存储：auth.json 的 `custom:<name>` 下
- 适用场景：同一 Provider 使用多个 API Key 分摊请求量和限流

---

# Sensenova Custom Provider Reference

Session-specific config details for the sensenova custom endpoint setup.

## Config Shape

```yaml
model:
  default: deepseek-v4-flash
  provider: custom:sensenova
  base_url: ''

custom_providers:
  - name: sensenova
    base_url: https://token.sensenova.cn/v1
    api_mode: chat_completions
    models:
      - id: deepseek-v4-flash
        name: deepseek-v4-flash
      - id: sensenova-6.7-flash-lite
        name: sensenova-6.7-flash-lite

credential_pool_strategies:
  custom:sensenova: round_robin
```

## Auth.json Pool Key

```
credential_pool["custom:sensenova"] = [
  { "auth_type": "api_key", "source": "manual", ... },
  ...
]
```

## CLI Commands Used

```bash
hermes auth add custom:sensenova --type api-key --api-key sk-...
hermes auth list custom:sensenova
hermes config set credential_pool_strategies '{}'
```

## Verified Model IDs

- `deepseek-v4-flash` — returns 200, normal chat completion response
- `sensenova-6.7-flash-lite` — listed in config, not yet probed

## Base URL

All requests go to: `https://token.sensenova.cn/v1`

Docs: https://platform.sensenova.cn/docs
