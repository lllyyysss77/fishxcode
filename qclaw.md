# 在 QClaw 中使用 FishXCode

> 首次使用？请先完成 [账户注册与充值](/account)，并在 [FishXCode 控制台](https://fishxcode.com/console/token) 获取 API Key。

## 一、确认接入参数

QClaw 接入第三方大模型 API 时，优先使用 OpenAI Compatible 协议。即使调用的是 Claude 模型，也填写 OpenAI 兼容接口参数。

| 配置项 | 值 |
| --- | --- |
| Protocol | `OpenAI Compatible` |
| Base URL | `https://fishxcode.com/v1` |
| API Key | FishXCode 控制台创建的 API Key |
| OpenAI 模型 | `gpt-5.5` |
| Claude 模型 | `claude-opus-4-7` |

如果 QClaw 要求填写完整 Endpoint，请使用：

```text
https://fishxcode.com/v1/chat/completions
```

## 二、通过设置界面配置

如果你的 QClaw 版本提供图形界面，可以按下面填写：

```text
Provider: OpenAI Compatible
Base URL: https://fishxcode.com/v1
API Key: sk-你的FishXCode-Token
Model: claude-opus-4-7
```

如果要使用 GPT 模型，将 `Model` 改为：

```text
gpt-5.5
```

## 三、通过 config.yaml 配置

如果你的 QClaw 版本使用 `~/.qclaw/config.yaml`，可以参考下面模板：

```yaml
llm:
  provider: openai-compatible
  base_url: https://fishxcode.com/v1
  api_key: sk-你的FishXCode-Token
  model: claude-opus-4-7
  max_tokens: 8192
```

切换到 OpenAI 模型时，只需要替换 `model`：

```yaml
llm:
  provider: openai-compatible
  base_url: https://fishxcode.com/v1
  api_key: sk-你的FishXCode-Token
  model: gpt-5.5
  max_tokens: 8192
```

如果客户端要求完整接口地址，可以改成：

```yaml
llm:
  provider: openai-compatible
  url: https://fishxcode.com/v1/chat/completions
  api_key: sk-你的FishXCode-Token
  model: claude-opus-4-7
  max_tokens: 8192
```

::: warning 重要
不同 QClaw 版本字段名可能不同，比如 `base_url`、`baseURL`、`apiBase` 或 `url`。如果保存后不生效，优先检查字段名和 QClaw 实际读取的配置文件路径。
:::

## 四、通过环境变量配置

如果 QClaw 支持环境变量，也可以使用：

::: code-group

```bash [Linux/macOS]
export OPENAI_BASE_URL="https://fishxcode.com/v1"
export OPENAI_API_KEY="sk-你的FishXCode-Token"
export QCLAW_MODEL="claude-opus-4-7"
qclaw
```

```powershell [Windows PowerShell]
$env:OPENAI_BASE_URL="https://fishxcode.com/v1"
$env:OPENAI_API_KEY="sk-你的FishXCode-Token"
$env:QCLAW_MODEL="claude-opus-4-7"
qclaw
```

:::

需要使用 GPT 模型时，将 `QCLAW_MODEL` 改为 `gpt-5.5`。

## 五、验证配置

先用 OpenAI 兼容接口验证 API Key 是否有效：

::: code-group

```bash [Claude 模型]
curl https://fishxcode.com/v1/chat/completions \
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
curl https://fishxcode.com/v1/chat/completions \
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

如果 curl 不通，QClaw 里也很难成功。请先检查 API Key、模型权限和 Base URL。

1. 保存设置或 `config.yaml`
2. 重启 QClaw
3. 选择 `gpt-5.5` 或 `claude-opus-4-7`
4. 发送短问题，例如 `用一句话说明你是谁`
5. 正常返回响应即表示接入成功

## 常见问题

### 404 或接口地址错误

如果填写 Base URL，使用 `https://fishxcode.com/v1`。如果填写完整 URL，使用 `https://fishxcode.com/v1/chat/completions`。

### Claude 模型能不能用

可以。前提是 FishXCode 当前支持该模型，并且 QClaw 使用 OpenAI Compatible 协议调用。模型名填写 `claude-opus-4-7`。

### 鉴权失败

请重新复制 API Key，确认没有多余空格，并检查该 Key 未被删除或禁用。
