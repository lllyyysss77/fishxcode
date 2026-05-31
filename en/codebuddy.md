# Using FishXCode with CodeBuddy

> First time using FishXCode? Complete [Account Setup](/en/account) first, then get your API Key from the [FishXCode console](https://fishxcode.com/console/token).

## 1. Install and Start CodeBuddy Code

This tutorial configures Tencent Cloud **CodeBuddy Code**, the command-line tool. Make sure Node.js 18.0 or later is installed locally, then install it:

```bash
npm install -g @tencent-ai/codebuddy-code
```

Check the version:

```bash
codebuddy --version
```

Start CodeBuddy Code:

```bash
codebuddy
```

On first launch, follow the prompts to complete login. Later, you can enter `/model` in the command line to check whether custom models were loaded successfully.

## 2. Open the CodeBuddy Configuration File

CodeBuddy Code supports custom models through a local `models.json` file. The configuration file is located at:

- macOS / Linux: `~/.codebuddy/models.json`
- Windows: `C:\Users\<用户名>\.codebuddy\models.json`

Create the directory or file first if it does not exist.

## 3. Write the FishXCode Model Configuration

CodeBuddy Code uses the OpenAI Chat Completions compatible protocol for third-party LLM configuration. Here, `vendor: "OpenAI"` means protocol compatibility. It does not mean only OpenAI models can be used; Claude models are also called through the FishXCode OpenAI compatible endpoint.

Write the following content to `~/.codebuddy/models.json`:

```json
{
  "models": [
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
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
- `availableModels` must include the model IDs from `models[].id`; otherwise, the new models may not appear in `/model`.
- Third-party relay services require the full Chat Completions endpoint: `https://api.fishxcode.com/v1/chat/completions`.
:::

## 4. Keep Tencent Coding Plan Alongside FishXCode

If you also need to keep Tencent's official Coding Plan, append the FishXCode models in the same `models.json` file:

```json
{
  "models": [
    {
      "id": "tc-code-latest",
      "name": "Auto",
      "vendor": "Tencent Cloud",
      "apiKey": "YOUR_TENCENT_CODING_PLAN_KEY",
      "url": "https://api.lkeap.cloud.tencent.com/coding/v3"
    },
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    }
  ],
  "availableModels": [
    "tc-code-latest",
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

## 5. Restart and Select a Model

1. Save `~/.codebuddy/models.json`
2. Fully quit any running CodeBuddy Code process
3. Run `codebuddy` again
4. Enter `/model`
5. Select `gpt-5.5` or `claude-opus-4-7`
6. Send a test prompt, such as `解释当前项目结构`

## FAQ

### `/model` does not show the models

Check `availableModels` first. The model ID must appear in both `models[].id` and `availableModels`.

### The model appears but calls fail

Check `url` first. FishXCode third-party model configuration should use the full endpoint `https://api.fishxcode.com/v1/chat/completions`.

### Authentication fails

Copy the API Key from the console again and make sure there are no extra spaces or line breaks.
