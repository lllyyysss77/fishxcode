# Using FishXCode with Claude Desktop

## Scope

This guide is for the new **Cowork / Code third-party inference** mode in Claude Desktop, which Anthropic documents as `third-party inference`.

Use this setup if you want to:

- use **Cowork** in Claude Desktop
- use **Code** in Claude Desktop
- route requests through FishXCode via a custom API or gateway

::: warning Note
This is not the old "just sign in and chat" flow. With third-party inference enabled, you mainly use the **Cowork** and **Code** tabs.
:::

## Prerequisites

Prepare the following first:

1. The latest Claude Desktop
2. A FishXCode token
3. A token that can access Claude models

Get your token here:
- [https://fishxcode.com/console/token](https://fishxcode.com/console/token)

Recommended starter models:

- `claude-sonnet-4-5-20250929`
- `claude-opus-4-5-20251101`
- `claude-haiku-4-5-20251001`

## Method 1: Configure Directly in Claude Desktop (Recommended)

Anthropic recommends using the built-in third-party inference setup flow in the desktop app.

### 1. Enable Developer mode

In Claude Desktop, go to:

```text
Help → Troubleshooting → Enable Developer mode
```

After enabling it, you should see `Developer` menu entries.

### 2. Open third-party inference setup

Then go to:

```text
Developer → Configure third-party inference
```

This opens Anthropic's official third-party inference configuration flow.

### 3. Choose Gateway mode

Select:

```text
Gateway
```

FishXCode maps to the `gateway` mode in this flow.

### 4. Fill in the FishXCode settings

Use the following values:

- Gateway URL: `https://fishxcode.com`
- Authentication: `x-api-key`
- API Key: your FishXCode token

If the UI asks for models, start with:

```text
claude-sonnet-4-5-20250929
claude-opus-4-5-20251101
claude-haiku-4-5-20251001
```

If there is an option to enable `Code`, keep it enabled.

### 5. Apply locally

When finished, choose:

```text
Apply locally
```

This writes the configuration into your local Claude Desktop user config.

After that, restart Claude Desktop. In most cases you should then see the **Cowork / Code** workspace.

## Method 2: Manage It via Local Config File (Advanced)

If you want to back up, migrate, or troubleshoot the setup, inspect Claude Desktop's local config file.

Common locations:

### macOS

```text
~/Library/Application Support/Claude-3p/claude_desktop_config.json
```

### Windows

```text
%APPDATA%\Claude-3p\claude_desktop_config.json
```

### Linux

```text
~/.config/Claude-3p/claude_desktop_config.json
```

::: tip Tip
The local file stores the third-party inference data under `enterpriseConfig`. The safer path is to configure it in the app first, then back up the generated config rather than hand-writing JSON from scratch.
:::

## Recommended FishXCode Values

If you want to verify the key fields manually, focus on these:

| Field | Recommended Value |
| --- | --- |
| `inferenceProvider` | `gateway` |
| `inferenceGatewayBaseUrl` | `https://fishxcode.com` |
| `inferenceGatewayAuthScheme` | `x-api-key` |
| `inferenceGatewayApiKey` | your FishXCode token |
| `inferenceModels` | list of Claude models |
| `isClaudeCodeForDesktopEnabled` | `true` |

Recommended model list:

```text
claude-sonnet-4-5-20250929
claude-opus-4-5-20251101
claude-haiku-4-5-20251001
```

## Usage

Once configured, you can use Claude Desktop to:

- open **Cowork** for general tasks, docs, and file collaboration
- open **Code** for coding, terminal workflows, and engineering tasks

If the model picker is visible, start with:

- daily use: `claude-sonnet-4-5-20250929`
- deep reasoning: `claude-opus-4-5-20251101`
- fast lightweight tasks: `claude-haiku-4-5-20251001`

## FAQ

### Why can't I find `Enable Developer mode`?

Check these first:

1. You are on a recent Claude Desktop build
2. You are looking at the real system menu bar, not just the window controls

If it still does not appear after upgrading, fully quit Claude Desktop and reopen it.

### Why can't I see Cowork / Code after configuring it?

Check in this order:

1. Developer mode is enabled
2. `Configure third-party inference` was completed
3. You selected `Gateway`
4. `gatewayUrl` is `https://fishxcode.com`
5. The token is valid and can access Claude models
6. Claude Desktop was fully restarted after setup

### Why is the Gateway URL not `https://fishxcode.com/v1`?

Because Claude Desktop third-party inference uses Anthropic-style gateway configuration, not the OpenAI-compatible `/v1/chat/completions` path. Use the gateway root URL instead:

```text
https://fishxcode.com
```

### What if authentication fails after setup?

Check these first:

1. Authentication is set to `x-api-key`
2. The API key field contains your FishXCode token directly
3. The token is still valid and has the required permissions

### Can I edit the local JSON manually?

Yes, but it is not the best choice for the initial setup. Claude Desktop evolves its third-party inference fields over time, and the in-app flow is more stable and easier to recover from if something breaks.
