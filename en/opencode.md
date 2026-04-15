# Using FishXCode with OpenCode

## Install OpenCode

::: code-group

```bash [npm]
npm install -g opencode-ai@latest
```

```bash [brew]
brew install anomalyco/tap/opencode
```

```bash [scoop]
scoop install opencode
```

:::

## Configure FishXCode

1. Get your API Key from [https://fishxcode.com/console/token](https://fishxcode.com/console/token)
2. Set the environment variable:

::: code-group

```bash [Linux/macOS]
export FISHXCODE_TOKEN=sk-xxx
```

```powershell [Windows PowerShell]
$env:FISHXCODE_TOKEN="sk-xxx"
```

:::

3. Create `opencode.json` in your project root or `~/.config/opencode/`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "fishxcode-anthropic": {
      "npm": "@ai-sdk/anthropic",
      "name": "fishxcode-anthropic",
      "options": {
        "baseURL": "https://fishxcode.com/v1",
        "apiKey": "{env:FISHXCODE_TOKEN}"
      },
      "models": {
        "claude-sonnet-4-6": {
          "name": "claude-sonnet-4-6"
        }
      }
    },
    "fishxcode-openai": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "fishxcode-openai",
      "options": {
        "baseURL": "https://fishxcode.com/v1",
        "apiKey": "{env:FISHXCODE_TOKEN}"
      },
      "models": {
        "gpt-5.2-codex": {
          "name": "gpt-5.2-codex"
        }
      }
    }
  }
}
```

::: warning Important
Replace `sk-xxx` with your actual Token from the [FishXCode console](https://fishxcode.com/console/token).
:::

## Launch

```bash
cd my-project
opencode
```

Once launched, select a model under the FishXCode provider to start coding.
