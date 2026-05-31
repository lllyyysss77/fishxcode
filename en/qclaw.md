# Using FishXCode with QClaw

> First time using FishXCode? Complete [Account Setup](/en/account) first, then get your API Key from the [FishXCode console](https://fishxcode.com/console/token).

## 1. Confirm the Integration Parameters

When QClaw connects to a third-party LLM API, use the OpenAI Compatible protocol first. Even when calling Claude models, fill in the OpenAI compatible endpoint parameters.

| Configuration Item | Value |
| --- | --- |
| Protocol | `OpenAI Compatible` |
| Base URL | `https://api.fishxcode.com/v1` |
| API Key | API Key created in the FishXCode console |
| OpenAI Model | `gpt-5.5` |
| Claude Model | `claude-opus-4-7` |

If QClaw requires a full Endpoint, use:

```text
https://api.fishxcode.com/v1/chat/completions
```

## 2. Configure Through the Settings UI

If your QClaw version provides a graphical interface, fill it in as follows:

```text
Provider: OpenAI Compatible
Base URL: https://api.fishxcode.com/v1
API Key: sk-你的FishXCode-Token
Model: claude-opus-4-7
```

To use a GPT model, change `Model` to:

```text
gpt-5.5
```

## 3. Configure Through config.yaml

If your QClaw version uses `~/.qclaw/config.yaml`, use the openai-compatible `config.yaml` scheme below:

```yaml
llm:
  provider: openai-compatible
  base_url: https://api.fishxcode.com/v1
  api_key: sk-你的FishXCode-Token
  model: claude-opus-4-7
  max_tokens: 8192
```

When switching to an OpenAI model, only replace `model`:

```yaml
llm:
  provider: openai-compatible
  base_url: https://api.fishxcode.com/v1
  api_key: sk-你的FishXCode-Token
  model: gpt-5.5
  max_tokens: 8192
```

If the client requires a full endpoint URL, change it to:

```yaml
llm:
  provider: openai-compatible
  url: https://api.fishxcode.com/v1/chat/completions
  api_key: sk-你的FishXCode-Token
  model: claude-opus-4-7
  max_tokens: 8192
```

::: warning Important
Different QClaw versions may use different field names, such as `base_url`, `baseURL`, `apiBase`, or `url`. If the configuration does not take effect after saving, check the field names and the configuration file path that QClaw actually reads.
:::

## 4. Configure Through Environment Variables

If QClaw supports environment variables, you can also use:

::: code-group

```bash [Linux/macOS]
export OPENAI_BASE_URL="https://api.fishxcode.com/v1"
export OPENAI_API_KEY="sk-你的FishXCode-Token"
export QCLAW_MODEL="claude-opus-4-7"
qclaw
```

```powershell [Windows PowerShell]
$env:OPENAI_BASE_URL="https://api.fishxcode.com/v1"
$env:OPENAI_API_KEY="sk-你的FishXCode-Token"
$env:QCLAW_MODEL="claude-opus-4-7"
qclaw
```

:::

When you need to use a GPT model, change `QCLAW_MODEL` to `gpt-5.5`.

## 5. Verify the Configuration

First verify that the API Key works with the OpenAI compatible endpoint:

::: code-group

```bash [Claude 模型]
curl https://api.fishxcode.com/v1/chat/completions \
  -H "content-type: application/json" \
  -H "authorization: Bearer sk-你的FishXCode-Token" \
  -d '{
    "model": "claude-opus-4-7",
    "messages": [
      { "role": "user", "content": "用一句话说明你是谁" }
    ]
  }'
```

```bash [GPT 模型]
curl https://api.fishxcode.com/v1/chat/completions \
  -H "content-type: application/json" \
  -H "authorization: Bearer sk-你的FishXCode-Token" \
  -d '{
    "model": "gpt-5.5",
    "messages": [
      { "role": "user", "content": "用一句话说明你是谁" }
    ]
  }'
```

:::

If curl does not work, QClaw is unlikely to work either. Check the API Key, model permissions, and Base URL first.

1. Save the settings or `config.yaml`
2. Restart QClaw
3. Select `gpt-5.5` or `claude-opus-4-7`
4. Send a short question, such as `用一句话说明你是谁`
5. A normal response means the integration is working

## FAQ

### 404 or endpoint address errors

If filling in a Base URL, use `https://api.fishxcode.com/v1`. If filling in a full URL, use `https://api.fishxcode.com/v1/chat/completions`.

### Can Claude models be used?

Yes. The prerequisites are that FishXCode currently supports the model and QClaw calls it through the OpenAI Compatible protocol. Use `claude-opus-4-7` as the model name.

### Authentication fails

Copy the API Key again, make sure there are no extra spaces, and check that the key has not been deleted or disabled.
