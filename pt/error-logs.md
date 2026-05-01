---
title: Explicacao dos logs de erro
description: Campos dos logs de erro no console FishXCode, significado dos codigos de status, erros comuns e fluxo de diagnostico.
---

# Explicacao dos logs de erro

Os logs de erro ajudam a localizar a causa exata de uma requisicao que falhou. Ao investigar, veja primeiro o [status de saude dos grupos](/pt/group-health) para entender o alcance do problema, depois use o `request_id` no log de erro para localizar a requisicao especifica.

Entrada: [Console -> Logs de uso](https://fishxcode.com/console/log). Depois de selecionar **Logs de erro**, voce pode filtrar por periodo, modelo, Token, grupo, request ID, mensagem de erro e codigo de status.

::: tip Julgamento rapido
- `request_id` localiza uma requisicao especifica
- `status_code` agrupa por tipo de erro
- `content` permite buscar palavras-chave do erro
- `group` ajuda a saber se o problema esta concentrado em um grupo
:::

## Campos

| Campo | Significado | Valor para diagnostico |
|-------|-------------|------------------------|
| Tempo | Horario em que a requisicao falhou | Saber se o problema continua ocorrendo |
| Modelo | Nome do modelo usado pela requisicao | Verificar se afeta apenas um modelo |
| Token | Nome da API Key ou Token usado | Verificar se afeta apenas um Token |
| Grupo | Grupo de plano, modelo ou recurso upstream da requisicao | Verificar se a falha esta concentrada em um grupo |
| Request ID | Identificador unico da requisicao, normalmente `request_id` | Informacao prioritaria ao contatar suporte |
| Mensagem de erro | Texto da falha, normalmente em `content` | Buscar palavras-chave do erro |
| Codigo de status | Codigo HTTP ou upstream, como `401`, `429`, `502` | Classificar rapidamente o erro |

## Ordem de diagnostico

1. Copie o `request_id` da resposta da API ou do log e pesquise exatamente essa requisicao.
2. Se nao houver `request_id`, reduza o escopo por periodo, modelo e Token.
3. Agrupe por codigo de status, como `401`, `413`, `429`, `502`, `503`.
4. Pesquise palavras-chave na mensagem de erro, como `Invalid API key`, `daily limit exceeded`, `Upstream request failed`.
5. Se o mesmo erro estiver concentrado no mesmo grupo, veja o [status de saude dos grupos](/pt/group-health) para confirmar o alcance.

## Atribuicao do problema

Os logs de erro ajudam a separar problemas de uso, problemas upstream e problemas da plataforma. Nao use apenas o codigo de status; combine com o alcance do impacto.

| Atribuicao | Criterio | Sinais comuns | Acao prioritaria |
|------------|----------|---------------|------------------|
| Problema de uso | Afeta apenas uma requisicao, um Token ou uma configuracao de cliente | Corpo da requisicao invalido, parametro incompativel, Token copiado errado, contexto grande demais, endpoint errado | Verificar configuracao local, corpo da requisicao, nome do modelo, Token e versao da ferramenta |
| Problema upstream | Varias requisicoes com falha upstream, timeout de gateway ou limite upstream | `Upstream request failed`, `all upstreams failed`, `bad response status code 502/524`, limite de conta upstream | Tentar mais tarde, reduzir concorrencia e trocar modelo ou grupo se necessario |
| Problema da plataforma | No, pool de recursos ou canal da plataforma indisponivel | `system disk overloaded`, `No available accounts`, `No available channel`, `Service Unavailable` do lado da plataforma | Tentar mais tarde; se persistir, contatar suporte com grupo, periodo e `request_id` |
| Precisa combinar com grupo | Uma linha de log nao basta para atribuir responsabilidade | `429`, `502`, `503` podem ter varias causas | Ver o [status de saude dos grupos](/pt/group-health) e confirmar se esta concentrado |

::: warning Atencao
Nao atribua responsabilidade apenas por uma ocorrencia de `502` ou `503`. Uma falha isolada pode ser oscilacao upstream; muitas falhas no mesmo grupo e periodo indicam melhor um problema upstream ou de recurso da plataforma.
:::

## Codigos de status

| Codigo | Significado comum | Atribuicao inicial | Acao prioritaria |
|--------|-------------------|--------------------|------------------|
| `400` | Corpo, parametros ou mensagens de ferramenta invalidos | Problema de uso | Verificar corpo da requisicao, endpoint e schema da ferramenta |
| `401` | Token invalido, desativado ou falha de autenticacao | Problema de uso | Copiar o Token novamente e confirmar seu status |
| `403` | Upstream recusou acesso, comum em permissao ou limite de modelo | Problema de uso ou upstream | Verificar plano, grupo e permissao do modelo |
| `413` | Corpo da requisicao grande demais | Problema de uso | Reduzir contexto, dividir arquivos, comprimir imagens |
| `429` | Limite de frequencia, limite diario ou credenciais em cooldown | Precisa combinar com grupo | Reduzir concorrencia e aguardar cota ou cooldown recuperar |
| `500` | Erro interno encapsulado pela camada de proxy ou upstream | Depende da mensagem | Interpretar pela mensagem real do erro |
| `502` | Servico upstream ou rede intermediaria anormal | Problema upstream ou de rede | Tentar mais tarde, trocar modelo/grupo se necessario |
| `503` | Servico, canal, conta ou recurso temporariamente indisponivel | Problema upstream ou da plataforma | Tentar mais tarde; se persistir, contatar suporte |
| `504` / `521` / `522` / `524` | Timeout de conexao, leitura ou resposta no gateway | Problema upstream ou de rede | Reduzir tarefas longas e tentar mais tarde |

## Mensagens de erro comuns

| Conteudo do log | Significado | Atribuicao inicial | Acao sugerida |
|-----------------|-------------|--------------------|---------------|
| `status_code=401, Invalid token` | Token invalido, copiado errado ou expirado | Problema de uso | Copiar novamente o Token do console e remover espacos extras |
| `status_code=401, Invalid API key or key is pending admin approval` | API Key invalida ou Key nova aguardando aprovacao/ativacao | Problema de uso ou estado da conta | Confirmar que usa o Token mais recente do console; se acabou de criar ou trocar plano, aguardar ativacao e tentar novamente |
| `status_code=403, bad response status code 403` | Upstream recusou a requisicao atual, comum em permissao, conta ou modelo | Problema de uso ou upstream | Verificar Token, grupo do plano e permissao do modelo; se persistir, trocar modelo ou contatar suporte com `request_id` |
| `status_code=413, openai_error` / `bad response status code 413` | Corpo grande demais, comum por contexto, arquivo, imagem ou resultado de ferramenta | Problema de uso | Reduzir contexto, diminuir uploads, dividir arquivos ou comprimir imagens |
| `status_code=429, Account RPM limit exceeded` | Conta upstream atingiu limite de requisicoes por minuto | Problema de uso ou limite upstream | Reduzir concorrencia e frequencia de retry, depois tentar novamente |
| `status_code=429, Account daily limit exceeded` | Conta upstream atingiu limite diario | Limite upstream | Aguardar reset diario ou trocar para outro modelo/grupo disponivel |
| `status_code=429, All credentials for model ... are cooling down` | Todas as credenciais upstream do modelo estao em cooldown | Limite upstream | Reduzir retries, aguardar cooldown; em tarefa urgente, trocar modelo temporariamente |
| `status_code=500, 请求失败 [429]: {"message":"Too many requests, please wait before trying again."}` | Upstream retornou `429`, mas a camada de proxy encapsulou como `500` | Problema de uso ou limite upstream | Tratar como rate limit: reduzir concorrencia, aumentar intervalo de retry e evitar retries imediatos |
| `status_code=500, auth_unavailable: no auth available` | Modelo ou grupo esta temporariamente sem recurso de autenticacao upstream | Problema da plataforma | Tentar mais tarde; se persistir, trocar modelo/grupo ou contatar suporte |
| `status_code=502, Upstream request failed` / `bad response status code 502` / `all upstreams failed` | Servico upstream ou rede intermediaria retornou erro, ou todos os upstreams falharam | Problema upstream | Tentar mais tarde; se persistir, trocar modelo ou contatar suporte com `request_id` |
| `status_code=502, openai_error` | Upstream compativel com OpenAI retornou erro sem motivo mais especifico | Problema upstream | Tratar como erro upstream; se reproduzir sempre, reduzir contexto e enviar `request_id` ao suporte |
| `status_code=502, The origin web server returned an invalid or incomplete response to Cloudflare` | Servidor de origem upstream retornou resposta invalida via Cloudflare | Problema upstream | Geralmente e falha temporaria upstream; tente mais tarde |
| `status_code=500, upstream error: do request failed` | Falha ao enviar a requisicao ao upstream, comum por rede ou indisponibilidade temporaria | Problema upstream ou de rede | Tentar mais tarde; se persistir, enviar `request_id` ao suporte |
| `status_code=520, bad response status code 520` | Cloudflare retornou erro desconhecido, normalmente por resposta upstream anormal ou conexao interrompida | Problema upstream ou de rede | Tentar mais tarde; se concentrado, tratar como falha upstream |
| `status_code=521` / `522` / `504` / `524` | Timeout ou falha de conexao/leitura/resposta no Cloudflare ou gateway upstream | Problema upstream ou de rede | Tentar mais tarde; se ocorrer em tarefas longas, reduzir contexto, saida ou cadeia de ferramentas |
| `status_code=503, Service Unavailable` | Servico upstream indisponivel ou grupo sem recurso disponivel | Precisa combinar com grupo | Tentar mais tarde; se persistir, trocar modelo/grupo ou contatar suporte |
| `status_code=503, system disk overloaded` | Disco do no de servico esta alto e a plataforma recusou temporariamente para proteger o servico | Problema da plataforma | Tentar mais tarde; se persistir, contatar suporte |
| `status_code=503, No available accounts: no available accounts` | Grupo atual nao tem conta upstream disponivel | Problema da plataforma | Trocar modelo/grupo ou tentar mais tarde; se persistir, contatar suporte |
| `status_code=503, No available channel for model ... under group ...` | O grupo atual nao tem canal disponivel para esse modelo | Problema de uso ou da plataforma | Verificar nome do modelo e suporte do plano; trocar para modelo suportado pelo grupo |
| `status_code=503, model gpt-image-2 is only supported on /v1/images/generations and /v1/images/edits` | Modelo de imagem usado no endpoint errado | Problema de uso | Enviar requisicoes de imagem para o endpoint images correspondente |
| `status_code=500, Image source is a local path that is not readable from this server` | A requisicao contem caminho local de imagem que o servidor nao consegue ler | Problema de uso | Em frontend, verificar arquivos `lock`, remover lock relacionado ou campos `png` anormais e reabrir a sessao; se precisar enviar imagem, use URL `http(s)` publica ou `data:image/...` base64 |
| `status_code=500, failed to parse multipart form` | Corpo de upload de imagem ou arquivo nao segue o formato do endpoint | Problema de uso | Verificar campos `multipart/form-data`, campo do arquivo e headers; nao escrever boundary incorreto manualmente |
| `status_code=400, Invalid request: prompt is required` / `解析 Images 请求失败: prompt 不能为空` | Requisicao de geracao/edicao de imagem sem `prompt` | Problema de uso | Informar `prompt` nao vazio e confirmar que usa endpoint de imagem |
| `status_code=400, Unsupported parameter: messages` | Endpoint e formato de parametros nao combinam, comum ao enviar parametros de Chat/Responses para endpoint que nao aceita `messages` | Problema de uso | Verificar `base_url`, endpoint e tipo de modelo da ferramenta; ajustar corpo para o endpoint correto |
| `tool_use ids were found without tool_result blocks immediately after` | Sequencia de mensagens de tool call nao cumpre o protocolo Claude | Problema de uso | Manter cada `tool_use` seguido do `tool_result` correspondente; se gerado pela ferramenta, atualizar cliente ou abrir nova sessao |
| `Invalid schema for function ... None is not of type 'array'` | Schema da funcao de ferramenta invalido, normalmente `parameters` ou campo array vazio/tipo incorreto | Problema de uso | Verificar definicao MCP/ferramenta, garantir arrays como `[]` e schema JSON valido |
| `status_code=500, not implemented` | Endpoint, capacidade do modelo ou caminho de tool call ainda nao implementado | Problema de uso ou da plataforma | Confirmar que usa endpoint e modelo suportados; se necessario, trocar modelo ou contatar suporte |

## Tratamento por tipo

### Autenticacao e permissao

Olhe principalmente `401`, `403`, `Invalid API key`, `pending admin approval`, `No available channel`. Confirme primeiro se o Token veio do console, se foi copiado completo e se tem permissao para o modelo e grupo atuais.

### Formato da requisicao

Olhe principalmente `400`, `413`, `Unsupported parameter`, `prompt is required`, `Invalid schema`. Verifique endpoint, campos do corpo, schema da ferramenta, parametros de imagem e tamanho do contexto.

### Limite e cota

Olhe principalmente `429`, `Max 10/min`, `daily limit exceeded`, `cooling down`. Nao faca retries sem intervalo; reduza a concorrencia conforme o limite no log, ou aguarde cota/credenciais recuperarem.

### Upstream e recursos da plataforma

Olhe principalmente `502`, `503`, `504`, `520`, `521`, `522`, `524`, `Service Unavailable`, `system disk overloaded`. Tente mais tarde; se estiver concentrado no mesmo grupo, veja o [status de saude dos grupos](/pt/group-health).

## Informacoes para suporte

Na lista `console/log`, voce pode copiar com um clique os detalhes de diagnostico a partir da mensagem de erro. Ao contatar suporte, envie as informacoes abaixo de uma vez para a equipe tecnica investigar com menos ida e volta:

- ID do usuario
- Periodo: quando o problema comecou e ultimo horario em que apareceu
- Nome do grupo: `group`
- Nome do modelo usado pela requisicao
- Codigo de status, como `429`, `413`, `502`, `503`
- Conteudo do erro: `error_reasons.content`
- Request ID: `request_id` do log individual ou da resposta da API
- Alcance: um Token, um modelo, um grupo ou varios grupos ao mesmo tempo

::: tip Conclusao
Logs de erro explicam "por que esta requisicao falhou"; status de saude dos grupos mostra "se o problema esta concentrado". Usar os dois juntos acelera o diagnostico.
:::
