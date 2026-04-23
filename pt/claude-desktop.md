# Usar FishXCode com Claude Desktop

## Escopo

Este guia cobre o novo modo de **Cowork / Code com inferência de terceiros** no Claude Desktop, documentado pela Anthropic como `third-party inference`.

Use esta configuração se você quiser:

- usar **Cowork** no Claude Desktop
- usar **Code** no Claude Desktop
- encaminhar as solicitações pelo FishXCode com uma API ou gateway personalizado

::: warning Observação
Este não é o fluxo antigo de "entrar e conversar". Quando a inferência de terceiros está ativa, você vai usar principalmente as abas **Cowork** e **Code**.
:::

## Pré-requisitos

Prepare o seguinte:

1. A versão mais recente do Claude Desktop
2. Um token do FishXCode
3. Um token com acesso aos modelos Claude

Obtenha o token aqui:
- [https://fishxcode.com/console/token](https://fishxcode.com/console/token)

Modelos recomendados para começar:

- `claude-sonnet-4-5-20250929`
- `claude-opus-4-5-20251101`
- `claude-haiku-4-5-20251001`

## Método 1: Configurar diretamente no Claude Desktop (Recomendado)

A Anthropic recomenda usar o fluxo integrado de configuração de inferência de terceiros dentro do aplicativo.

### 1. Ativar o Developer mode

No Claude Desktop, vá para:

```text
Help → Troubleshooting → Enable Developer mode
```

Depois de ativar, você deverá ver entradas de menu `Developer`.

### 2. Abrir a configuração de inferência de terceiros

Depois, vá para:

```text
Developer → Configure third-party inference
```

Isso abre o fluxo oficial de configuração da Anthropic.

### 3. Escolher o modo Gateway

Selecione:

```text
Gateway
```

O FishXCode se encaixa no modo `gateway` dentro desse fluxo.

### 4. Preencher a configuração do FishXCode

Use estes valores:

- Gateway URL: `https://fishxcode.com`
- Authentication: `x-api-key`
- API Key: seu token do FishXCode

Se a interface pedir uma lista de modelos, comece com:

```text
claude-sonnet-4-5-20250929
claude-opus-4-5-20251101
claude-haiku-4-5-20251001
```

Se houver opção para ativar `Code`, deixe ativada.

### 5. Aplicar localmente

Ao finalizar, escolha:

```text
Apply locally
```

Isso grava a configuração no arquivo local do Claude Desktop do usuário atual.

Depois disso, reinicie o Claude Desktop. Na maioria dos casos, você verá então o espaço de trabalho **Cowork / Code**.

## Método 2: Gerenciar pelo arquivo de configuração local (Avançado)

Se quiser fazer backup, migrar ou depurar a configuração, você pode inspecionar o arquivo local.

Caminhos comuns:

### macOS

```text
~/Library/Application Support/Claude-3p/claude_desktop_config.json
```

### Windows

```text
%APPDATA%\Claude-3p\claude_desktop_config.json
```

### Linux

```text
~/.config/Claude-3p/claude_desktop_config.json
```

::: tip Dica
O arquivo local armazena a configuração de inferência de terceiros em `enterpriseConfig`. O caminho mais seguro é configurar primeiro no app e depois fazer backup do resultado gerado, em vez de escrever JSON manualmente do zero.
:::

## Valores recomendados para FishXCode

Se quiser conferir manualmente os campos principais, foque nestes:

| Campo | Valor recomendado |
| --- | --- |
| `inferenceProvider` | `gateway` |
| `inferenceGatewayBaseUrl` | `https://fishxcode.com` |
| `inferenceGatewayAuthScheme` | `x-api-key` |
| `inferenceGatewayApiKey` | seu token do FishXCode |
| `inferenceModels` | lista de modelos Claude |
| `isClaudeCodeForDesktopEnabled` | `true` |

Lista recomendada de modelos:

```text
claude-sonnet-4-5-20250929
claude-opus-4-5-20251101
claude-haiku-4-5-20251001
```

## Uso

Depois de configurado, você pode usar o Claude Desktop para:

- abrir **Cowork** para tarefas gerais, documentos e colaboração em arquivos
- abrir **Code** para programação, terminal e tarefas de engenharia

Se o seletor de modelos estiver visível, comece com:

- uso diário: `claude-sonnet-4-5-20250929`
- raciocínio profundo: `claude-opus-4-5-20251101`
- tarefas leves e rápidas: `claude-haiku-4-5-20251001`

## Perguntas frequentes

### Por que não encontro `Enable Developer mode`?

Verifique primeiro:

1. Se você está usando uma versão recente do Claude Desktop
2. Se está olhando a barra de menus real do sistema, e não apenas os controles da janela

Se ainda não aparecer após atualizar, feche totalmente o Claude Desktop e abra novamente.

### Por que não vejo Cowork / Code depois da configuração?

Verifique nesta ordem:

1. O Developer mode está ativado
2. Você concluiu `Configure third-party inference`
3. Você escolheu `Gateway`
4. `gatewayUrl` é `https://fishxcode.com`
5. O token é válido e pode acessar modelos Claude
6. O Claude Desktop foi reiniciado completamente após a configuração

### Por que a Gateway URL não é `https://fishxcode.com/v1`?

Porque a inferência de terceiros do Claude Desktop usa uma configuração de gateway no estilo Anthropic, e não o caminho OpenAI-compatível `/v1/chat/completions`. Aqui você deve usar a URL raiz do gateway:

```text
https://fishxcode.com
```

### O que fazer se a autenticação falhar depois da configuração?

Confira primeiro:

1. se a autenticação está definida como `x-api-key`
2. se o campo API Key contém diretamente o seu token do FishXCode
3. se o token ainda é válido e possui as permissões necessárias

### Posso editar o JSON local manualmente?

Sim, mas não é a melhor escolha para a configuração inicial. Os campos de inferência de terceiros evoluem com as versões do Claude Desktop, e o fluxo integrado costuma ser mais estável e mais fácil de recuperar se algo quebrar.
