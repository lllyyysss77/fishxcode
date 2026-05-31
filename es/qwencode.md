# Usar FishXCode con Qwen Code

## Instalación

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

## Configurar Variables de Entorno

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
Reemplaza `sk-xxx` con tu token de la [Consola FishXCode](https://fishxcode.com/console/token).
:::

## Lanzamiento Directo

```bash
cd mi-proyecto
qwen
```
