# 在 Claude Desktop 中使用 FishXCode

## 适用范围

本文适用于 Claude Desktop 新版的 **Cowork / Code 第三方推理接入** 模式，也就是 Anthropic 官方文档中的 `third-party inference`。

如果你想要的是：

- 在桌面端使用 Claude 的 **Cowork**
- 在桌面端使用 Claude 的 **Code**
- 通过自定义 API / 网关把请求转到 FishXCode

那么走的就是这套配置方式。

::: warning 注意
这不是旧版“直接登录 Claude 账号即可聊天”的路径。接入第三方推理后，你主要会用到的是 **Cowork** 和 **Code** 标签页。
:::

## 前置准备

开始前请先准备：

1. 安装最新版 Claude Desktop
2. 准备一个 FishXCode Token
3. 确认你的 Token 可访问 Claude 模型

Token 获取地址：
- [https://fishxcode.com/console/token](https://fishxcode.com/console/token)

建议优先准备这些模型作为候选：

- `claude-sonnet-4-5-20250929`
- `claude-opus-4-5-20251101`
- `claude-haiku-4-5-20251001`

## 方式一：在 Claude Desktop 里直接配置（推荐）

Anthropic 官方推荐优先通过桌面端内置的第三方推理配置界面完成设置。

### 1. 启用 Developer mode

在 Claude Desktop 顶部菜单中依次进入：

```text
Help → Troubleshooting → Enable Developer mode
```

启用后，应用菜单里会出现 `Developer` 相关入口。

### 2. 打开第三方推理配置

继续进入：

```text
Developer → Configure third-party inference
```

这里会打开一套 Anthropic 官方提供的第三方推理配置流程。

### 3. 选择 Gateway 模式

在提供商类型里，选择：

```text
Gateway
```

FishXCode 这类自定义 API / 中转服务，对应的就是 `gateway` 模式。

### 4. 填写 FishXCode 配置

核心信息按下面填写：

- Gateway URL：`https://api.fishxcode.com`
- Authentication：`x-api-key`
- API Key：你的 FishXCode Token

如果界面要求填写模型列表，建议先填：

```text
claude-sonnet-4-5-20250929
claude-opus-4-5-20251101
claude-haiku-4-5-20251001
```

如果界面支持启用 `Code`，保持开启即可。

### 5. 应用到本地

完成配置后，优先选择：

```text
Apply locally
```

这会把第三方推理配置写入当前用户的本地 Claude Desktop 配置中。

配置成功后，重新打开 Claude Desktop，通常就能看到可用的 **Cowork / Code** 工作界面。

## 方式二：通过本地配置文件管理（进阶）

如果你想备份、迁移或排查配置，可以查看 Claude Desktop 本地写入的位置。

常见路径如下：

### macOS

```text
~/Library/Application Support/Claude-3p/claude_desktop_config.json
```

### Windows

```text
%APPDATA%\Claude-3p\claude_desktop_config.json
```

### Linux

```text
~/.config/Claude-3p/claude_desktop_config.json
```

::: tip 提示
本地文件里实际使用的是 `enterpriseConfig` 这组第三方推理配置。更稳妥的做法不是手写 JSON，而是先在应用内完成配置，再把生成结果纳入备份或同步。
:::

## FishXCode 推荐配置

如果你要手动核对字段，重点关注下面几项：

| 字段 | 建议值 |
| --- | --- |
| `inferenceProvider` | `gateway` |
| `inferenceGatewayBaseUrl` | `https://api.fishxcode.com` |
| `inferenceGatewayAuthScheme` | `x-api-key` |
| `inferenceGatewayApiKey` | 你的 FishXCode Token |
| `inferenceModels` | Claude 模型列表 |
| `isClaudeCodeForDesktopEnabled` | `true` |

推荐模型列表示例：

```text
claude-sonnet-4-5-20250929
claude-opus-4-5-20251101
claude-haiku-4-5-20251001
```

## 使用方式

配置完成后，你可以直接在 Claude Desktop 中：

- 进入 **Cowork** 做通用问答、文档分析、文件协作
- 进入 **Code** 做代码编辑、终端协作、工程任务

如果模型选择器可见，优先选择：

- 日常使用：`claude-sonnet-4-5-20250929`
- 复杂推理：`claude-opus-4-5-20251101`
- 轻量快速：`claude-haiku-4-5-20251001`

## 常见问题

### 为什么我找不到 `Enable Developer mode`？

先确认两件事：

1. 你使用的是较新的 Claude Desktop 版本
2. 你看的是真正的系统菜单栏，而不是窗口内部按钮

如果升级后仍然找不到，先彻底退出 Claude Desktop，再重新打开一次。

### Windows 提示 `Virtual Machine Platform not available` 怎么办？

如果 Claude Desktop 提示：

```text
Claude's workspace requires the Virtual Machine Platform on Windows. Enable this feature, then restart.
```

说明当前 Windows 没有启用 **Virtual Machine Platform**。请用管理员身份打开 PowerShell，执行：

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

执行完成后重启 Windows，再重新打开 Claude Desktop。

### 为什么我配置完后还是看不到 Cowork / Code？

按下面顺序排查：

1. 确认已经启用 Developer mode
2. 确认已执行 `Configure third-party inference`
3. 确认选择的是 `Gateway`
4. 确认 `gatewayUrl` 写的是 `https://api.fishxcode.com`
5. 确认 Token 有效且可访问 Claude 模型
6. 完成后彻底退出 Claude Desktop 再重开

### Gateway URL 为什么不是 `https://api.fishxcode.com/v1`？

因为 Claude Desktop 的第三方推理网关模式走的是 Anthropic 风格网关配置，不是 OpenAI 兼容的 `/v1/chat/completions` 路径。这里应填写网关根地址：

```text
https://api.fishxcode.com
```

### 配好了，但请求报认证错误怎么办？

优先检查：

1. 认证方式是否选成了 `x-api-key`
2. API Key 是否直接填入了 FishXCode Token
3. Token 是否已失效、被删除或权限不足

### 能不能手动改本地 JSON？

可以，但不建议把“第一次配置”建立在手写 JSON 上。原因很简单：Claude Desktop 的第三方推理字段会随着版本演进，直接在应用内完成配置更稳，出问题也更容易回滚。
