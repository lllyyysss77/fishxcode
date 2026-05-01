# Perguntas frequentes

## Geral

### O que e o FishXCode?

O FishXCode e uma estacao de retransmissao de AI Coding que suporta os modelos Claude e Codex em multiplas plataformas.

### Quais ferramentas sao suportadas?

Ferramentas disponiveis atualmente:

- **Claude Code** — CLI oficial da Anthropic
- **OpenAI Codex** — Assistente de programacao da OpenAI
- **RooCode** — Extensao de IA para VS Code
- **Qwen Code** — Ferramenta de programacao baseada no Qwen da Alibaba
- **Droid CLI** — CLI leve para programacao com IA
- **OpenCode** — Ferramenta terminal de codigo aberto para programacao com IA

### Como meus dados sao tratados?

O FishXCode funciona apenas como um servico de retransmissao de API. Seu codigo e suas conversas nao sao armazenados em nossos servidores. Todas as requisicoes sao encaminhadas diretamente aos provedores de modelos.

::: tip Dica
Para projetos sensiveis, recomendamos consultar a politica de privacidade de cada provedor de modelos antes de utilizar o servico.
:::

## Conta e Token

### Como me cadastro?

Acesse [fishxcode.com/register](https://fishxcode.com/register?aff=9CTW) e siga as instrucoes para criar sua conta.

### Como obtenho um token de API?

Apos fazer login, acesse a [pagina de gerenciamento de tokens](https://fishxcode.com/console/token) no console para gerar um novo token.

### Quanto tempo um token e valido?

Os tokens permanecem ativos ate serem excluidos ou regenerados manualmente. Recomendamos renova-los periodicamente por questoes de seguranca.

### Quais sao os limites da cota de uso?

Cada usuario recebe uma cota de uso. A quantidade exata e ajustada dinamicamente conforme os recursos da plataforma. Consulte o console para verificar seu consumo atual.

::: warning Atencao
Quando sua cota se esgotar, as requisicoes serao recusadas. Planeje seu uso com antecedencia.
:::

## Configuracao das ferramentas

### Minhas variaveis de ambiente nao estao funcionando

Verifique estes problemas comuns:

1. **Terminal nao foi reiniciado** — Apos editar `.bashrc` / `.zshrc`, execute `source ~/.bashrc` ou abra um novo terminal
2. **Erro de digitacao no nome da variavel** — Os nomes diferenciam maiusculas de minusculas: `ANTHROPIC_BASE_URL` nao e o mesmo que `Anthropic_Base_Url`
3. **Espacos ou aspas extras** — Certifique-se de nao incluir espacos desnecessarios em `export KEY="value"`

::: details Comandos de diagnostico rapido
```bash
# Verificar se as variaveis estao definidas
echo $ANTHROPIC_BASE_URL
echo $ANTHROPIC_AUTH_TOKEN

# Se a saida estiver vazia, a variavel nao esta configurada
```
:::

### Onde ficam os arquivos de configuracao?

| Ferramenta | Localizacao |
|------------|-------------|
| Claude Code | Variaveis de ambiente |
| Codex | `~/.codex/config.toml` e `~/.codex/auth.json` |
| RooCode | Configuracoes JSON do VS Code |
| Qwen Code | Variaveis de ambiente |

### Nao consigo me conectar ao FishXCode

1. Confirme que o `BASE_URL` esta configurado como `https://fishxcode.com/` (atencao para a `/` no final)
2. Verifique se `fishxcode.com` esta acessivel pela sua rede
3. Se voce estiver atras de um proxy corporativo, confira as configuracoes do proxy

## Selecao de modelo

### Como escolher o modelo certo?

Escolha de acordo com o seu caso de uso:

| Caso de uso | Modelo recomendado | Motivo |
|-------------|-------------------|--------|
| Programacao do dia a dia | `claude-sonnet-4-5-20250929` | Bom equilibrio entre capacidade e velocidade |
| Completacoes rapidas | `claude-3-5-haiku-20241022` | Tempo de resposta curto |
| Arquitetura complexa | `claude-sonnet-4-5-20250514` | Forte capacidade de raciocinio |

### Quais sao as diferencas entre os modelos?

- **Serie Sonnet** — Versutil, adequada para a maioria das tarefas de programacao
- **Serie Haiku** — Leve e rapida, ideal para completacoes simples e formatacao
- Para comparacoes detalhadas, consulte a documentacao oficial de cada provedor

### Como troco de modelo?

Defina a variavel de ambiente `ANTHROPIC_MODEL`:

```bash
export ANTHROPIC_MODEL=claude-sonnet-4-5-20250929
```

## Solucao de problemas

### Falha de autenticacao (Auth Error)

::: warning Causas comuns
- O token contem espacos extras ou esta incompleto
- O token foi excluido ou regenerado
- `API_KEY` e `AUTH_TOKEN` precisam estar configurados simultaneamente
:::

Solucao: Acesse o [console](https://fishxcode.com/console/token), verifique o status do seu token e copie-o novamente.

### Como ler os logs de erro?

Acesse [Console -> Logs de uso](https://fishxcode.com/console/log), selecione o tipo **Logs de erro** e filtre por periodo, modelo, token, grupo, request ID, mensagem de erro ou codigo de status.

::: tip Dicas de diagnostico
- Copie primeiro o `request_id` da resposta da API ou do log e pesquise exatamente essa requisicao
- Use o filtro de mensagem de erro para buscar palavras-chave em `content`, como `Invalid token` ou `Upstream request failed`
- Use o codigo de status para agrupar problemas rapidamente, como `401`, `429`, `502`, `503` ou `524`
:::

Para campos completos, classificacao de status e mensagens comuns, veja [Explicacao dos logs de erro](/pt/error-logs).

### Como ler o status de saude dos grupos?

O status de saude dos grupos ajuda a saber se o problema e uma falha isolada ou se esta concentrado em um plano, modelo ou grupo upstream. Veja primeiro `success_rate`, `error_count` e `error_reasons`, depois use o `request_id` do log individual para localizar a requisicao.

Para a pagina completa, campos e fluxo de diagnostico, veja [Status de saude dos grupos](/pt/group-health).

### Tempo limite esgotado (Timeout)

Possiveis causas:
1. Alta latencia de rede
2. Contexto de entrada muito grande exigindo mais tempo de processamento
3. Servico sob carga elevada

Tente reduzir o contexto de entrada ou aguarde alguns instantes antes de tentar novamente.

### Limite de requisicoes atingido (429)

Um codigo `429` significa que voce esta enviando requisicoes com muita frequencia.

::: tip Como resolver
- Diminua o ritmo e aguarde alguns segundos entre as requisicoes
- Evite chamar a API em loops sem intervalos
- Verifique se nenhum outro processo esta usando o mesmo token simultaneamente
:::

### Modelo nao disponivel

Verifique se o nome do modelo esta correto. Consulte a lista de modelos recomendados em [Comecar](/pt/start). Alguns modelos podem ainda nao estar disponiveis no FishXCode.
