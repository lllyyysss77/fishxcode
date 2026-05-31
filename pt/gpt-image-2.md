# Guia de geração de imagens com GPT-Image-2

## Pré-requisitos

Antes de começar, prepare o seguinte:

1. Uma conta FishXCode
2. Um token de API utilizável
3. Confirmar que o token atual pode acessar `gpt-image-2`

Obtenha seu token aqui:
- Console: [https://fishxcode.com/console/token](https://fishxcode.com/console/token)

::: tip Dica
Se o console mostrar grupos de modelos ou permissões, verifique se o token inclui acesso ao `gpt-image-2`. Se houver dúvida, crie um novo token padrão e teste com ele primeiro.
:::

## Ferramenta visual open-source

Se você não quiser começar escrevendo código, use a ferramenta open-source GPT-Image-2 da FishXCode para testar prompts e gerar imagens diretamente:

- Demo online: [https://fishxcode-gpt-image-2.lovable.app/](https://fishxcode-gpt-image-2.lovable.app/)
- Repositório GitHub: [https://github.com/fishxcode/gpt-image-2](https://github.com/fishxcode/gpt-image-2)

::: tip Dica
Use a ferramenta para validar token, prompt e parâmetros de imagem antes de migrar os mesmos parâmetros para o seu código ou fluxo de trabalho.
:::

## Método 1: Gerar imagens pela Images API

Esta é a forma mais direta e corresponde ao endpoint de geração de imagens compatível com OpenAI.

- Endpoint: `https://api.fishxcode.com/v1/images/generations`
- Modelo: `gpt-image-2`

### Exemplo em Python

```python
import base64
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-fishxcode-token",
    base_url="https://api.fishxcode.com/v1"
)

result = client.images.generate(
    model="gpt-image-2",
    prompt="Um gato laranja usando um capacete de astronauta, sentado na lua, iluminação cinematográfica, ultra detalhado",
    size="1024x1024"
)

image_base64 = result.data[0].b64_json

with open("gpt-image-2-output.png", "wb") as f:
    f.write(base64.b64decode(image_base64))
```

### Exemplo em Node.js

```javascript
import fs from "node:fs";
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "sk-your-fishxcode-token",
  baseURL: "https://api.fishxcode.com/v1",
});

const result = await client.images.generate({
  model: "gpt-image-2",
  prompt: "Uma cidade futurista flutuando acima das nuvens, estilo neon cyberpunk, ultra detalhada",
  size: "1024x1024",
});

const imageBase64 = result.data[0].b64_json;
fs.writeFileSync("gpt-image-2-output.png", Buffer.from(imageBase64, "base64"));
```

### Exemplo com curl

```bash
curl --request POST \
  --url https://api.fishxcode.com/v1/images/generations \
  --header "Authorization: Bearer sk-your-fishxcode-token" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "gpt-image-2",
    "prompt": "Um pôster minimalista de produto em fundo branco, com um teclado de vidro transparente flutuando no centro, estilo fotografia comercial",
    "size": "1024x1024"
  }'
```

::: warning Atenção
O principal resultado da Images API normalmente vem em `b64_json`, então você precisa decodificar a string Base64 para gerar um arquivo de imagem real.
:::

## Método 2: Gerar imagens via Chat Completions

Se o seu fluxo já estiver baseado em `/v1/chat/completions`, você também pode chamar `gpt-image-2` por lá.

- Endpoint: `https://api.fishxcode.com/v1/chat/completions`
- Modelo: `gpt-image-2`

### Exemplo em Python

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-fishxcode-token",
    base_url="https://api.fishxcode.com/v1"
)

response = client.chat.completions.create(
    model="gpt-image-2",
    messages=[
        {
            "role": "user",
            "content": "Desenhe um Shiba Inu usando óculos escuros, sentado em um conversível vermelho vintage, estilo road trip"
        }
    ]
)

print(response)
```

### Exemplo com curl

```bash
curl --request POST \
  --url https://api.fishxcode.com/v1/chat/completions \
  --header "Authorization: Bearer sk-your-fishxcode-token" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "gpt-image-2",
    "messages": [
      {
        "role": "user",
        "content": "Desenhe um café japonês ao pôr do sol com composição cinematográfica acolhedora"
      }
    ]
  }'
```

::: tip Dica
Se o objetivo é apenas obter uma imagem de forma estável, prefira `/v1/images/generations`. Use `/v1/chat/completions` apenas quando precisar encaixar a geração de imagens em um fluxo de conversa já existente.
:::

## Parâmetros comuns

Os parâmetros mais comuns do `gpt-image-2` incluem:

| Parâmetro | Significado | Exemplo |
| --- | --- | --- |
| `model` | ID do modelo | `gpt-image-2` |
| `prompt` | Prompt da imagem | `uma coruja mecânica` |
| `size` | Tamanho de saída | `1024x1024` |
| `n` | Quantidade de imagens | `1` |
| `background` | Modo de fundo | `transparent` / `white` |
| `quality` | Qualidade de saída | `high` / `medium` |

::: warning Atenção
A compatibilidade dos parâmetros pode variar conforme o SDK e a camada de proxy. Se algo falhar, volte primeiro ao conjunto mínimo: `model + prompt + size`.
:::

## Usando GPT-Image-2 no Cherry Studio

Se você prefere gerar imagens diretamente no Cherry Studio, configure assim:

### 1. Adicione um provedor compatível com OpenAI

Crie um provedor personalizado no Cherry Studio com:

- API Key: seu token do FishXCode
- Base URL: `https://api.fishxcode.com/v1`

### 2. Adicione o modelo

Use este nome de modelo:

```text
gpt-image-2
```

Se o Cherry Studio permitir definir o tipo de modelo, escolha um tipo de geração de imagens ou um tipo compatível com OpenAI Images.

### 3. Comece a gerar

Crie uma nova sessão ou abra o painel de geração de imagens, selecione `gpt-image-2` e informe o prompt.

::: tip Dica
Se o Cherry Studio não mostrar a imagem, verifique primeiro:
- se a Base URL está `https://api.fishxcode.com/v1`
- se a versão do Cherry Studio oferece suporte correto à OpenAI Images API
:::

## Perguntas frequentes

### Por que a requisição deu certo, mas nenhum arquivo de imagem apareceu?

Porque muitas respostas retornam `b64_json` em vez de um arquivo salvo. Você precisa decodificar essa string Base64 e gravá-la manualmente em um arquivo `.png`.

### Qual endpoint devo priorizar?

- Apenas geração de imagem: prefira `POST /v1/images/generations`
- Geração de imagem dentro de um fluxo de chat existente: considere `POST /v1/chat/completions`

### O que fazer se o modelo não existir ou eu não tiver permissão?

Verifique nesta ordem:

1. Confirme que o nome do modelo é `gpt-image-2`
2. Confirme que o token foi obtido no [Console do FishXCode](https://fishxcode.com/console/token)
3. Confirme que o token tem acesso ao `gpt-image-2`
4. Confirme que a Base URL é `https://api.fishxcode.com/v1`

### Por que alguns parâmetros extras falham?

Porque a compatibilidade varia entre clientes, versões do SDK e camadas de proxy. Comece com o conjunto mínimo viável e depois adicione `quality`, `background`, `n` e outros parâmetros opcionais aos poucos.
