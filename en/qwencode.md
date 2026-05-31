# Using FishXCode with Qwen Code

## Install Qwen Code

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

## Configure Environment Variables

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

::: warning Important
Replace `sk-xxx` with your actual token from the [FishXCode Console](https://fishxcode.com/console/token).
:::

## Launch Directly

```bash
cd my-project
qwen
```

## Persistent Configuration

Add environment variables to your shell configuration file:

- **Linux/macOS**: Add to `~/.bashrc` or `~/.zshrc`
- **Windows PowerShell**: Add to `$PROFILE`
