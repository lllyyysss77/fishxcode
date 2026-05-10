---
title: Status de saude dos grupos
description: Status de saude dos grupos FishXCode, detalhes de tokens de equipe, colunas de exportacao e fluxo de diagnostico.
---

# Status de saude dos grupos

O status de saude dos grupos ajuda a saber se uma falha de chamada e um problema isolado de requisicao ou um problema concentrado em um plano, modelo, grupo upstream ou membro da equipe. Administradores de empresa e equipe podem usa-lo para responder rapidamente a tres perguntas:

- Qual grupo teve queda na taxa de sucesso no periodo selecionado
- Qual usuario ou token concentrou mais requisicoes, consumo ou erros
- Se o erro esta concentrado em um unico Token ou ja afeta todo o grupo

Ao diagnosticar falhas de API, veja primeiro o status do grupo e depois use os logs de uso para localizar o `request_id` especifico.

::: info Escopo dos dados
A pagina publica `status` incorporada aqui consulta o status de saude dos grupos usados por todos os usuarios FishXCode durante o periodo selecionado. Ela reflete a disponibilidade global dos grupos da plataforma e e em tempo real, imparcial e estavel.

A visao **Logs de uso -> Status de saude dos grupos** dentro do console mostra os dados visiveis conforme as permissoes da conta atual. Usuarios pessoais normalmente veem apenas seus proprios tokens; administradores de empresa e equipe podem analisar uso por usuario, nome de usuario, token e grupo.
:::

<iframe
  src="https://status.fishxcode.com/group/global?view=list&sort=group&window=24h"
  title="Status de saude dos grupos FishXCode"
  loading="lazy"
  referrerpolicy="no-referrer-when-downgrade"
  style="width: 100%; height: 720px; border: 1px solid var(--vp-c-divider); border-radius: 8px; background: var(--vp-c-bg-soft);"
></iframe>

Se a pagina de status acima nao carregar, abra diretamente o [status de saude dos grupos FishXCode](https://status.fishxcode.com/group/global?view=list&sort=group&window=24h).

Entrada no console: [Console -> Logs de uso](https://fishxcode.com/console/log). Em logs de erro ou na visao estatistica, filtre por periodo, modelo, Token, grupo, mensagem de erro e codigo de status.

## Exemplo no console

A imagem abaixo e um exemplo de **Logs de uso -> Status de saude dos grupos** no console. Ela mostra taxa de sucesso, numero de requisicoes, consumo, cache, tempo medio, requisicao mais recente e motivos de falha.

![Exemplo de status de saude dos grupos no console](/img/group-health.png)

::: tip Principio de uso
Primeiro determine o alcance do impacto, depois trate o erro individual. Logs individuais servem para localizar a requisicao; status de grupo serve para saber se o problema esta concentrado.
:::

Para interpretar uma mensagem de erro individual, veja [Explicacao dos logs de erro](/pt/error-logs).

## Colunas da lista

A lista do console e a exportacao CSV usam as mesmas colunas exibidas. A lista contem dois tipos de linha:

- **Linha de grupo**: resume a saude geral de um grupo no periodo selecionado.
- **Linha de token**: mostra detalhes de usuario e token dentro de um grupo, ajudando administradores de empresa e equipe a localizar membros, projetos ou servicos.

| Coluna exibida | Aplica-se a | Descricao | Como usar |
|----------------|-------------|-----------|-----------|
| Tipo | Linha de grupo, linha de token | Indica se a linha e resumo `Grupo` ou detalhe `Token` | Veja primeiro as linhas de grupo, depois as linhas de token para localizar membro ou Token |
| Grupo | Linha de grupo, linha de token | Grupos que apareceram no periodo selecionado, incluindo grupos por uso, grupos de plano, grupo padrao ou grupo de modelo | Saber se o problema esta concentrado em um plano, modelo ou pool upstream |
| ID do usuario | Linha de token | ID do usuario que usou o token | Localizar a conta do membro em diagnosticos empresariais |
| Nome de usuario | Linha de token | Nome do usuario que usou o token | Relatorios de equipe, contato com membro e verificacao de permissoes |
| Token | Linha de token | Nome do Token configurado no console | Saber se a anomalia esta isolada a um Token |
| Taxa de sucesso | Linha de grupo, linha de token | Taxa de sucesso = requisicoes bem-sucedidas / total de requisicoes | Abaixo de 80% merece atencao; se estiver claramente menor que linhas semelhantes, investigue primeiro esse grupo ou token |
| Requisicoes | Linha de grupo, linha de token | Total de requisicoes no periodo selecionado | Evite interpretar demais a taxa de sucesso quando a amostra for pequena |
| Sucesso | Linha de grupo, linha de token | Requisicoes bem-sucedidas que retornaram 2xx | Leia junto com Requisicoes e Erros para avaliar disponibilidade |
| Erros | Linha de grupo, linha de token | Requisicoes com erro (4xx/5xx) | Se subir, veja primeiro Motivo da falha e logs de erro |
| Consumo | Linha de grupo, linha de token | Consumo acumulado de cota/custo no periodo, exportado no formato monetario do console | Contabilidade da equipe, rateio por projeto e deteccao de consumo anormal |
| Taxa de cache | Linha de grupo, linha de token | Taxa de cache = tokens com hit de cache / total de tokens | Quanto mais alta, mais economia; a parte em cache costuma ser cobrada mais barato ou de graca |
| Tokens em cache | Linha de grupo, linha de token | Numero de tokens que atingiram cache no periodo | Essa parte costuma ser cobrada com alto desconto; quanto mais, maior a economia |
| Requisicoes em cache | Linha de grupo, linha de token | Numero de requisicoes que atingiram cache pelo menos uma vez | Mede quantas requisicoes realmente usaram cache |
| Proporcao de requisicoes em cache | Linha de grupo, linha de token | Proporcao de requisicoes em cache = requisicoes com cache / requisicoes totais | Quanto mais alta, mais chamadas recebem desconto de cache |
| Media de tokens em cache | Linha de grupo, linha de token | Media de tokens por hit de cache | Comparar eficiencia de reutilizacao entre membros, servicos ou grupos |
| Tempo medio | Linha de grupo, linha de token | Tempo medio por requisicao, em segundos | Quanto menor, mais rapido o upstream responde; se subir, investigar contexto longo, saida longa e ferramentas |
| Hora de inicio | Linha de grupo, linha de token | Primeira aparicao desse grupo ou token no periodo atual | Localizar inicio do problema ou do trafego |
| Ultima requisicao | Linha de grupo, linha de token | Aparicao mais recente desse grupo ou token no periodo atual | Saber se o problema ou trafego continua |
| Motivo da falha | Linha de grupo | Principais motivos de falha por frequencia, com codigo de status e contagem; vazio ou `-` quando nao ha erros | Priorizar o erro com mais ocorrencias, nao apenas o ultimo log |

::: info Fonte dos campos
As colunas exibidas sao geradas a partir de estatisticas agregadas. Para uso diario, siga a lista do console e as colunas do CSV; faca o mapeamento para campos brutos apenas em integracoes de API ou diagnostico tecnico.
:::

::: tip Diagnostico de equipe
Veja primeiro as linhas de grupo para decidir se e problema do pool de recursos, depois as linhas de token para saber se um usuario ou token causou o problema. Se a taxa de sucesso do grupo estiver normal mas um token tiver muitos erros, verifique primeiro o Token, nome do modelo, configuracao do cliente ou corpo da requisicao desse membro.
:::

## Exportacao CSV

A exportacao CSV usa as mesmas colunas da lista atual. Ela e adequada para relatorios semanais, rateio de custos, revisoes de incidentes e conciliacao de uso por membro.

Depois de exportar, voce pode previsualizar o arquivo com o [visualizador CSV online](https://tools.beer/zh/csv/viewer/). Ele permite arrastar ou selecionar um arquivo CSV, e tambem colar texto CSV, util para verificar rapidamente colunas e motivos de falha.

| Comportamento da exportacao | Descricao |
|-----------------------------|-----------|
| Linha de grupo | `Tipo` e `Grupo`; ID do usuario, nome de usuario e Token normalmente ficam vazios, representando o resumo do grupo |
| Linha de token | `Tipo` e `Token`; mostra ID do usuario, nome de usuario e Token, representando detalhe de membro ou Token dentro do grupo |
| Formato monetario | `Consumo` usa o formato monetario do console, por exemplo `¥905.48` |
| Formato percentual | Taxa de sucesso, taxa de cache e proporcao de requisicoes em cache saem como percentuais |
| Formato numerico | Numeros grandes podem incluir separadores de milhares, uteis para leitura direta ou importacao em planilhas |
| Formato de tempo | Hora de inicio e Ultima requisicao saem como horario local para alinhar com o incidente |
| Motivo da falha | Varios erros frequentes sao combinados com contagem no final; vazio ou `-` quando nao ha erros |

## Fluxo de diagnostico

### 1. Determine o alcance do impacto

Veja primeiro as linhas em que `Tipo=Grupo`. Se a Taxa de sucesso esta perto do normal e Erros e baixo, geralmente e uma falha ocasional; copie o `request_id` da requisicao individual para continuar.

Se a Taxa de sucesso de um grupo esta claramente abaixo dos outros, ou Erros esta concentrado, investigue primeiro modelo, Token, conta upstream, permissao do plano e recursos da plataforma por grupo.

Em cenarios de empresa ou equipe, veja depois as linhas `Tipo=Token` dentro desse grupo. Se apenas um usuario ou token estiver anormal, verifique primeiro configuracao do cliente, Token, nome do modelo, corpo da requisicao e estrategia de concorrencia desse membro.

### 2. Veja os principais motivos de falha

Motivo da falha normalmente aparece ordenado por ocorrencias. Trate primeiro o erro mais frequente e depois os de baixa frequencia. Erros frequentes representam o principal tipo de falha no periodo atual.

| Tipo de erro | Palavras-chave comuns | Atribuicao inicial | Julgamento prioritario |
|--------------|-----------------------|--------------------|------------------------|
| Limite de frequencia | `Account RPM limit exceeded`, `Max 10/min`, `Max 5/min` | Problema de uso ou limite upstream | Concorrencia ou requisicoes por minuto altas demais |
| Limite diario | `Account daily limit exceeded` | Limite upstream | Cota diaria da conta upstream esgotada |
| Credenciais em cooldown | `All credentials ... are cooling down` | Limite upstream | Credenciais upstream do modelo estao em cooldown |
| Corpo grande demais | `status_code=413`, `openai_error` | Problema de uso | Contexto, arquivo, imagem ou resultado de ferramenta grande demais |
| Permissao ou autenticacao | `401`, `403`, `Invalid API key`, `pending admin approval` | Problema de uso ou estado da conta | Token, plano, grupo ou permissao do modelo anormal |
| Sem recurso disponivel | `No available accounts`, `No available channel`, `auth_unavailable` | Problema da plataforma ou configuracao | Grupo atual sem conta, canal ou recurso de auth disponivel |
| Erro upstream | `502`, `all upstreams failed`, `Upstream request failed` | Problema upstream | Servico upstream ou rede intermediaria anormal |
| Timeout de gateway | `504`, `521`, `522`, `524` | Problema upstream ou de rede | Conexao, leitura ou resposta upstream excedeu timeout |
| Protecao de recurso da plataforma | `system disk overloaded`, `Service Unavailable` | Problema da plataforma | No da plataforma ou recurso upstream temporariamente indisponivel |
| Formato de API de imagem | `gpt-image-2`, `prompt is required`, `multipart form` | Problema de uso | Endpoint de imagem, prompt ou formato de upload errado |
| Formato de tool call | `tool_use`, `tool_result`, `Invalid schema` | Problema de uso | Mensagens de ferramenta ou JSON Schema do cliente invalidos |

### 3. Trate pelo alcance do impacto

| Sintoma | Causa mais provavel | Acao sugerida |
|---------|---------------------|---------------|
| Apenas um Token falha | Configuracao do Token, permissao ou formato local da requisicao | Copiar o Token novamente, verificar configuracao do cliente e corpo da requisicao |
| Apenas um modelo falha | Permissao do modelo, canal do modelo ou recurso upstream do modelo | Trocar para modelo similar e confirmar se o plano atual suporta esse modelo |
| Apenas um grupo tem taxa baixa | Pool do grupo, permissao do plano ou conta upstream | Trocar grupo/modelo; ao contatar suporte, informar grupo e periodo |
| Varios grupos mostram `502`, `504`, `521`, `522`, `524` | Upstream ou rede intermediaria anormal | Tentar mais tarde e reduzir tarefas longas; se persistir, contatar suporte |
| Varias requisicoes mostram `413` | Corpo grande demais | Reduzir contexto, dividir arquivos, comprimir imagens ou reduzir resultados de ferramentas |
| Varias requisicoes mostram `429` | Frequencia alta demais, cota diaria esgotada ou credenciais em cooldown | Reduzir concorrencia; diferenciar RPM, daily limit e cooling down pelo log |

### 4. Combine consumo e cache

| Sintoma | Causa mais provavel | Acao sugerida |
|---------|---------------------|---------------|
| Consumo claramente maior que outros tokens do mesmo grupo | Contexto grande, saida longa, chamadas frequentes ou tarefas repetidas | Combinar Requisicoes, Tempo medio e logs de erro para localizar servico ou membro |
| Taxa de cache alta mas Proporcao de requisicoes em cache baixa | Poucas requisicoes grandes atingem cache | Verificar se apenas tarefas fixas reutilizam contexto |
| Proporcao de requisicoes em cache alta mas Media de tokens em cache baixa | Muitas requisicoes atingem cache, mas cada uma economiza pouco | Verificar se o contexto e curto demais ou se o conteudo cache e instavel |
| Um token tem Tempo medio claramente alto | Tarefas pesadas no cliente, contexto longo, saida longa ou upstream lento | Comparar Requisicoes, cache, Motivo da falha e logs individuais desse token |

## Informacoes para suporte

Para problemas simples, veja primeiro [Explicacao dos logs de erro](/pt/error-logs) e [Status de saude dos grupos](/pt/group-health). Se o problema continuar, abra os detalhes do log de erro em `console/log` e clique no icone de copiar para copiar os detalhes de diagnostico em um clique. Ao contatar suporte, envie as informacoes abaixo de uma vez para a equipe tecnica investigar com menos ida e volta:

- ID do usuario
- Periodo: quando o problema comecou e ultimo horario em que apareceu
- Nome do grupo: `group`
- Nome do modelo usado pela requisicao
- Codigo de status, como `429`, `413`, `502`, `503`
- Conteudo do erro: `error_reasons.content`
- Request ID: `request_id` do log individual ou da resposta da API
- Alcance: um Token, um modelo, um grupo ou varios grupos ao mesmo tempo

::: tip Conclusao rapida
`401` / `403` normalmente pede verificacao de permissao, `413` pede corpo menor, `429` pede frequencia e cota, `502` / `504` / `524` pede upstream e tarefas longas, `503` pede verificar recurso temporariamente indisponivel.
:::
