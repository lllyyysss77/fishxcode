# Using FishXCode with Droid CLI

## Install Droid CLI

::: code-group

```bash [macOS/Linux]
curl -fsSL https://app.factory.ai/cli | sh
```

```powershell [Windows]
irm https://app.factory.ai/cli/windows | iex
```

:::

## Configure Model

Edit `~/.factory/config.json` with the following content:

```json
{
  "custom_models": [
    {
      "model_display_name": "fishxcode-gpt5",
      "model": "gpt-5",
      "base_url": "https://api.fishxcode.com/v1",
      "api_key": "YOUR_FISHXCODE_KEY",
      "provider": "generic-chat-completion-api",
      "max_tokens": 1280000
    }
  ]
}
```

::: warning Important
Replace `YOUR_FISHXCODE_KEY` with your FishXCode API Key.
:::

## Launch Directly

After configuration, type `droid` in your terminal, enter `/model`, and select `fishxcode-gpt5` from Custom Model to start using it.
