# Usar FishXCode com OpenClaw

::: info Introdução ao projeto
OpenClaw é uma plataforma de assistente de IA pessoal de código aberto e auto-hospedada que conecta aplicativos de mensagens a agentes de IA executados no seu próprio hardware. Projetada para desenvolvedores e usuários avançados que desejam assistentes de IA autônomos sem abrir mão do controle dos seus dados.

- Site oficial: [https://openclaw.ai](https://openclaw.ai)
- Documentação: [https://docs.openclaw.ai](https://docs.openclaw.ai)
- GitHub: [https://github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
:::

## Pré-requisitos

- OpenClaw instalado (consulte a seção de instalação abaixo)
- API Key do FishXCode ([Obter no console](https://fishxcode.com/console/token))

## Recursos principais

### Integração multicanal

- **Cobertura completa**: Suporta Lark (Feishu), Discord, Slack, Microsoft Teams e mais
- **Gateway único**: Gerencie todos os canais através de um único processo Gateway
- **Suporte de voz**: Interação por voz no macOS/iOS/Android
- **Interface Canvas**: Renderiza interfaces Canvas interativas

### Auto-hospedagem e segurança de dados

- **Totalmente auto-hospedado**: Funciona na sua própria máquina ou servidor
- **Código aberto**: Licença MIT, código totalmente transparente
- **Armazenamento local**: Contexto e habilidades armazenados localmente, não na nuvem

### Capacidades de agente inteligente

- **Sempre ativo**: Operação persistente em segundo plano com memória persistente
- **Tarefas agendadas**: Suporta tarefas cron
- **Isolamento de sessões**: Sessões isoladas por agente/espaço de trabalho/remetente
- **Roteamento multi-agente**: Colaboração entre múltiplos agentes
- **Chamada de ferramentas**: Suporte nativo a chamadas de ferramentas e execução de código

## Instalação

::: info Requisitos
- API Key do FishXCode
- Node.js 22+ necessário para os métodos npm/git; o one-liner curl gerencia as dependências automaticamente
:::

::: code-group

```bash [curl (Recomendado)]
curl -fsSL https://openclaw.ai/install.sh | bash
```

```bash [npm]
npm install -g openclaw@latest
```

```bash [curl (modo git)]
curl -fsSL https://openclaw.ai/install.sh | bash -s -- --install-method git
```

```bash [Clonar manualmente]
git clone https://github.com/openclaw/openclaw.git
cd openclaw && pnpm install && pnpm run build
pnpm run openclaw onboard
```

:::

Após a instalação, execute o assistente de configuração (já incluído nas etapas de clonagem manual):

```bash
openclaw onboard
```

## Configuração

### Localização do arquivo de configuração

O arquivo de configuração do OpenClaw está em `~/.openclaw/openclaw.json`:

- **macOS**: `/Users/seu-usuario/.openclaw/openclaw.json`
- **Linux**: `/home/seu-usuario/.openclaw/openclaw.json`
- **Windows**: `C:\Users\seu-usuario\.openclaw\openclaw.json`

Se o arquivo não existir, crie-o primeiro:

::: code-group

```bash [macOS / Linux]
mkdir -p ~/.openclaw
touch ~/.openclaw/openclaw.json
```

```powershell [Windows (PowerShell)]
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.openclaw"
New-Item -ItemType File -Force -Path "$env:USERPROFILE\.openclaw\openclaw.json"
```

:::

::: tip Dica
Se você já executou `openclaw onboard`, o arquivo de configuração será gerado automaticamente. Basta modificar o conteúdo existente.
:::

### Estrutura do arquivo de configuração

A estrutura do `openclaw.json` consiste em duas partes principais:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      // Configurar provedores de modelos de IA
    }
  },
  "agents": {
    "defaults": {
      // Configurar modelo padrão, diretório de trabalho, etc.
    }
  }
}
```

- `models.providers` — Define os provedores de serviços (URL, chave, lista de modelos)
- `models.mode` — Definir como `"merge"` para mesclar a configuração personalizada com os padrões integrados, **fortemente recomendado**
- `agents.defaults.model.primary` — Modelo a ser usado por padrão, formato: `nome-provedor/id-modelo`
- `api` — Tipo de protocolo API: `"anthropic-messages"` para modelos Anthropic, `"openai-responses"` para modelos compatíveis com OpenAI

### Métodos de configuração

#### Configurar modelos Anthropic (Claude)

Adicione o seguinte conteúdo ao `openclaw.json`:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-anthropic": {
        "baseUrl": "https://api.fishxcode.com",
        "apiKey": "sk-seu-token-fishxcode",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "claude-opus-4-6",
            "name": "Claude Opus 4.6",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 8192,
            "reasoning": false
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "fishxcode-anthropic/claude-opus-4-6"
      }
    }
  }
}
```

::: warning Importante
- Substitua `sk-seu-token-fishxcode` pelo seu token real obtido no [console FishXCode](https://fishxcode.com/console/token)
- **Para o protocolo Anthropic, o `baseUrl` NÃO deve incluir `/v1`** — o SDK adiciona o caminho automaticamente
:::

#### Configurar modelos OpenAI (GPT)

Ao chamar modelos OpenAI via FishXCode, o campo `api` deve ser `openai-responses`:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-openai": {
        "baseUrl": "https://api.fishxcode.com/v1",
        "apiKey": "sk-seu-token-fishxcode",
        "api": "openai-responses",
        "models": [
          {
            "id": "gpt-5",
            "name": "GPT-5",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 16384,
            "reasoning": true
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "fishxcode-openai/gpt-5"
      }
    }
  }
}
```

::: tip
**O protocolo OpenAI requer `/v1`**, ou seja, `https://api.fishxcode.com/v1`. Isso ocorre porque os dois SDKs têm lógicas de concatenação de caminho diferentes.
:::

#### Configurar Anthropic + OpenAI simultaneamente (Recomendado)

Adicione ambos os providers lado a lado em `models.providers` para usar modelos das duas famílias:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-anthropic": {
        "baseUrl": "https://api.fishxcode.com",
        "apiKey": "sk-seu-token-fishxcode",
        "api": "anthropic-messages",
        "models": [
          {
            "id": "claude-opus-4-6",
            "name": "Claude Opus 4.6",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 8192,
            "reasoning": false
          },
          {
            "id": "claude-sonnet-4-5-20250929",
            "name": "Claude Sonnet 4.5",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 8192,
            "reasoning": false
          }
        ]
      },
      "fishxcode-openai": {
        "baseUrl": "https://api.fishxcode.com/v1",
        "apiKey": "sk-seu-token-fishxcode",
        "api": "openai-responses",
        "models": [
          {
            "id": "gpt-5",
            "name": "GPT-5",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 16384,
            "reasoning": true
          },
          {
            "id": "gpt-5-codex",
            "name": "GPT-5 Codex",
            "input": ["text", "image"],
            "contextWindow": 200000,
            "maxTokens": 16384,
            "reasoning": true
          }
        ]
      }
    }
  },
  "agents": {
    "defaults": {
      "model": {
        "primary": "fishxcode-anthropic/claude-opus-4-6",
        "fallbacks": [
          "fishxcode-anthropic/claude-sonnet-4-5-20250929",
          "fishxcode-openai/gpt-5"
        ]
      }
    }
  }
}
```

### Campos chave de configuração

| Campo | Significado | Anthropic (Claude) | OpenAI (GPT) |
| --- | --- | --- | --- |
| `baseUrl` | Endereço do proxy API | `https://api.fishxcode.com` | `https://api.fishxcode.com/v1` |
| `apiKey` | Sua chave API | `sk-seu-token-fishxcode` | `sk-seu-token-fishxcode` |
| `api` | Tipo de protocolo API | `anthropic-messages` | `openai-responses` |
| `mode` | Modo de mesclagem de config | `merge` (recomendado) | `merge` (recomendado) |
| `models[].id` | ID do modelo | `claude-opus-4-6` | `gpt-5` |
| `model.primary` | Modelo padrão | `fishxcode-anthropic/claude-opus-4-6` | `fishxcode-openai/gpt-5` |
| `reasoning` | Ativar modo raciocínio | `false` (depende do modelo) | `true` (GPT-5.x suportado) |

## Verificar a configuração

Execute o seguinte comando para confirmar que a configuração está funcionando:

```bash
openclaw
```

Verificar a lista de modelos:

```bash
openclaw models list
```

Verificar o status dos modelos e autenticação:

```bash
openclaw models status
```

Diagnóstico completo:

```bash
openclaw doctor
```

## Iniciar o serviço

Após a configuração, inicie o OpenClaw:

```bash
openclaw start
```

Uma vez iniciado, você pode interagir com o assistente de IA através dos canais configurados.

Reiniciar o Gateway:

```bash
openclaw gateway restart
```

## Solução de problemas

### 403 Bloqueado

**Sintoma**: O provider está configurado, a requisição curl direta à API retorna 200, mas as requisições do OpenClaw recebem um 403 "Your request was blocked".

**Causa**: O OpenClaw usa `@anthropic-ai/sdk` internamente, que envia requisições com o User-Agent oficial do SDK (ex: `Anthropic/JS 0.73.0`). Alguns CDNs ou WAFs bloqueiam esse UA.

**Solução**: Adicione um campo `headers` na configuração do provider para substituir o UA:

```json
{
  "fishxcode-anthropic": {
    "baseUrl": "https://api.fishxcode.com",
    "apiKey": "sua-api-key",
    "api": "anthropic-messages",
    "headers": {
      "User-Agent": "Mozilla/5.0"
    },
    "models": [...]
  }
}
```

### Não incluir /v1 no baseUrl (Protocolo Anthropic)

**Sintoma**: A requisição retorna 404, e os logs mostram que o caminho se tornou `/v1/v1/messages`.

**Causa**: O SDK da Anthropic adiciona automaticamente `/v1/messages` ao baseURL. Se seu baseUrl já inclui `/v1`, o caminho real fica duplicado.

**Solução**: Para o protocolo Anthropic, escreva apenas o domínio no baseUrl, sem `/v1`:

```json
{
  "baseUrl": "https://api.fishxcode.com"
}
```

::: tip
O protocolo OpenAI requer `/v1`, ou seja, `https://api.fishxcode.com/v1`. Isso ocorre porque os dois SDKs têm lógicas de concatenação de caminho diferentes.
:::

### O campo api aceita apenas três valores

**Sintoma**: Na inicialização aparece "Config invalid", ou o provider configurado não aparece na lista de modelos.

**Causa**: O OpenClaw valida estritamente o campo `api`, aceitando apenas estes três valores:

| Valor | Protocolo |
| --- | --- |
| `anthropic-messages` | Anthropic Messages API |
| `openai-completions` | OpenAI Chat Completions |
| `openai-responses` | OpenAI Responses API |

Valores como `openai-chat`, `openai`, `anthropic`, etc. causarão erros.

**Solução**: Ao usar FishXCode, use `anthropic-messages` para modelos Claude e `openai-responses` para modelos GPT.

### Resposta vazia com openai-completions (Não usar para modelos GPT)

**Sintoma**: `api` está configurado como `openai-completions`, a requisição é bem-sucedida (`isError=false` nos logs), mas a interface exibe uma mensagem vazia.

**Causa**: O OpenClaw processa internamente os fluxos de mensagens no formato Anthropic. As respostas no formato OpenAI de `openai-completions` podem não ser mapeadas corretamente em alguns casos.

**Solução**: Ao chamar modelos GPT via FishXCode, use `openai-responses` em vez de `openai-completions`.

### Alterações de configuração não têm efeito

**Sintoma**: `openclaw.json` foi modificado, mas o OpenClaw ainda usa a configuração antiga.

**Causa**: O OpenClaw tem dois lugares onde as configurações de providers precisam estar sincronizadas:

```
~/.openclaw/openclaw.json              → models.providers
~/.openclaw/agents/main/agent/models.json → providers
```

Modificar apenas um pode causar problemas.

**Solução**: Após a modificação, confirme com:

```bash
openclaw models status
```

Ou reinicie o Gateway do OpenClaw:

```bash
openclaw gateway restart
```

### Erros de formato JSON

Erros comuns de formato JSON:

- **Vírgula extra**: O último elemento não pode ter vírgula final
- **Vírgula faltando**: Dois pares chave-valor adjacentes devem ser separados por vírgula
- **Problemas com aspas**: JSON aceita apenas aspas duplas inglesas `"`, não aspas simples ou especiais
- **Chaves sem par**: Cada `{` deve ter um `}` correspondente, cada `[` deve ter um `]` correspondente

Valide o formato com:

```bash
python3 -m json.tool ~/.openclaw/openclaw.json
```

### Referência de comandos de diagnóstico

| Comando | Propósito |
| --- | --- |
| `openclaw models status` | Ver status dos modelos e autenticação |
| `openclaw models list` | Ver lista de modelos configurados |
| `openclaw doctor` | Diagnóstico completo |
| `openclaw gateway restart` | Reiniciar o Gateway |

::: tip Estratégia de depuração
Primeiro use curl para confirmar que a API do FishXCode está funcionando normalmente, depois verifique o que é diferente nas requisições enviadas pelo OpenClaw (UA, caminho, formato).
:::
