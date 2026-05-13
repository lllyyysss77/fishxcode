# Using FishXCode with WorkBuddy

> First time using FishXCode? Complete [Account Setup](/en/account) first, then get your API Key from the [FishXCode console](https://fishxcode.com/console/token).

## 1. Open the WorkBuddy Configuration File

WorkBuddy supports custom models through a local `models.json` file. The configuration file is located at:

- macOS / Linux: `~/.workbuddy/models.json`
- Windows: `C:\Users\<用户名>\.workbuddy\models.json`

Create the directory or file first if it does not exist.

## 2. Configure FishXCode Third-Party Models

WorkBuddy uses the OpenAI Chat Completions compatible protocol for third-party model configuration. Here, `vendor: "OpenAI"` means the OpenAI Chat Completions compatible protocol. It does not mean only OpenAI models can be used; Claude models are also called through the FishXCode OpenAI compatible endpoint.

Write the following content to `~/.workbuddy/models.json`:

```json
{
  "models": [
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "url": "https://fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "url": "https://fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    }
  ],
  "availableModels": [
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

::: warning Important
- Replace `sk-你的FishXCode-Token` with the actual API Key you created in the FishXCode console.
- `availableModels` must include the model IDs from `models[].id`; otherwise, WorkBuddy may not show them in the dropdown list.
- `url` must be the full Chat Completions endpoint: `https://fishxcode.com/v1/chat/completions`.
:::

## 3. Keep Tencent Token Plan Alongside FishXCode

If you also need to keep Tencent's official Token Plan, place the official models and FishXCode models in the same `models.json` file:

```json
{
  "models": [
    {
      "id": "tc-code-latest",
      "name": "Auto",
      "vendor": "OpenAI",
      "url": "https://api.lkeap.cloud.tencent.com/plan/v3/chat/completions",
      "apiKey": "your-token-plan-api-key",
      "maxInputTokens": 100000,
      "maxOutputTokens": 4096
    },
    {
      "id": "hunyuan-2.0-instruct",
      "name": "Tencent HY 2.0 Instruct",
      "vendor": "OpenAI",
      "url": "https://api.lkeap.cloud.tencent.com/plan/v3/chat/completions",
      "apiKey": "your-token-plan-api-key",
      "maxInputTokens": 100000,
      "maxOutputTokens": 4096
    },
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "url": "https://fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "url": "https://fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    }
  ],
  "availableModels": [
    "tc-code-latest",
    "hunyuan-2.0-instruct",
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

## 4. Restart and Verify

1. Save `models.json`
2. Fully quit WorkBuddy
3. Restart WorkBuddy
4. Open the model selector
5. Select `gpt-5.5` or `claude-opus-4-7`
6. Send a simple question to verify the response

## FAQ

### New models are not visible

First check whether `availableModels` includes the corresponding model ID, then check whether `models.json` is valid JSON.

### Calls fail or show red errors

Check that `url` is the full endpoint `https://fishxcode.com/v1/chat/completions`, not just `https://fishxcode.com/v1`.

### Authentication fails

Copy the API Key from the console again and make sure there are no extra spaces or line breaks.
