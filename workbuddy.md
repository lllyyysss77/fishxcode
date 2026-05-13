# 在 WorkBuddy 中使用 FishXCode

> 首次使用？请先完成 [账户注册与充值](/account)，并在 [FishXCode 控制台](https://fishxcode.com/console/token) 获取 API Key。

## 一、打开 WorkBuddy 配置文件

WorkBuddy 支持通过本地 `models.json` 接入自定义模型。配置文件位置：

- macOS / Linux：`~/.workbuddy/models.json`
- Windows：`C:\Users\<用户名>\.workbuddy\models.json`

如果目录或文件不存在，请先创建。

## 二、配置 FishXCode 第三方模型

WorkBuddy 的第三方模型配置使用 OpenAI Chat Completions 兼容协议。这里的 `vendor: "OpenAI"` 表示接口协议，不代表模型只能是 OpenAI 模型；Claude 模型也可以通过 FishXCode 的 OpenAI 兼容接口调用。

将下面内容写入 `~/.workbuddy/models.json`：

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

::: warning 重要
- 请将 `sk-你的FishXCode-Token` 替换为你在 FishXCode 控制台创建的实际 API Key。
- `availableModels` 必须包含上面 `models[].id` 中的模型 ID，否则 WorkBuddy 可能不会在下拉列表中显示。
- `url` 需要填写完整 Chat Completions 地址：`https://fishxcode.com/v1/chat/completions`。
:::

## 三、保留腾讯 Token Plan 与 FishXCode

如果你还需要保留腾讯官方 Token Plan，可以把官方模型和 FishXCode 模型放在同一个 `models.json` 中：

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

## 四、重启并验证

1. 保存 `models.json`
2. 完全退出 WorkBuddy
3. 重新启动 WorkBuddy
4. 打开模型选择区域
5. 选择 `gpt-5.5` 或 `claude-opus-4-7`
6. 发送一个简单问题验证响应

## 常见问题

### 看不到新增模型

优先检查 `availableModels` 是否包含对应模型 ID，再检查 `models.json` 是否为合法 JSON。

### 调用失败或红色异常

请检查 `url` 是否为完整地址 `https://fishxcode.com/v1/chat/completions`，不要只填写 `https://fishxcode.com/v1`。

### 鉴权失败

请重新复制控制台中的 API Key，确认没有多余空格或换行。
