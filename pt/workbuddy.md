# Usando FishXCode no WorkBuddy

> Primeira vez usando? Conclua primeiro a [configuração de conta e recarga](/pt/account) e obtenha uma API Key no [console do FishXCode](https://fishxcode.com/console/token).

## 1. Abrir o arquivo de configuração do WorkBuddy

O WorkBuddy permite conectar modelos personalizados por meio do arquivo local `models.json`. Localização do arquivo:

- macOS / Linux: `~/.workbuddy/models.json`
- Windows: `C:\Users\<用户名>\.workbuddy\models.json`

Se o diretório ou o arquivo não existir, crie-o primeiro.

## 2. Configurar modelos de terceiros do FishXCode

A configuração de modelos de terceiros do WorkBuddy usa o protocolo compatível com OpenAI Chat Completions. Aqui, `vendor: "OpenAI"` indica o protocolo da interface, não significa que apenas modelos OpenAI possam ser usados; modelos Claude também são chamados pelo endpoint OpenAI compatible do FishXCode.

Escreva o conteudo abaixo em `~/.workbuddy/models.json`:

```json
{
  "models": [
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    }
  ],
  "availableModels": [
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

::: warning Importante
- Substitua `sk-你的FishXCode-Token` pela API Key real criada no console do FishXCode.
- `availableModels` deve incluir os IDs definidos em `models[].id`; caso contrário, o WorkBuddy pode não exibir os modelos na lista suspensa.
- O campo `url` deve usar o endereco completo de Chat Completions: `https://api.fishxcode.com/v1/chat/completions`.
:::

## 3. Manter o Tencent Token Plan junto com o FishXCode

Se você também precisar manter o Token Plan oficial da Tencent, coloque os modelos oficiais e os modelos FishXCode no mesmo `models.json`:

```json
{
  "models": [
    {
      "id": "tc-code-latest",
      "name": "Auto",
      "vendor": "OpenAI",
      "url": "https://api.lkeap.cloud.tencent.com/plan/v3/chat/completions",
      "apiKey": "your-token-plan-api-key",
      "maxInputTokens": 100000,
      "maxOutputTokens": 4096
    },
    {
      "id": "hunyuan-2.0-instruct",
      "name": "Tencent HY 2.0 Instruct",
      "vendor": "OpenAI",
      "url": "https://api.lkeap.cloud.tencent.com/plan/v3/chat/completions",
      "apiKey": "your-token-plan-api-key",
      "maxInputTokens": 100000,
      "maxOutputTokens": 4096
    },
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    }
  ],
  "availableModels": [
    "tc-code-latest",
    "hunyuan-2.0-instruct",
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

## 4. Reiniciar e verificar

1. Salve `models.json`
2. Feche completamente o WorkBuddy
3. Inicie o WorkBuddy novamente
4. Abra a area de selecao de modelos
5. Selecione `gpt-5.5` ou `claude-opus-4-7`
6. Envie uma pergunta simples para verificar a resposta

## Perguntas frequentes

### Os novos modelos nao aparecem

Verifique primeiro se `availableModels` contém o ID do modelo correspondente e depois confira se `models.json` é um JSON válido.

### A chamada falha ou aparece um erro em vermelho

Confira se `url` usa o endereco completo `https://api.fishxcode.com/v1/chat/completions`; nao preencha apenas `https://api.fishxcode.com/v1`.

### Falha de autenticação

Copie novamente a API Key do console e confirme que não há espaços ou quebras de linha extras.
