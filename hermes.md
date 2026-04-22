# 在 Hermes 中使用 FishXCode

::: info 项目简介
Hermes Agent 是 Nous Research 推出的通用 AI Agent，支持 CLI 对话、工具调用、记忆、技能、网关和定时任务。它既可以连接官方支持的云服务，也可以接入任意 OpenAI 兼容端点。

- 官方网站：[https://hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com)
- 文档：[https://hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs)
- GitHub：[https://github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
:::

## 前提条件

- 已安装 Hermes
- 已获取 FishXCode API Key（[控制台获取](https://fishxcode.com/console/token)）

## 安装 Hermes

::: info 环境要求
- macOS / Linux / WSL2
- Windows 原生不受支持，建议使用 WSL2
:::

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

安装完成后重新加载终端配置：

```bash
source ~/.zshrc
```

如果你使用的是 `bash`，请改为：

```bash
source ~/.bashrc
```

## 配置 FishXCode

Hermes 官方推荐优先使用 `hermes model` 进行交互式配置。对于 FishXCode，这里选择 **Custom endpoint** 即可，因为 FishXCode 提供 OpenAI 兼容接口。

### 方式一：使用 `hermes model` 交互式配置（推荐）

运行：

```bash
hermes model
```

按提示填写以下内容：

- Provider：`Custom endpoint (self-hosted / VLLM / etc.)`
- API base URL：`https://fishxcode.com/v1`
- API key：你的 FishXCode Token
- Model name：`gpt-5.4`
- Context length：留空即可，或按实际模型上下文填写

配置完成后，Hermes 会把模型、提供商和地址写入 `~/.hermes/config.yaml`。

### 方式二：手动编辑配置文件

Hermes 的主配置文件位于 `~/.hermes/config.yaml`。如果目录不存在，可以先创建：

```bash
mkdir -p ~/.hermes
touch ~/.hermes/config.yaml
touch ~/.hermes/.env
```

然后在 `~/.hermes/.env` 中写入你的 Token：

```bash
OPENAI_API_KEY=sk-你的FishXCode-Token
```

再把以下内容写入 `~/.hermes/config.yaml`：

```yaml
model:
  default: gpt-5.4
  provider: custom
  base_url: https://fishxcode.com/v1
```

::: tip 提示
Hermes 对自定义端点会优先读取 `config.yaml` 中的 `provider`、`default`、`base_url`，API Key 则可以直接写在 `config.yaml`，也可以像上面这样放到 `~/.hermes/.env` 的 `OPENAI_API_KEY` 中。为了避免明文写密钥，推荐使用 `.env`。
:::

## 切换模型

如果你想使用其他 FishXCode 支持的模型，只需要修改 `model.default`，或者重新运行 `hermes model`。

例如：

```yaml
model:
  default: claude-sonnet-4-5-20250929
  provider: custom
  base_url: https://fishxcode.com/v1
```

::: warning 注意
这里的前提是该模型可以通过 FishXCode 的 OpenAI 兼容入口访问。如果你不确定具体模型 ID，先参考 [支持的模型](/models) 页面，再填入 `default` 字段。
:::

## 启动使用

直接进入对话：

```bash
hermes
```

也可以直接发起一条测试消息：

```bash
hermes chat "用一句话回复：FishXCode 已连接"
```

如果你已经配置成功，在会话内还可以用 `/model` 切换到已经接入过的模型。

## 验证配置

优先做这三个检查：

```bash
hermes doctor
```

```bash
hermes config check
```

```bash
hermes chat "请只回复 ok"
```

如果最后一条命令能正常返回内容，说明 FishXCode 接入已经生效。

## 常见问题

### 为什么要选 `Custom endpoint`？

因为 Hermes 官方把任意 OpenAI 兼容接口统一归类为 `provider: custom`。FishXCode 的接入方式正符合这个模式，所以不需要额外写 Hermes 专用适配器。

### `OPENAI_BASE_URL` 或 `LLM_MODEL` 为什么不生效？

Hermes 官方已经移除了这两个旧环境变量的读取逻辑。现在模型、提供商和端点地址以 `~/.hermes/config.yaml` 为准，不应再依赖旧变量。

### 配置了 Key 但还是认证失败怎么办？

按下面顺序排查：

1. 确认 `base_url` 写的是 `https://fishxcode.com/v1`
2. 确认 Token 来自 [FishXCode 控制台](https://fishxcode.com/console/token)
3. 确认 `model.default` 填的是有效模型 ID，例如 `gpt-5.4`
4. 运行 `hermes config check` 与 `hermes doctor` 查看具体报错

### Windows 能直接安装吗？

截至 2026 年 4 月 22 日，Hermes 官方文档仍注明 **Windows 原生不受支持**，建议在 WSL2 中安装和运行。
