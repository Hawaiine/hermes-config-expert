# Discord Home Channel 配置参考

## 概述

Home channel 是 Hermes 投递 cron 任务结果和跨平台消息的默认频道。

## 核心规则

- Home channel 必须是**具体文字频道**，不能是分类（Category）
- 分类本身不能收发消息，无法作为投递目标
- 在任意有机器人的 Discord 频道中发送 `/sethome` 即可将该频道设为主频道

## 配置方法

### 方法 A：`/sethome` 指令（推荐）

在目标频道内发送：

```
/sethome
```

Hermes 会自动识别当前频道 ID 并写入配置。

> **注意**：Issue #6447 提到 `/sethome` 可能将 channel ID 错误写入 `config.yaml` 而非 `.env`。如遇此问题，改用方法 B。

### 方法 B：手动配置 `.env`

在 `~/.hermes/.env` 或当前 profile 的 `.env` 中添加：

```ini
DISCORD_HOME_CHANNEL=1520795989676523703
DISCORD_HOME_CHANNEL_NAME="#随便聊聊"
```

- `DISCORD_HOME_CHANNEL`：频道 ID（数字）
- `DISCORD_HOME_CHANNEL_NAME`：显示名称（带 `#` 前缀）

## 频道 ID 获取方式

1. 开启 Discord 开发者模式：设置 → 高级 → 开发者模式
2. 右键点击目标频道 → 复制 ID

## 验证

```bash
# 查看当前生效的配置
grep DISCORD_HOME_CHANNEL /opt/data/.env

# 查看 gateway 日志确认生效
tail -n 30 /opt/data/logs/gateway.log | grep 'home channel'
```

预期日志输出：

```
Sent shutdown notification to home channel discord:<CHANNEL_ID>
```

## 多 Gateway 隔离

每个 profile 的 `.env` 独立管理各自的 `DISCORD_HOME_CHANNEL`。  
多 service 部署时，修改某 service 的配置后只需 `docker compose restart <service-name>` 即可。
