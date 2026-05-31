# Using FishXCode with OpenClaw

::: info Project Introduction
OpenClaw is an open-source, self-hosted personal AI assistant platform that bridges messaging apps to AI agents running on your own hardware. Designed for developers and power users who want autonomous AI assistants without surrendering data control.

- Official Website: [https://openclaw.ai](https://openclaw.ai)
- Documentation: [https://docs.openclaw.ai](https://docs.openclaw.ai)
- GitHub: [https://github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
:::

## Prerequisites

- OpenClaw installed (see installation section below)
- FishXCode API Key ([Get from Console](https://fishxcode.com/console/token))

## Core Features

### Multi-Channel Integration

- **Full Platform Coverage**: Supports Lark (Feishu), Discord, Slack, Microsoft Teams, and more
- **Single Gateway**: Manage all channels through a single Gateway process
- **Voice Support**: Voice interaction on macOS/iOS/Android
- **Canvas Interface**: Render interactive Canvas interfaces

### Self-Hosted & Data Security

- **Fully Self-Hosted**: Runs on your own machine or server
- **Open Source**: MIT licensed, fully transparent code
- **Local Data Storage**: Context and skills stored on your local computer, not in the cloud

### Intelligent Agent Capabilities

- **Always-On**: Supports background persistent operation with persistent memory
- **Scheduled Tasks**: Supports cron scheduled tasks
- **Session Isolation**: Sessions isolated per agent/workspace/sender
- **Multi-Agent Routing**: Supports multi-agent collaborative work
- **Tool Calling**: Native support for tool calling and code execution

## Installation

::: info Requirements
- FishXCode API Key
- Node.js 22+ required for npm/git methods; the curl one-liner handles dependencies automatically
:::

::: code-group

```bash [curl (Recommended)]
curl -fsSL https://openclaw.ai/install.sh | bash
```

```bash [npm]
npm install -g openclaw@latest
```

```bash [curl (git mode)]
curl -fsSL https://openclaw.ai/install.sh | bash -s -- --install-method git
```

```bash [Manual git clone]
git clone https://github.com/openclaw/openclaw.git
cd openclaw && pnpm install && pnpm run build
pnpm run openclaw onboard
```

:::

After installation, run the onboarding wizard (already included in the manual clone steps above):

```bash
openclaw onboard
```

## Configuration

### Configuration File Location

The OpenClaw configuration file is located at `~/.openclaw/openclaw.json`:

- **macOS**: `/Users/your-username/.openclaw/openclaw.json`
- **Linux**: `/home/your-username/.openclaw/openclaw.json`
- **Windows**: `C:\Users\your-username\.openclaw\openclaw.json`

If the file doesn't exist, create it first:

::: code-group

```bash [macOS / Linux]
mkdir -p ~/.openclaw
touch ~/.openclaw/openclaw.json
```

```powershell [Windows (PowerShell)]
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.openclaw"
New-Item -ItemType File -Force -Path "$env:USERPROFILE\.openclaw\openclaw.json"
```

:::

::: tip
If you've previously run `openclaw onboard`, the configuration file will be auto-generated. You can simply modify the existing content.
:::

### Configuration File Structure

The `openclaw.json` structure consists of two main parts:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      // Configure AI model providers
    }
  },
  "agents": {
    "defaults": {
      // Configure default model, workspace, etc.
    }
  }
}
```

- `models.providers` — Define service providers, including URLs, keys, and model lists
- `models.mode` — Set to `"merge"` to merge custom config with built-in defaults, **strongly recommended**
- `agents.defaults.model.primary` — Default model to use, format: `provider-name/model-id`
- `api` — API protocol type, use `"anthropic-messages"` for Anthropic models, `"openai-responses"` for OpenAI-compatible models

### Configuration Methods

#### Configure Anthropic (Claude) Models

Add the following to `openclaw.json`:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-anthropic": {
        "baseUrl": "https://api.fishxcode.com",
        "apiKey": "sk-your-fishxcode-token",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "claude-opus-4-6",
            "name": "Claude Opus 4.6",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 8192,
            "reasoning": false
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "fishxcode-anthropic/claude-opus-4-6"
      }
    }
  }
}
```

::: warning Important
- Replace `sk-your-fishxcode-token` with your actual Token from the [FishXCode console](https://fishxcode.com/console/token)
- **For Anthropic protocol, `baseUrl` should NOT include `/v1`** — the SDK automatically appends the path
:::

#### Configure OpenAI (GPT) Models

When calling OpenAI models through FishXCode, the `api` field must be set to `openai-responses`:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-openai": {
        "baseUrl": "https://api.fishxcode.com/v1",
        "apiKey": "sk-your-fishxcode-token",
        "api": "openai-responses",
        "models": [
          {
            "id": "gpt-5",
            "name": "GPT-5",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 16384,
            "reasoning": true
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "fishxcode-openai/gpt-5"
      }
    }
  }
}
```

::: tip
**OpenAI protocol requires `/v1`**, i.e., `https://api.fishxcode.com/v1`. This is because the two SDKs have different path concatenation logic.
:::

#### Configure Both Anthropic + OpenAI (Recommended)

Add both providers side-by-side under `models.providers` to use models from both families:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-anthropic": {
        "baseUrl": "https://api.fishxcode.com",
        "apiKey": "sk-your-fishxcode-token",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "claude-opus-4-6",
            "name": "Claude Opus 4.6",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 8192,
            "reasoning": false
          },
          {
            "id": "claude-sonnet-4-5-20250929",
            "name": "Claude Sonnet 4.5",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 8192,
            "reasoning": false
          }
        ]
      },
      "fishxcode-openai": {
        "baseUrl": "https://api.fishxcode.com/v1",
        "apiKey": "sk-your-fishxcode-token",
        "api": "openai-responses",
        "models": [
          {
            "id": "gpt-5",
            "name": "GPT-5",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 16384,
            "reasoning": true
          },
          {
            "id": "gpt-5-codex",
            "name": "GPT-5 Codex",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 16384,
            "reasoning": true
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "fishxcode-anthropic/claude-opus-4-6",
        "fallbacks": [
          "fishxcode-anthropic/claude-sonnet-4-5-20250929",
          "fishxcode-openai/gpt-5"
        ]
      }
    }
  }
}
```

To switch the default model, simply modify the `model.primary` value:

- `"fishxcode-anthropic/claude-opus-4-6"` → Claude Opus 4.6
- `"fishxcode-anthropic/claude-sonnet-4-5-20250929"` → Claude Sonnet 4.5
- `"fishxcode-openai/gpt-5"` → GPT-5
- `"fishxcode-openai/gpt-5-codex"` → GPT-5 Codex

### Key Configuration Fields

| Field | Meaning | Anthropic (Claude) | OpenAI (GPT) |
| --- | --- | --- | --- |
| `baseUrl` | API proxy address | `https://api.fishxcode.com` | `https://api.fishxcode.com/v1` |
| `apiKey` | Your API Key | `sk-your-fishxcode-token` | `sk-your-fishxcode-token` |
| `api` | API protocol type | `anthropic-messages` | `openai-responses` |
| `mode` | Config merge mode | `merge` (recommended) | `merge` (recommended) |
| `models[].id` | Model ID | `claude-opus-4-6` | `gpt-5` |
| `model.primary` | Default model | `fishxcode-anthropic/claude-opus-4-6` | `fishxcode-openai/gpt-5` |
| `reasoning` | Enable reasoning mode | `false` (depends on model) | `true` (GPT-5.x supports) |

## Verify Configuration

Run the following command to confirm the configuration is working:

```bash
openclaw
```

If configured correctly, OpenClaw will start normally and route requests through FishXCode.

You can also check the model list:

```bash
openclaw models list
```

Check current model and authentication status:

```bash
openclaw models status
```

Run a comprehensive diagnostic:

```bash
openclaw doctor
```

## Start the Service

After configuration is complete, start OpenClaw:

```bash
openclaw start
```

Once started, you can interact with the AI assistant through the configured channels.

Restart the Gateway:

```bash
openclaw gateway restart
```

## Troubleshooting

### 403 Blocked

**Symptom**: Provider is configured, direct curl request to the API returns 200, but requests from OpenClaw get a 403 "Your request was blocked".

**Cause**: OpenClaw uses `@anthropic-ai/sdk` under the hood, which sends requests with the official SDK User-Agent (e.g., `Anthropic/JS 0.73.0`). Some CDNs or WAFs block this UA.

**Fix**: Add a `headers` field to the provider config to override the UA:

```json
{
  "fishxcode-anthropic": {
    "baseUrl": "https://api.fishxcode.com",
    "apiKey": "your-api-key",
    "api": "anthropic-messages",
    "headers": {
      "User-Agent": "Mozilla/5.0"
    },
    "models": [...]
  }
}
```

### Don't Include /v1 in baseUrl (Anthropic Protocol)

**Symptom**: Request returns 404, and logs show the request path has become `/v1/v1/messages`.

**Cause**: The Anthropic SDK automatically appends `/v1/messages` to the baseURL. If your baseUrl already includes `/v1`, the actual request path becomes duplicated.

**Fix**: For Anthropic protocol, only write the domain in baseUrl, without `/v1`:

```json
{
  "baseUrl": "https://api.fishxcode.com"
}
```

::: tip
OpenAI protocol requires `/v1`, i.e., `https://api.fishxcode.com/v1`. This is because the two SDKs have different path concatenation logic.
:::

### The api Field Only Accepts Three Values

**Symptom**: Startup shows "Config invalid", or the configured provider doesn't appear in the model list.

**Cause**: OpenClaw strictly validates the `api` field, accepting only these three values:

| Value | Protocol |
| --- | --- |
| `anthropic-messages` | Anthropic Messages API |
| `openai-completions` | OpenAI Chat Completions |
| `openai-responses` | OpenAI Responses API |

Values like `openai-chat`, `openai`, `anthropic`, etc. will cause errors.

**Fix**: When using FishXCode, use `anthropic-messages` for Claude models and `openai-responses` for GPT models.

### Empty Response with openai-completions (Don't Use for GPT Models)

**Symptom**: `api` is set to `openai-completions`, request succeeds (log shows `isError=false`), but the UI displays an empty message.

**Cause**: OpenClaw internally handles message streams in Anthropic format. OpenAI-format responses from `openai-completions` may not map correctly in some cases.

**Fix**: When calling GPT models via FishXCode, use `openai-responses` instead of `openai-completions`.

### Configuration Changes Not Taking Effect

**Symptom**: Modified `openclaw.json` but OpenClaw still uses the old configuration.

**Cause**: OpenClaw has two places where provider configs need to be in sync:

```
~/.openclaw/openclaw.json              → models.providers
~/.openclaw/agents/main/agent/models.json → providers
```

Changing only one may cause the configuration to not take effect.

**Fix**: After modifying, confirm with:

```bash
openclaw models status
```

Or restart the OpenClaw Gateway:

```bash
openclaw gateway restart
```

### JSON Format Errors

Common JSON format errors:

- **Trailing comma**: The last element cannot have a trailing comma
- **Missing comma**: Two adjacent key-value pairs must be separated by a comma
- **Quote issues**: JSON only accepts English double quotes `"`, not Chinese quotes or single quotes
- **Mismatched brackets**: Every `{` must have a matching `}`, every `[` must have a matching `]`

Validate the format with:

```bash
python3 -m json.tool ~/.openclaw/openclaw.json
```

### What is the reasoning Field?

`reasoning` controls whether to enable the model's reasoning/thinking mode. When set to `true`, the model performs internal reasoning before outputting a response, but consumes more tokens.

GPT-5.x Codex series generally supports `reasoning`. For Claude models, it depends on the version. If unsure, set to `false`.

### What's the Difference Between openai-completions and openai-responses?

- **openai-responses** — Corresponds to OpenAI Responses API (`/v1/responses`). **Use this when accessing GPT models via FishXCode.**
- **openai-completions** — Corresponds to OpenAI Chat Completions API (`/v1/chat/completions`), a universal compatibility format, but may cause empty responses with FishXCode's GPT models. **Not recommended.**

### Diagnostic Command Reference

| Command | Purpose |
| --- | --- |
| `openclaw models status` | View current model and authentication status |
| `openclaw models list` | View configured model list |
| `openclaw doctor` | Comprehensive diagnostic |
| `openclaw gateway restart` | Restart Gateway |

::: tip Debugging Strategy
First use curl to confirm the FishXCode API itself is working normally, then check what's different in the requests sent by OpenClaw (UA, path, format).
:::
