# Honcho Memory Provider Setup

Honcho (https://honcho.dev/) is an AI-native memory backend that adds dialectic reasoning and deep user modeling as a Hermes memory provider.

Docs: https://hermes-agent.nousresearch.com/docs/user-guide/features/memory → Honcho Memory

## Overview

| Capability | Built-in Memory | Honcho |
|---|---|---|
| Cross-session persistence | File-based MEMORY.md/USER.md | Server-side with API |
| User profile | Manual agent curation | Automatic dialectic reasoning |
| Session summary | — | Session-scoped context injection |
| Multi-agent isolation | — | Per-peer profile separation |
| Semantic search | FTS5 session search | Semantic search over conclusions |

## Installation

### Standard (writable Hermes venv)

```bash
pip install honcho-ai
# or
uv pip install honcho-ai
```

### Read-only Hermes venv (workaround)

When `/opt/hermes/.venv` is root-owned and read-only (common in containerized installs):

```bash
# 1. Create a separate writable venv
python3 -m venv /path/to/honcho-venv

# 2. Install honcho-ai into it
/path/to/honcho-venv/bin/pip install honcho-ai
# or
uv pip install honcho-ai --python /path/to/honcho-venv

# 3. Create a .pth file so Python discovers it automatically
mkdir -p ~/.local/lib/python3.XX/site-packages
echo '/path/to/honcho-venv/lib/python3.XX/site-packages' \
  > ~/.local/lib/python3.XX/site-packages/honcho-venv.pth
```

Replace `python3.XX` with the actual Python version (`python3 -c "import sys; print(f'{sys.version_info.major}.{sys.version_info.minor}')"`).

## Configuration

### 1. config.yaml

```yaml
memory:
  memory_enabled: true
  user_profile_enabled: true
  provider: honcho           # was ''
```

CLI equivalent: `hermes memory setup` (interactive, select "honcho")

### 2. .env

```
HONCHO_API_KEY=hch-v3-<your-key>
```

### 3. honcho.json (optional, profile-local at $HERMES_HOME/honcho.json)

```json
{
  "contextTokens": 1200,
  "contextCadence": 1,
  "dialecticCadence": 3,
  "dialecticDepth": 1,
  "dialecticReasoningLevel": "low",
  "recallMode": "hybrid",
  "writeFrequency": "async",
  "saveMessages": true,
  "observationMode": "directional",
  "sessionStrategy": "per-session"
}
```

Also accepted at `~/.honcho/config.json` (global, shared with Claude Code / Cursor).

## Key Config Knobs

| Knob | Default | Description |
|---|---|---|
| `recallMode` | `hybrid` | `hybrid` (auto-inject + tools), `context` (inject only), `tools` (tools only) |
| `contextCadence` | 1 | Turns between context() API calls (base layer refresh) |
| `dialecticCadence` | 2 | Turns between peer.chat() LLM calls. Recommend 1–5 |
| `dialecticDepth` | 1 | Multi-pass depth per invocation (1–3) |
| `dialecticReasoningLevel` | `low` | Base reasoning level: minimal/low/medium/high/max |
| `sessionStrategy` | `per-directory` | `per-session`, `per-directory`, `per-repo`, `global` |
| `observationMode` | `directional` | `directional` (full mutual) or `unified` (shared pool) |
| `writeFrequency` | `async` | `async` (bg thread), `turn` (sync), `session` (batch on end) |

## Available Tools (post-activation)

- `honcho_profile` — Read/update peer card
- `honcho_search` — Semantic search over context
- `honcho_context` — Full session context (summary, representation, card)
- `honcho_reasoning` — Synthesized answer from Honcho's LLM
- `honcho_conclude` — Create/delete conclusions (PII removal)

## CLI Commands (post-activation)

```
hermes memory setup honcho    # Configure Honcho directly
hermes honcho status          # Connection status
hermes honcho strategy        # Show/set session strategy
hermes honcho peer            # Show/update peer names
hermes honcho mode            # Show/set recall mode
hermes honcho tokens          # Token budget for context/dialectic
hermes honcho identity        # Seed/show AI peer identity
hermes honcho enable|disable  # Toggle for active profile
hermes honcho sync            # Sync to all profiles
```

## Verification

```bash
python3 -c "import honcho; print(honcho.__version__)"
hermes doctor                 # Check config
grep 'provider:' config.yaml  # Confirm memory.provider: honcho
```

After changing memory provider, restart Hermes (gateway: `/restart`, CLI: exit + relaunch).

## Pitfalls

- **Read-only Hermes venv**: `/opt/hermes/.venv/` is often root-owned. `uv pip install` hangs silently with permission errors. Always install optional deps in a separate venv + `.pth` fallback.
- **HONCHO_API_KEY redacted**: The secret redactor hides the key in tool output. Verify via `wc -c /opt/data/.env` or line length matching, not by reading the value back.
- **Restart required**: `memory.provider` is read at session init. Changing it without restart has no effect.
- **`.env` file is read-only to patch/write_file tools**: use `echo >> .env` via terminal (with approval) or Python's `open()` to write.
