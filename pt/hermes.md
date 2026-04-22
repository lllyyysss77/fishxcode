# Usar FishXCode com Hermes

::: info Introdução do projeto
Hermes Agent é um agente de IA de uso geral da Nous Research. Ele oferece chat via CLI, chamadas de ferramentas, memória, skills, gateways e tarefas agendadas. Pode se conectar tanto a serviços em nuvem oficialmente suportados quanto a qualquer endpoint compatível com OpenAI.

- Site oficial: [https://hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com)
- Documentação: [https://hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs)
- GitHub: [https://github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
:::

## Pré-requisitos

- Hermes instalado
- API Key do FishXCode ([Obter no Console](https://fishxcode.com/console/token))

## Instalar o Hermes

::: info Requisitos do ambiente
- macOS / Linux / WSL2
- Windows nativo não é suportado; recomenda-se usar WSL2
:::

```bash
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

Após a instalação, recarregue a configuração do shell:

```bash
source ~/.zshrc
```

Se você usa `bash`, execute:

```bash
source ~/.bashrc
```

## Configurar o FishXCode

O Hermes recomenda oficialmente usar `hermes model` para a configuração interativa. Para o FishXCode, escolha **Custom endpoint**, porque o FishXCode fornece uma API compatível com OpenAI.

### Opção 1: Configuração interativa com `hermes model` (Recomendada)

Execute:

```bash
hermes model
```

Preencha os campos assim:

- Provider: `Custom endpoint (self-hosted / VLLM / etc.)`
- API base URL: `https://fishxcode.com/v1`
- API key: seu token do FishXCode
- Model name: `gpt-5`
- Context length: deixe em branco ou informe o tamanho real do contexto

Após concluir a configuração, o Hermes gravará o modelo, o provedor e o endpoint em `~/.hermes/config.yaml`.

### Opção 2: Editar o arquivo de configuração manualmente

O Hermes usa `~/.hermes/config.yaml` como arquivo principal de configuração. Se o diretório ainda não existir, crie-o primeiro:

```bash
mkdir -p ~/.hermes
touch ~/.hermes/config.yaml
touch ~/.hermes/.env
```

Depois, coloque seu token em `~/.hermes/.env`:

```bash
OPENAI_API_KEY=sk-your-fishxcode-token
```

Em seguida, escreva isto em `~/.hermes/config.yaml`:

```yaml
model:
  default: gpt-5
  provider: custom
  base_url: https://fishxcode.com/v1
```

::: tip Dica
Para endpoints personalizados, o Hermes lê `provider`, `default` e `base_url` de `config.yaml`. A API key pode ser escrita diretamente em `config.yaml`, ou colocada em `~/.hermes/.env` como `OPENAI_API_KEY`, como acima. Recomenda-se usar `.env` para evitar armazenar a chave em texto puro.
:::

## Trocar de modelo

Se quiser usar outro modelo suportado pelo FishXCode, basta alterar `model.default` ou executar `hermes model` novamente.

Por exemplo:

```yaml
model:
  default: claude-sonnet-4-5-20250929
  provider: custom
  base_url: https://fishxcode.com/v1
```

::: warning Observação
Isso pressupõe que o modelo esteja disponível pelo endpoint compatível com OpenAI do FishXCode. Se você não tiver certeza sobre o ID exato do modelo, consulte primeiro a página de [Modelos suportados](/pt/models) e depois preencha o campo `default`.
:::

## Começar a usar o Hermes

Inicie uma sessão interativa:

```bash
hermes
```

Ou envie uma mensagem rápida de teste:

```bash
hermes chat "Responda em uma frase: FishXCode está conectado"
```

Se a configuração já estiver funcionando, você também pode trocar para outro modelo configurado dentro da sessão usando `/model`.

## Verificar a configuração

Comece com estas verificações:

```bash
hermes doctor
```

```bash
hermes config check
```

```bash
hermes chat "Responda apenas ok"
```

Se o último comando retornar uma resposta normal, a integração com o FishXCode está funcionando.

## Perguntas frequentes

### Por que escolher `Custom endpoint`?

Porque o Hermes trata qualquer API compatível com OpenAI como `provider: custom`. O FishXCode se encaixa exatamente nesse modelo, então você não precisa de um adaptador específico para Hermes.

### Por que `OPENAI_BASE_URL` ou `LLM_MODEL` não funcionam?

O Hermes removeu o suporte a essas variáveis de ambiente antigas. Agora o modelo, o provedor e o endpoint são definidos por `~/.hermes/config.yaml`.

### O que fazer se a API key estiver configurada, mas a autenticação ainda falhar?

Verifique nesta ordem:

1. Confirme que `base_url` é `https://fishxcode.com/v1`
2. Confirme que o token foi obtido no [Console do FishXCode](https://fishxcode.com/console/token)
3. Confirme que `model.default` é um ID de modelo válido, como `gpt-5`
4. Execute `hermes config check` e `hermes doctor` para ver o erro exato

### Posso instalar o Hermes diretamente no Windows?

Até 22 de abril de 2026, a documentação oficial do Hermes ainda informa que o Windows nativo não é suportado. Use WSL2 no lugar.
