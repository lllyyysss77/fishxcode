# 在 Qwen Code 中使用 FishXCode

## 安装 Qwen Code

::: code-group

```bash [pnpm]
pnpm install -g @qwen-code/qwen-code
```

```bash [npm]
npm install -g @qwen-code/qwen-code
```

```bash [yarn]
yarn global add @qwen-code/qwen-code
```

```bash [bunx]
bunx --global @qwen-code/qwen-code
```

:::

## 配置环境变量

::: code-group

```bash [Linux/macOS]
export OPENAI_API_KEY="sk-xxx"
export OPENAI_BASE_URL="https://api.fishxcode.com/v1"
export OPENAI_MODEL="gpt-5"
```

```powershell [Windows PowerShell]
$env:OPENAI_API_KEY="sk-xxx"
$env:OPENAI_BASE_URL="https://api.fishxcode.com/v1"
$env:OPENAI_MODEL="gpt-5"
```

```cmd [Windows CMD]
set OPENAI_API_KEY="sk-xxx"
set OPENAI_BASE_URL="https://api.fishxcode.com/v1"
set OPENAI_MODEL="gpt-5"
```

:::

::: warning 重要
请将 `sk-xxx` 替换为你在 [FishXCode 控制台](https://fishxcode.com/console/token) 获取的实际 Token。
:::

## 快速启动

```bash
cd my-project
qwen
```

## 持久化配置

将环境变量添加到 shell 配置文件以避免每次手动设置：

- **Linux/macOS**: 添加到 `~/.bashrc` 或 `~/.zshrc`
- **Windows PowerShell**: 添加到 `$PROFILE`
