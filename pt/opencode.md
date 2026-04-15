# Usar FishXCode com OpenCode

## Instalar OpenCode

::: code-group

```bash [npm]
npm install -g opencode-ai@latest
```

```bash [brew]
brew install anomalyco/tap/opencode
```

```bash [scoop]
scoop install opencode
```

:::

## Configurar FishXCode

1. Obtenha sua API Key em [https://fishxcode.com/console/token](https://fishxcode.com/console/token)
2. Configure a variavel de ambiente:

::: code-group

```bash [Linux/macOS]
export FISHXCODE_TOKEN=sk-xxx
```

```powershell [Windows PowerShell]
$env:FISHXCODE_TOKEN="sk-xxx"
```

:::

3. Crie `opencode.json` na raiz do seu projeto ou em `~/.config/opencode/`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "fishxcode-anthropic": {
      "npm": "@ai-sdk/anthropic",
      "name": "fishxcode-anthropic",
      "options": {
        "baseURL": "https://fishxcode.com/v1",
        "apiKey": "{env:FISHXCODE_TOKEN}"
      },
      "models": {
        "claude-sonnet-4-6": {
          "name": "claude-sonnet-4-6"
        }
      }
    },
    "fishxcode-openai": {
      "npm": "@ai-sdk/openai-compatible",
      "name": "fishxcode-openai",
      "options": {
        "baseURL": "https://fishxcode.com/v1",
        "apiKey": "{env:FISHXCODE_TOKEN}"
      },
      "models": {
        "gpt-5.2-codex": {
          "name": "gpt-5.2-codex"
        }
      }
    }
  }
}
```

::: warning Importante
Substitua `sk-xxx` pelo seu token real obtido no [console do FishXCode](https://fishxcode.com/console/token).
:::

## Iniciar

```bash
cd my-project
opencode
```

Apos iniciar, selecione um modelo do provedor FishXCode para comecar.
