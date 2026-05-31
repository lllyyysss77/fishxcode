# Tutorial ZCF

[ZCF](https://github.com/UfoMiao/zcf) é uma ferramenta de aprimoramento zero-configuração para Claude Code / Codex. Um único comando `zcf init` conclui toda a inicialização: configuração da API, importação de workflows e integração de serviços MCP.

> Sem conta? Primeiro complete a [Configuração de conta](/pt/account) para obter sua API Key.

---

## Parâmetros comuns

### Configuração da API

| Parâmetro | Descrição |
|---|---|
| `-p custom` | Usar provedor personalizado (método utilizado pelo FishXCode) |
| `-t api_key` | Equivalente a `-p custom`, mesmo efeito |
| `-k "sk-xxx"` | API Key |
| `-u "https://api.fishxcode.com/"` | URL base do FishXCode |
| `-M "claude-sonnet-4-5-20250929"` | Especificar modelo principal |
| `-H "claude-haiku-4-5-20251001"` | Especificar modelo rápido |

Exemplo especificando modelos:

```bash
npx zcf i -s -t api_key -k "sk-xxx" -u "https://api.fishxcode.com/" \
  -M "claude-sonnet-4-5-20250929" \
  -H "claude-haiku-4-5-20251001"
```

### Workflows

| Parâmetro | Descrição |
|---|---|
| `-w all` | Instalar todos os workflows |
| `-w sixStepsWorkflow,gitWorkflow` | Instalar workflows específicos |
| `-w skip` | Pular |

Workflows disponíveis: `commonTools` / `sixStepsWorkflow` / `featPlanUx` / `gitWorkflow` / `bmadWorkflow`

### Serviços MCP

| Parâmetro | Descrição |
|---|---|
| `-m all` | Instalar todos os serviços MCP |
| `-m context7,open-websearch` | Instalar serviços específicos |
| `-m skip` | Pular |

Serviços disponíveis: `context7` / `open-websearch` / `spec-workflow` / `mcp-deepwiki` / `Playwright` / `exa` / `serena`

### Outras opções

| Parâmetro | Descrição |
|---|---|
| `-g pt` | Definir idioma da interface, templates e saída da IA como português |
| `-s` | Modo não-interativo, pular todos os prompts |
| `-r backup` | Estratégia de configuração (`backup` / `merge` / `docs-only` / `skip`) |
| `-x false` | Não instalar a barra de status CCometixLine |

---

## Comandos de workflow

Após a instalação, os seguintes comandos de barra estão disponíveis no Claude Code:

| Comando | Descrição |
|---|---|
| `/zcf:workflow` | Workflow de desenvolvimento em seis fases (Pesquisa → Ideação → Planejamento → Execução → Otimização → Revisão) |
| `/zcf:feat` | Desenvolvimento de nova funcionalidade, com planejamento e design UI/UX |
| `/zcf:git-commit` | Geração automática de mensagens de commit Git |
| `/zcf:git-rollback` | Rollback interativo para versões anteriores |
| `/zcf:git-worktree` | Gerenciamento de Git Worktrees |
| `/zcf:bmad-init` | Processo ágil de desenvolvimento empresarial |

---

## Descrição dos serviços MCP

MCP (Model Context Protocol) permite que o Claude Code acesse ferramentas e serviços externos. O ZCF inclui os seguintes serviços:

| Serviço | Descrição | Requer Key |
|---|---|---|
| `context7` | Consulta documentação atualizada e exemplos de código de bibliotecas | Não |
| `open-websearch` | Pesquisa web via DuckDuckGo/Bing/Brave | Não |
| `spec-workflow` | Workflow estruturado de Requisitos → Design → Implementação | Não |
| `mcp-deepwiki` | Consulta de documentação de repositórios GitHub | Não |
| `Playwright` | Automação de navegador | Não |
| `exa` | Pesquisa semântica Exa AI | Sim (`EXA_API_KEY`) |
| `serena` | Assistente IDE, recuperação semântica de código | Não |

### Descrição de cada serviço

**context7** — Consulta documentação atualizada e exemplos de código de qualquer biblioteca, evitando que a IA use APIs obsoletas:
```
Por favor, consulte a documentação mais recente e exemplos do hook useState do React
```

**open-websearch** — Suporta pesquisa em múltiplos motores: DuckDuckGo, Bing, Brave. Não requer API Key:
```
Pesquise as novidades do TypeScript 5.0
```

**spec-workflow** — Workflow estruturado do requisito à implementação, incluindo análise de requisitos, design técnico e decomposição de tarefas:
```bash
npx -y @pimzino/spec-workflow-mcp@latest --dashboard  # Iniciar painel de visualização
```

**mcp-deepwiki** — Consulta documentação de repositórios GitHub:
```
Consulte a documentação da Composition API do repositório vuejs/core
```

**Playwright** — Controle do navegador para capturas de tela, preenchimento de formulários e simulação de interações. Na primeira execução, é necessário baixar o navegador.

**exa** — Pesquisa semântica na web via Exa AI, requer configuração de API Key:
```bash
export EXA_API_KEY="your-api-key"  # Obtenha em dashboard.exa.ai
```

**serena** — Recuperação semântica de código e sugestões de edição inteligentes, com capacidades similares a um IDE.

### Localização do arquivo de configuração

Após a instalação pelo ZCF, as configurações MCP são gravadas em `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@context-labs/context7"]
    },
    "open-websearch": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-open-websearch"]
    }
  }
}
```

::: tip Usuários Windows
O ZCF corrige automaticamente os formatos de caminhos do Windows. Se houver problemas de conexão com o MCP, execute `npx zcf` → selecione `4. Configurar MCP` para correção automática.
:::

---

## Atualização

```bash
npx zcf update
# ou
npx zcf u -s -g pt
```

A atualização sincroniza apenas os templates de workflow e prompts — **não modifica a configuração da API**.

---

## Perguntas frequentes

### O settings.json existente será sobrescrito?

O ZCF faz backup automático em `~/.claude/backup/YYYY-MM-DD_HH-mm-ss/` antes de qualquer modificação e oferece quatro estratégias de tratamento:

```bash
npx zcf i -s -r backup    # Backup e sobrescrever (padrão)
npx zcf i -s -r merge     # Mescla inteligente
npx zcf i -s -r docs-only # Atualizar apenas documentos de workflow
npx zcf i -s -r skip      # Pular sem modificar
```

### Como voltar para configuração manual?

O `settings.json` gerado pelo ZCF está em formato padrão e pode ser editado diretamente. Consulte a [Configuração do Claude Code](/pt/start).

### Requisito de versão do Node.js

ZCF requer Node.js >= 18:

```bash
node --version
```
