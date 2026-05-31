# Usando FishXCode com Qwen Code

## Instalação

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

## Configurar Variáveis de Ambiente

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

:::

::: warning Importante
Substitua `sk-xxx` pelo seu token do [Console FishXCode](https://fishxcode.com/console/token).
:::

## Iniciar Diretamente

```bash
cd meu-projeto
qwen
```
