# Usando FishXCode no QClaw

> Primeira vez usando? Conclua primeiro a [configuração de conta e recarga](/pt/account) e obtenha uma API Key no [console do FishXCode](https://fishxcode.com/console/token).

## 1. Confirmar os parâmetros de integração

Ao conectar o QClaw a APIs de LLMs de terceiros, prefira o protocolo OpenAI Compatible. Mesmo ao chamar um modelo Claude, preencha os parâmetros da interface compatível com OpenAI.

| Item de configuração | Valor |
| --- | --- |
| Protocol | `OpenAI Compatible` |
| Base URL | `https://fishxcode.com/v1` |
| API Key | API Key criada no console do FishXCode |
| Modelo OpenAI | `gpt-5.5` |
| Modelo Claude | `claude-opus-4-7` |

Se o QClaw exigir o Endpoint completo, use:

```text
https://fishxcode.com/v1/chat/completions
```

## 2. Configurar pela interface de ajustes

Se a sua versão do QClaw oferecer interface gráfica, preencha assim:

```text
Provider: OpenAI Compatible
Base URL: https://fishxcode.com/v1
API Key: sk-你的FishXCode-Token
Model: claude-opus-4-7
```

Para usar um modelo GPT, altere `Model` para:

```text
gpt-5.5
```

## 3. Configurar por config.yaml

Se a sua versao do QClaw usa `~/.qclaw/config.yaml`, consulte o modelo abaixo:

```yaml
llm:
  provider: openai-compatible
  base_url: https://fishxcode.com/v1
  api_key: sk-你的FishXCode-Token
  model: claude-opus-4-7
  max_tokens: 8192
```

Ao trocar para um modelo OpenAI, basta substituir `model`:

```yaml
llm:
  provider: openai-compatible
  base_url: https://fishxcode.com/v1
  api_key: sk-你的FishXCode-Token
  model: gpt-5.5
  max_tokens: 8192
```

Se o cliente exigir o endereço completo da interface, você pode alterar para:

```yaml
llm:
  provider: openai-compatible
  url: https://fishxcode.com/v1/chat/completions
  api_key: sk-你的FishXCode-Token
  model: claude-opus-4-7
  max_tokens: 8192
```

::: warning Importante
Versões diferentes do QClaw podem usar nomes de campo diferentes, como `base_url`, `baseURL`, `apiBase` ou `url`. Se a configuração não funcionar depois de salvar, verifique primeiro os nomes dos campos e o caminho real do arquivo de configuração lido pelo QClaw.
:::

## 4. Configurar por variáveis de ambiente

Se o QClaw oferecer suporte a variáveis de ambiente, você também pode usar:

::: code-group

```bash [Linux/macOS]
export OPENAI_BASE_URL="https://fishxcode.com/v1"
export OPENAI_API_KEY="sk-你的FishXCode-Token"
export QCLAW_MODEL="claude-opus-4-7"
qclaw
```

```powershell [Windows PowerShell]
$env:OPENAI_BASE_URL="https://fishxcode.com/v1"
$env:OPENAI_API_KEY="sk-你的FishXCode-Token"
$env:QCLAW_MODEL="claude-opus-4-7"
qclaw
```

:::

Quando precisar usar um modelo GPT, altere `QCLAW_MODEL` para `gpt-5.5`.

## 5. Verificar a configuração

Primeiro use a interface compatível com OpenAI para verificar se a API Key é válida:

::: code-group

```bash [Modelo Claude]
curl https://fishxcode.com/v1/chat/completions \
  -H "content-type: application/json" \
  -H "authorization: Bearer sk-你的FishXCode-Token" \
  -d '{
    "model": "claude-opus-4-7",
    "messages": [
      { "role": "user", "content": "用一句话说明你是谁" }
    ]
  }'
```

```bash [Modelo GPT]
curl https://fishxcode.com/v1/chat/completions \
  -H "content-type: application/json" \
  -H "authorization: Bearer sk-你的FishXCode-Token" \
  -d '{
    "model": "gpt-5.5",
    "messages": [
      { "role": "user", "content": "用一句话说明你是谁" }
    ]
  }'
```

:::

Se o curl não funcionar, dificilmente o QClaw funcionará. Verifique primeiro a API Key, as permissões do modelo e a Base URL.

1. Salve os ajustes ou `config.yaml`
2. Reinicie o QClaw
3. Selecione `gpt-5.5` ou `claude-opus-4-7`
4. Envie uma pergunta curta, por exemplo `用一句话说明你是谁`
5. Se a resposta retornar normalmente, a integração foi concluída com sucesso

## Perguntas frequentes

### 404 ou endereço da interface incorreto

Ao preencher Base URL, use `https://fishxcode.com/v1`. Ao preencher a URL completa, use `https://fishxcode.com/v1/chat/completions`.

### O modelo Claude pode ser usado?

Sim. O requisito é que o FishXCode ofereça suporte ao modelo no momento e que o QClaw chame usando o protocolo OpenAI Compatible. Preencha o nome do modelo como `claude-opus-4-7`.

### Falha de autenticação

Copie novamente a API Key, confirme que não há espaços extras e verifique se a Key não foi excluída nem desativada.
