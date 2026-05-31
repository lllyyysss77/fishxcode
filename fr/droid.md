# Utiliser FishXCode avec Droid CLI

## Installation

::: code-group

```bash [macOS/Linux]
curl -fsSL https://app.factory.ai/cli | sh
```

```powershell [Windows]
irm https://app.factory.ai/cli/windows | iex
```

:::

## Configuration du Modèle

Éditez `~/.factory/config.json` :

```json
{
  "custom_models": [
    {
      "model_display_name": "fishxcode-gpt5",
      "model": "gpt-5",
      "base_url": "https://api.fishxcode.com/v1",
      "api_key": "VOTRE_CLÉ_FISHXCODE",
      "provider": "generic-chat-completion-api",
      "max_tokens": 1280000
    }
  ]
}
```

::: warning Important
Remplacez `VOTRE_CLÉ_FISHXCODE` par votre clé API.
:::

## Lancement Direct

Tapez `droid`, entrez `/model`, et sélectionnez `fishxcode-gpt5` dans Custom Model.
