# Usando FishXCode no CodeBuddy

> Primeira vez usando? Conclua primeiro a [configuração de conta e recarga](/pt/account) e obtenha uma API Key no [console do FishXCode](https://fishxcode.com/console/token).

## 1. Instalar e iniciar o CodeBuddy Code

Este tutorial configura a ferramenta de linha de comando **CodeBuddy Code** da Tencent Cloud. Primeiro confirme que o Node.js 18.0 ou superior está instalado localmente e depois instale:

```bash
npm install -g @tencent-ai/codebuddy-code
```

Verifique a versão:

```bash
codebuddy --version
```

Inicie o CodeBuddy Code:

```bash
codebuddy
```

Na primeira inicialização, conclua o login conforme as instruções. Depois, você pode digitar `/model` na linha de comando para verificar se os modelos personalizados foram carregados corretamente.

## 2. Abrir o arquivo de configuração do CodeBuddy

O CodeBuddy Code permite conectar modelos personalizados por meio do arquivo local `models.json`. Localização do arquivo:

- macOS / Linux: `~/.codebuddy/models.json`
- Windows: `C:\Users\<用户名>\.codebuddy\models.json`

Se o diretório ou o arquivo não existir, crie-o primeiro.

## 3. Escrever a configuração de modelos do FishXCode

A configuração de LLMs de terceiros do CodeBuddy Code usa o protocolo compatível com OpenAI Chat Completions. Aqui, `vendor: "OpenAI"` indica compatibilidade de protocolo, não significa que apenas modelos OpenAI possam ser preenchidos; modelos Claude também podem ser chamados pelo endpoint OpenAI compatible do FishXCode.

Escreva o conteudo abaixo em `~/.codebuddy/models.json`:

```json
{
  "models": [
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
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
- `availableModels` deve incluir os IDs definidos em `models[].id`; caso contrário, os novos modelos podem não aparecer em `/model`.
- Um gateway de terceiros precisa do endereco completo de Chat Completions: `https://api.fishxcode.com/v1/chat/completions`.
:::

## 4. Manter o Tencent Coding Plan junto com o FishXCode

Se você também precisar manter o Coding Plan oficial da Tencent, adicione os modelos FishXCode ao mesmo `models.json`:

```json
{
  "models": [
    {
      "id": "tc-code-latest",
      "name": "Auto",
      "vendor": "Tencent Cloud",
      "apiKey": "YOUR_TENCENT_CODING_PLAN_KEY",
      "url": "https://api.lkeap.cloud.tencent.com/coding/v3"
    },
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    }
  ],
  "availableModels": [
    "tc-code-latest",
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

## 5. Reiniciar e selecionar o modelo

1. Salve `~/.codebuddy/models.json`
2. Feche completamente o CodeBuddy Code em execucao
3. Execute `codebuddy` novamente
4. Digite `/model`
5. Selecione `gpt-5.5` ou `claude-opus-4-7`
6. Envie um prompt de teste, por exemplo `解释当前项目结构`

## Perguntas frequentes

### `/model` nao mostra os modelos

Verifique primeiro `availableModels`. O ID do modelo deve aparecer tanto em `models[].id` quanto em `availableModels`.

### O modelo aparece, mas a chamada falha

Verifique primeiro `url`. A configuração de modelos de terceiros do FishXCode deve usar o endereço completo `https://api.fishxcode.com/v1/chat/completions`.

### Falha de autenticação

Copie novamente a API Key do console e confirme que não há espaços ou quebras de linha extras.
