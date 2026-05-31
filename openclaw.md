# 在 OpenClaw 中使用 FishXCode

::: info 项目简介
OpenClaw 是一个开源、自托管的个人 AI 助手平台，将消息应用连接到运行在你自己硬件上的 AI Agent。专为希望在不放弃数据控制权的前提下使用自主 AI 助手的开发者和高级用户设计。

- 官方网站：[https://openclaw.ai](https://openclaw.ai)
- 文档：[https://docs.openclaw.ai](https://docs.openclaw.ai)
- GitHub：[https://github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
:::

## 前提条件

- 已安装 OpenClaw（参考下方安装章节）
- 已获取 FishXCode API Key（[控制台获取](https://fishxcode.com/console/token)）

## 核心特性

### 多渠道集成

- **全平台覆盖**：支持飞书（Lark）、Discord、Slack、Microsoft Teams 等
- **单一网关**：通过单个 Gateway 进程管理所有渠道
- **语音支持**：支持 macOS/iOS/Android 语音交互
- **画布界面**：可渲染交互式 Canvas 界面

### 自托管与数据安全

- **完全自托管**：运行在你自己的机器或服务器上
- **开源**：MIT 协议，代码完全透明
- **本地数据存储**：上下文与技能存储在本地，不上传云端

### 智能 Agent 能力

- **持久运行**：支持后台持久运行与持久记忆
- **定时任务**：支持 cron 定时任务
- **会话隔离**：按 Agent/工作区/发送者隔离会话
- **多 Agent 路由**：支持多 Agent 协作
- **工具调用**：原生支持工具调用与代码执行

## 安装

::: info 环境要求
- FishXCode API Key
- 使用 npm/git 方式需要 Node.js 22+；curl 一键安装会自动处理依赖
:::

::: code-group

```bash [curl（推荐）]
curl -fsSL https://openclaw.ai/install.sh | bash
```

```bash [npm]
npm install -g openclaw@latest
```

```bash [curl（git 模式）]
curl -fsSL https://openclaw.ai/install.sh | bash -s -- --install-method git
```

```bash [手动克隆]
git clone https://github.com/openclaw/openclaw.git
cd openclaw && pnpm install && pnpm run build
pnpm run openclaw onboard
```

:::

安装完成后运行引导向导（手动克隆方式已在上方命令中包含）：

```bash
openclaw onboard
```

## 配置

### 配置文件位置

OpenClaw 配置文件位于 `~/.openclaw/openclaw.json`：

- **macOS**: `/Users/你的用户名/.openclaw/openclaw.json`
- **Linux**: `/home/你的用户名/.openclaw/openclaw.json`
- **Windows**: `C:\Users\你的用户名\.openclaw\openclaw.json`

如果文件不存在，请先创建：

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

::: tip 提示
如果你之前运行过 `openclaw onboard`，配置文件会自动生成，只需在已有内容上修改即可。
:::

### 配置文件结构

`openclaw.json` 的整体结构分为两大部分：

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      // 配置 AI 模型提供商
    }
  },
  "agents": {
    "defaults": {
      // 配置默认模型、工作目录等
    }
  }
}
```

- `models.providers` — 定义服务提供商，包括地址、Key、模型列表
- `models.mode` — 设为 `"merge"` 时，自定义配置会与内置默认配置合并，**强烈建议始终加上**
- `agents.defaults.model.primary` — 默认使用的模型，格式为 `provider名称/模型ID`
- `api` — API 协议类型，Anthropic 模型用 `"anthropic-messages"`，OpenAI 兼容模型用 `"openai-responses"`

### 配置方式

#### 配置 Anthropic（Claude）模型

将以下内容写入 `openclaw.json`：

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-anthropic": {
        "baseUrl": "https://api.fishxcode.com",
        "apiKey": "sk-你的FishXCode-Token",
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

::: warning 重要
- 请将 `sk-你的FishXCode-Token` 替换为你在 [FishXCode 控制台](https://fishxcode.com/console/token) 获取的实际 Token
- **Anthropic 协议的 `baseUrl` 不要带 `/v1`**，SDK 会自动拼接路径
:::

#### 配置 OpenAI（GPT）模型

通过 FishXCode 调用 OpenAI 模型时，`api` 字段须设为 `openai-responses`：

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-openai": {
        "baseUrl": "https://api.fishxcode.com/v1",
        "apiKey": "sk-你的FishXCode-Token",
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

::: tip 提示
**OpenAI 协议需要带 `/v1`**，即 `https://api.fishxcode.com/v1`。这是因为两种 SDK 的路径拼接逻辑不同。
:::

#### 同时配置 Anthropic + OpenAI（推荐）

在 `models.providers` 下并列添加两个 provider，即可同时使用两家模型：

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-anthropic": {
        "baseUrl": "https://api.fishxcode.com",
        "apiKey": "sk-你的FishXCode-Token",
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
        "apiKey": "sk-你的FishXCode-Token",
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

切换默认模型时，只需修改 `model.primary` 的值：

- `"fishxcode-anthropic/claude-opus-4-6"` → Claude Opus 4.6
- `"fishxcode-anthropic/claude-sonnet-4-5-20250929"` → Claude Sonnet 4.5
- `"fishxcode-openai/gpt-5"` → GPT-5
- `"fishxcode-openai/gpt-5-codex"` → GPT-5 Codex

### 关键字段说明

| 字段 | 含义 | Anthropic（Claude） | OpenAI（GPT） |
| --- | --- | --- | --- |
| `baseUrl` | API 代理地址 | `https://api.fishxcode.com` | `https://api.fishxcode.com/v1` |
| `apiKey` | 你的 API Key | `sk-你的FishXCode-Token` | `sk-你的FishXCode-Token` |
| `api` | API 协议类型 | `anthropic-messages` | `openai-responses` |
| `mode` | 配置合并模式 | `merge`（推荐） | `merge`（推荐） |
| `models[].id` | 模型 ID | `claude-opus-4-6` | `gpt-5` |
| `model.primary` | 默认模型 | `fishxcode-anthropic/claude-opus-4-6` | `fishxcode-openai/gpt-5` |
| `reasoning` | 是否启用推理模式 | `false`（根据模型而定） | `true`（GPT-5.x 支持） |

## 验证配置

运行以下命令确认配置生效：

```bash
openclaw
```

如果配置正确，OpenClaw 将正常启动并通过 FishXCode 代理发送请求。

也可以用以下命令检查模型列表：

```bash
openclaw models list
```

查看当前模型和认证状态：

```bash
openclaw models status
```

综合诊断：

```bash
openclaw doctor
```

## 启动服务

配置完成后，启动 OpenClaw：

```bash
openclaw start
```

启动后即可通过配置的渠道与 AI 助手交互。

重启 Gateway：

```bash
openclaw gateway restart
```

## 常见问题

### 403 被拦截

**症状**：provider 配好了，curl 直接请求 API 正常返回 200，但 OpenClaw 发出去就是 403 Your request was blocked。

**原因**：OpenClaw 底层使用 `@anthropic-ai/sdk`，发请求时会携带官方 SDK 的 User-Agent（如 `Anthropic/JS 0.73.0`），部分 CDN 或 WAF 会拦截这类 UA。

**解决**：在 provider 配置里添加 `headers` 字段覆盖 UA：

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

### baseUrl 不要带 /v1（Anthropic 协议）

**症状**：请求直接 404，日志中看到请求路径变成了 `/v1/v1/messages`。

**原因**：Anthropic SDK 会在 baseURL 后面自动拼接 `/v1/messages`。如果你的 baseUrl 已经写了 `/v1`，实际请求会变成双重路径。

**解决**：Anthropic 协议的 baseUrl 只写到域名，不带 `/v1`：

```json
{
  "baseUrl": "https://api.fishxcode.com"
}
```

::: tip 提示
OpenAI 协议需要带 `/v1`，即 `https://api.fishxcode.com/v1`。这是因为两种 SDK 的路径拼接逻辑不同。
:::

### api 字段只认三个值

**症状**：启动报 Config invalid，或者模型列表里看不到你配的 provider。

**原因**：OpenClaw 的 `api` 字段做了严格校验，只接受以下三个值：

| 值 | 对应格式 |
| --- | --- |
| `anthropic-messages` | Anthropic Messages API |
| `openai-completions` | OpenAI Chat Completions |
| `openai-responses` | OpenAI Responses API |

写成 `openai-chat`、`openai`、`anthropic` 等都会报错。

**解决**：通过 FishXCode 使用时，Claude 模型填 `anthropic-messages`，GPT 模型填 `openai-responses`。

### openai-completions 收到空回复（不要用于 GPT 模型）

**症状**：api 设为 `openai-completions`，请求成功（日志 `isError=false`），但 UI 上显示空消息。

**原因**：OpenClaw 内部使用 Anthropic 格式处理消息流，`openai-completions` 返回的 OpenAI 格式响应在某些情况下无法正确映射。

**解决**：通过 FishXCode 调用 GPT 模型时，请使用 `openai-responses` 而非 `openai-completions`。

### 配置了但没生效

**症状**：修改了 `openclaw.json` 但 OpenClaw 仍使用旧配置。

**原因**：OpenClaw 有两处 provider 配置需要同步：

```
~/.openclaw/openclaw.json              → models.providers
~/.openclaw/agents/main/agent/models.json → providers
```

只改一个可能会出现不生效的情况。

**解决**：修改后用以下命令确认：

```bash
openclaw models status
```

或者重启 OpenClaw Gateway：

```bash
openclaw gateway restart
```

### JSON 格式报错

JSON 格式常见错误：

- **多余逗号**：最后一个元素后面不能有逗号
- **缺少逗号**：两个并列的键值对之间必须用逗号分隔
- **引号问题**：JSON 只接受英文双引号 `"`，不能用中文引号或单引号
- **括号不匹配**：每个 `{` 都要有对应的 `}`，每个 `[` 都要有对应的 `]`

可以使用以下命令验证格式：

```bash
python3 -m json.tool ~/.openclaw/openclaw.json
```

### reasoning 字段是什么？

`reasoning` 控制是否启用模型的推理/思考模式。设为 `true` 时，模型会先进行内部推理再输出回复，但会消耗更多 token。

GPT-5.x Codex 系列通常支持 `reasoning`，Claude 模型根据版本而定。如果不确定，设为 `false`。

### openai-completions 和 openai-responses 有什么区别？

- **openai-responses** — 对应 OpenAI Responses API（`/v1/responses`）。**通过 FishXCode 使用 GPT 模型时用这个。**
- **openai-completions** — 对应 OpenAI Chat Completions API（`/v1/chat/completions`），通用兼容格式，但在 FishXCode 的 GPT 模型上可能导致空回复，**不推荐使用**。

### 排查工具速查

| 命令 | 用途 |
| --- | --- |
| `openclaw models status` | 查看当前模型和认证状态 |
| `openclaw models list` | 查看已配置的模型列表 |
| `openclaw doctor` | 综合诊断 |
| `openclaw gateway restart` | 重启 Gateway |

::: tip 排查核心思路
先用 curl 确认 FishXCode API 本身正常，再检查 OpenClaw 端发出的请求有什么不同（UA、路径、格式）。
:::
