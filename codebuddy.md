# 在 CodeBuddy 中使用 FishXCode

> 首次使用？请先完成 [账户注册与充值](/account)，并在 [FishXCode 控制台](https://fishxcode.com/console/token) 获取 API Key。

## 一、安装并启动 CodeBuddy Code

本教程配置的是腾讯云 **CodeBuddy Code** 命令行工具。请先确认本地已安装 Node.js 18.0 或更高版本，然后安装：

```bash
npm install -g @tencent-ai/codebuddy-code
```

检查版本：

```bash
codebuddy --version
```

启动 CodeBuddy Code：

```bash
codebuddy
```

首次启动时按提示完成登录。后续可在命令行中输入 `/model` 检查自定义模型是否加载成功。

## 二、打开 CodeBuddy 配置文件

CodeBuddy Code 支持通过本地 `models.json` 接入自定义模型。配置文件位置：

- macOS / Linux：`~/.codebuddy/models.json`
- Windows：`C:\Users\<用户名>\.codebuddy\models.json`

如果目录或文件不存在，请先创建。

## 三、写入 FishXCode 模型配置

CodeBuddy Code 的第三方大模型配置走 OpenAI Chat Completions 兼容协议。这里的 `vendor: "OpenAI"` 表示协议兼容，不代表只能填写 OpenAI 模型；Claude 模型也可以通过 FishXCode 的 OpenAI 兼容接口调用。

将下面内容写入 `~/.codebuddy/models.json`：

```json
{
  "models": [
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://fishxcode.com/v1/chat/completions"
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://fishxcode.com/v1/chat/completions"
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
- `availableModels` 必须包含上面 `models[].id` 中的模型 ID，否则 `/model` 里可能看不到新增模型。
- 第三方中转站需要完整 Chat Completions 地址：`https://fishxcode.com/v1/chat/completions`。
:::

## 四、保留腾讯 Coding Plan 与 FishXCode

如果你还需要保留腾讯官方 Coding Plan，可以在同一个 `models.json` 中追加 FishXCode 模型：

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
      "url": "https://fishxcode.com/v1/chat/completions"
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://fishxcode.com/v1/chat/completions"
    }
  ],
  "availableModels": [
    "tc-code-latest",
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

## 五、重启并选择模型

1. 保存 `~/.codebuddy/models.json`
2. 完全退出正在运行的 CodeBuddy Code
3. 重新执行 `codebuddy`
4. 输入 `/model`
5. 选择 `gpt-5.5` 或 `claude-opus-4-7`
6. 发送测试提示词，例如 `解释当前项目结构`

## 常见问题

### `/model` 看不到模型

先检查 `availableModels`。模型 ID 必须同时出现在 `models[].id` 和 `availableModels` 中。

### 模型出现但调用失败

优先检查 `url`。FishXCode 第三方模型配置应使用完整地址 `https://fishxcode.com/v1/chat/completions`。

### 鉴权失败

请重新复制控制台中的 API Key，确认没有多余空格或换行。
