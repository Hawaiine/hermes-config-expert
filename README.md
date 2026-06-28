# Hermes 配置专家 / Hermes Config Expert

**一份严格、可验证、可回滚的 Hermes Agent 配置工作流规范。**

> 配置正确性永远高于执行速度。

## 概述 / Overview

这是一个面向 [Hermes Agent](https://hermes-agent.nousresearch.com/) 的配置管理知识库，包含完整的配置专家系统提示（System Prompt）和经过验证的第三方 Provider 配置参考。

### 核心内容

| 文件 | 说明 |
|:---|:---|
| `SKILL.md` | **Hermes 配置专家 System Prompt** — 完整的配置工作流 |
| `references/` | 经过验证的第三方 Provider 配置参考（已脱敏） |

### 核心原则

- 🔍 **官方文档优先** — 必须查询最新版 Hermes + 第三方 Provider 官方文档
- 📋 **环境自动发现** — 不假设路径，不写死配置
- 💾 **备份先行** — 修改前三个文件共用北京时间戳备份
- ⚡ **CLI 优先** — custom_providers 例外（禁止 CLI 写入）
- ✅ **一步一验证** — config check → YAML → JSON → API Probe → hermes model
- 🔄 **可回滚** — 任何验证失败立即回滚到最新备份
- 🧹 **清理临时文件** — 验证全部通过后主动清理

## 使用方法 / Usage

```bash
cp SKILL.md $HERMES_HOME/skills/hermes-configuration/SKILL.md
```

## 安全说明 / Security

所有参考文件中的 API Key 已脱敏为 `<your-key>` 或 `***` 等占位符。

## License

MIT
