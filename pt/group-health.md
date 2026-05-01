---
title: Status de saude dos grupos
description: Status de saude dos grupos FishXCode, campos, classificacao de erros e fluxo de diagnostico.
---

# Status de saude dos grupos

O status de saude dos grupos ajuda a saber se uma falha de chamada e um problema isolado de requisicao ou um problema concentrado em um plano, modelo ou grupo upstream. Ao diagnosticar falhas de API, veja primeiro o status do grupo e depois use os logs de uso para localizar o `request_id` especifico.

::: info Escopo dos dados
A pagina publica `status` incorporada aqui consulta o status de saude dos grupos usados por todos os usuarios FishXCode durante o periodo selecionado. Ela reflete a disponibilidade global dos grupos da plataforma e e em tempo real, imparcial e estavel.

A visao **Logs de uso -> Status de saude dos grupos** dentro do console conta apenas as requisicoes do usuario atual. Use-a para diagnosticar seus proprios tokens, modelos e requisicoes.
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

A imagem abaixo e um exemplo de **Logs de uso -> Status de saude dos grupos** no console. O periodo e de 6 de abril de 2026 a 1 de maio de 2026, mostrando taxa de sucesso, numero de requisicoes, tempo medio, horario da requisicao mais recente e motivos de falha para grupos globais.

![Exemplo de status de saude dos grupos no console](/img/group-health.png)

::: tip Principio de uso
Primeiro determine o alcance do impacto, depois trate o erro individual. Logs individuais servem para localizar a requisicao; status de grupo serve para saber se o problema esta concentrado.
:::

Para interpretar uma mensagem de erro individual, veja [Explicacao dos logs de erro](/pt/error-logs).

## Campos

| Campo | Significado | Valor para diagnostico |
|-------|-------------|------------------------|
| `group` | Grupo da requisicao, como grupo de plano, grupo padrao ou grupo de modelo especifico | Saber se o problema esta concentrado em um plano, modelo ou pool upstream |
| `total_count` | Total de requisicoes no periodo atual | Saber se a amostra e suficiente e evitar conclusao por poucas requisicoes |
| `success_count` | Numero de requisicoes bem-sucedidas | Comparar com `total_count` para avaliar disponibilidade geral |
| `error_count` | Numero de requisicoes com falha | Quando cresce continuamente, ver primeiro `error_reasons` |
| `success_rate` | Taxa de sucesso | Se estiver claramente baixa, normalmente indica problema concentrado no grupo |
| `avg_use_time` | Tempo medio, em segundos | Se subir, investigar contexto longo, saida longa, cadeia de ferramentas e lentidao upstream |
| `quota` | Cota do grupo ou valor estatistico de cota | Combinar com plano e saldo no console para saber se esta perto do limite |
| `tokens` | Consumo de Token no periodo atual | Identificar consumo anormal ou requisicoes com contexto grande |
| `first_seen_at` | Primeiro horario visto no periodo estatistico | Localizar o inicio do problema |
| `last_seen_at` | Ultimo horario visto no periodo estatistico | Saber se o problema ainda continua |
| `error_reasons` | Motivos de erro frequentes e contagens | Priorizar o erro com mais ocorrencias, nao apenas o ultimo log |

## Fluxo de diagnostico

### 1. Veja primeiro taxa de sucesso e numero de erros

Se `success_rate` esta perto do normal e `error_count` e baixo, geralmente e uma falha ocasional; copie o `request_id` da requisicao individual para continuar.

Se `success_rate` de um grupo esta claramente abaixo dos outros, ou `error_count` esta concentrado, investigue primeiro modelo, Token, conta upstream, permissao do plano e recursos da plataforma por grupo.

### 2. Depois veja os principais motivos de erro

`error_reasons` normalmente aparece ordenado por ocorrencias. Trate primeiro o erro mais frequente e depois os de baixa frequencia. Erros frequentes representam o principal tipo de falha no periodo atual.

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

::: tip Conclusao rapida
`401` / `403` normalmente pede verificacao de permissao, `413` pede corpo menor, `429` pede frequencia e cota, `502` / `504` / `524` pede upstream e tarefas longas, `503` pede verificar recurso temporariamente indisponivel.
:::
