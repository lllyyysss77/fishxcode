# Guía de generación de imágenes con GPT-Image-2

## Requisitos previos

Antes de empezar, prepara lo siguiente:

1. Una cuenta de FishXCode
2. Un token API usable
3. Confirmar que tu token actual puede acceder a `gpt-image-2`

Obtén tu token aquí:
- Consola: [https://fishxcode.com/console/token](https://fishxcode.com/console/token)

::: tip Consejo
Si tu consola muestra grupos de modelos o permisos, asegúrate de que tu token incluya acceso a `gpt-image-2`. Si no estás seguro, crea un token nuevo por defecto y haz la prueba con ese.
:::

## Herramienta visual open source

Si no quieres empezar escribiendo código, puedes usar la herramienta GPT-Image-2 open source de FishXCode para probar prompts y generar imágenes directamente:

- Demo online: [https://fishxcode-gpt-image-2.lovable.app/](https://fishxcode-gpt-image-2.lovable.app/)
- Repositorio GitHub: [https://github.com/fishxcode/gpt-image-2](https://github.com/fishxcode/gpt-image-2)

::: tip Consejo
Usa primero la herramienta para verificar tu token, prompt y parámetros de imagen; después migra los mismos parámetros a tu código o flujo de trabajo.
:::

## Método 1: Generar imágenes con la Images API

Es la forma más directa y corresponde al endpoint de generación de imágenes compatible con OpenAI.

- Endpoint: `https://fishxcode.com/v1/images/generations`
- Modelo: `gpt-image-2`

### Ejemplo en Python

```python
import base64
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-fishxcode-token",
    base_url="https://fishxcode.com/v1"
)

result = client.images.generate(
    model="gpt-image-2",
    prompt="Un gato naranja con casco de astronauta sentado sobre la luna, iluminación cinematográfica, ultra detallado",
    size="1024x1024"
)

image_base64 = result.data[0].b64_json

with open("gpt-image-2-output.png", "wb") as f:
    f.write(base64.b64decode(image_base64))
```

### Ejemplo en Node.js

```javascript
import fs from "node:fs";
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "sk-your-fishxcode-token",
  baseURL: "https://fishxcode.com/v1",
});

const result = await client.images.generate({
  model: "gpt-image-2",
  prompt: "Una ciudad futurista flotando sobre las nubes, estilo neón cyberpunk, ultra detallada",
  size: "1024x1024",
});

const imageBase64 = result.data[0].b64_json;
fs.writeFileSync("gpt-image-2-output.png", Buffer.from(imageBase64, "base64"));
```

### Ejemplo con curl

```bash
curl --request POST \
  --url https://fishxcode.com/v1/images/generations \
  --header "Authorization: Bearer sk-your-fishxcode-token" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "gpt-image-2",
    "prompt": "Un póster de producto minimalista sobre fondo blanco, con un teclado de cristal transparente flotando en el centro, estilo fotografía comercial",
    "size": "1024x1024"
  }'
```

::: warning Nota
El resultado principal de la Images API suele venir en `b64_json`, por lo que debes decodificar la cadena Base64 para obtener un archivo de imagen real.
:::

## Método 2: Generar imágenes mediante Chat Completions

Si tu flujo ya está basado en `/v1/chat/completions`, también puedes invocar `gpt-image-2` desde ahí.

- Endpoint: `https://fishxcode.com/v1/chat/completions`
- Modelo: `gpt-image-2`

### Ejemplo en Python

```python
from openai import OpenAI

client = OpenAI(
    api_key="sk-your-fishxcode-token",
    base_url="https://fishxcode.com/v1"
)

response = client.chat.completions.create(
    model="gpt-image-2",
    messages=[
        {
            "role": "user",
            "content": "Dibuja un Shiba Inu con gafas de sol, sentado en un descapotable rojo vintage, estilo road trip"
        }
    ]
)

print(response)
```

### Ejemplo con curl

```bash
curl --request POST \
  --url https://fishxcode.com/v1/chat/completions \
  --header "Authorization: Bearer sk-your-fishxcode-token" \
  --header "Content-Type: application/json" \
  --data '{
    "model": "gpt-image-2",
    "messages": [
      {
        "role": "user",
        "content": "Dibuja una cafetería japonesa al atardecer con una composición cinematográfica cálida"
      }
    ]
  }'
```

::: tip Consejo
Si solo buscas obtener imágenes de forma estable, prioriza `/v1/images/generations`. Usa `/v1/chat/completions` solo si necesitas integrarlo dentro de un flujo conversacional ya existente.
:::

## Parámetros comunes

Los parámetros usados con más frecuencia en `gpt-image-2` son:

| Parámetro | Significado | Ejemplo |
| --- | --- | --- |
| `model` | ID del modelo | `gpt-image-2` |
| `prompt` | Prompt de imagen | `un búho mecánico` |
| `size` | Tamaño de salida | `1024x1024` |
| `n` | Número de imágenes | `1` |
| `background` | Modo de fondo | `transparent` / `white` |
| `quality` | Calidad de salida | `high` / `medium` |

::: warning Nota
La compatibilidad de parámetros puede variar según el SDK y la capa proxy. Si algo falla, vuelve primero al conjunto mínimo: `model + prompt + size`.
:::

## Usar GPT-Image-2 en Cherry Studio

Si prefieres generar imágenes directamente desde Cherry Studio, configúralo así:

### 1. Añadir un proveedor compatible con OpenAI

Crea un proveedor personalizado en Cherry Studio con:

- API Key: tu token de FishXCode
- Base URL: `https://fishxcode.com/v1`

### 2. Añadir el modelo

Usa este nombre de modelo:

```text
gpt-image-2
```

Si Cherry Studio permite definir el tipo de modelo, elige un tipo de generación de imágenes o uno compatible con OpenAI Images.

### 3. Empezar a generar

Crea una nueva sesión o abre el panel de generación de imágenes, selecciona `gpt-image-2` e introduce tu prompt.

::: tip Consejo
Si Cherry Studio no muestra imágenes, revisa primero:
- que la Base URL sea `https://fishxcode.com/v1`
- que tu versión de Cherry Studio soporte correctamente la API OpenAI Images
:::

## Preguntas frecuentes

### ¿Por qué la petición fue exitosa pero no apareció ningún archivo de imagen?

Porque muchas respuestas devuelven `b64_json` en lugar de un archivo guardado. Debes decodificar esa cadena Base64 y escribirla tú mismo en un archivo `.png`.

### ¿Qué endpoint debería usar primero?

- Solo generación de imágenes: usa `POST /v1/images/generations`
- Generación de imágenes dentro de un flujo de chat existente: considera `POST /v1/chat/completions`

### ¿Qué hago si el modelo no existe o no tengo permisos?

Revísalo en este orden:

1. Asegúrate de que el nombre del modelo sea `gpt-image-2`
2. Asegúrate de que el token venga de la [Consola de FishXCode](https://fishxcode.com/console/token)
3. Asegúrate de que tu token tenga acceso a `gpt-image-2`
4. Asegúrate de que la Base URL sea `https://fishxcode.com/v1`

### ¿Por qué fallan algunos parámetros extra?

Porque la compatibilidad varía entre clientes, versiones del SDK y capas proxy. Empieza por el conjunto mínimo viable y luego añade `quality`, `background`, `n` y otros parámetros opcionales poco a poco.
