# Usar FishXCode con OpenClaw

::: info Introducción al proyecto
OpenClaw es una plataforma de asistente de IA personal de código abierto y auto-alojada que conecta aplicaciones de mensajería con agentes de IA que se ejecutan en tu propio hardware. Diseñada para desarrolladores y usuarios avanzados que desean asistentes de IA autónomos sin ceder el control de sus datos.

- Sitio oficial: [https://openclaw.ai](https://openclaw.ai)
- Documentación: [https://docs.openclaw.ai](https://docs.openclaw.ai)
- GitHub: [https://github.com/openclaw/openclaw](https://github.com/openclaw/openclaw)
:::

## Requisitos previos

- OpenClaw instalado (ver la sección de instalación a continuación)
- API Key de FishXCode ([Obtener desde la consola](https://fishxcode.com/console/token))

## Características principales

### Integración multicanal

- **Cobertura completa**: Soporta Lark (Feishu), Discord, Slack, Microsoft Teams y más
- **Pasarela única**: Gestiona todos los canales a través de un único proceso Gateway
- **Soporte de voz**: Interacción por voz en macOS/iOS/Android
- **Interfaz Canvas**: Renderiza interfaces Canvas interactivas

### Auto-alojamiento y seguridad de datos

- **Completamente auto-alojado**: Se ejecuta en tu propia máquina o servidor
- **Código abierto**: Licencia MIT, código completamente transparente
- **Almacenamiento local**: Contexto y habilidades almacenados localmente, no en la nube

### Capacidades de agente inteligente

- **Siempre activo**: Operación persistente en segundo plano con memoria persistente
- **Tareas programadas**: Soporta tareas cron
- **Aislamiento de sesiones**: Sesiones aisladas por agente/espacio de trabajo/remitente
- **Enrutamiento multi-agente**: Colaboración entre múltiples agentes
- **Llamada a herramientas**: Soporte nativo para llamadas a herramientas y ejecución de código

## Instalación

::: info Requisitos
- API Key de FishXCode
- Node.js 22+ requerido para los métodos npm/git; el one-liner curl gestiona las dependencias automáticamente
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

Tras la instalación, ejecuta el asistente de configuración (ya incluido en los pasos de clonado manual):

```bash
openclaw onboard
```

## Configuración

### Ubicación del archivo de configuración

El archivo de configuración de OpenClaw se encuentra en `~/.openclaw/openclaw.json`:

- **macOS**: `/Users/tu-usuario/.openclaw/openclaw.json`
- **Linux**: `/home/tu-usuario/.openclaw/openclaw.json`
- **Windows**: `C:\Users\tu-usuario\.openclaw\openclaw.json`

Si el archivo no existe, créalo primero:

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

::: tip Consejo
Si ya has ejecutado `openclaw onboard`, el archivo de configuración se generará automáticamente. Solo necesitas modificar el contenido existente.
:::

### Estructura del archivo de configuración

La estructura de `openclaw.json` consta de dos partes principales:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      // Configurar proveedores de modelos de IA
    }
  },
  "agents": {
    "defaults": {
      // Configurar modelo por defecto, directorio de trabajo, etc.
    }
  }
}
```

- `models.providers` — Define los proveedores de servicios (URL, clave, lista de modelos)
- `models.mode` — Establecer en `"merge"` para fusionar la configuración personalizada con los valores predeterminados integrados, **muy recomendado**
- `agents.defaults.model.primary` — Modelo a usar por defecto, formato: `nombre-proveedor/id-modelo`
- `api` — Tipo de protocolo API: `"anthropic-messages"` para modelos Anthropic, `"openai-responses"` para modelos compatibles con OpenAI

### Métodos de configuración

#### Configurar modelos Anthropic (Claude)

Añade el siguiente contenido a `openclaw.json`:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-anthropic": {
        "baseUrl": "https://api.fishxcode.com",
        "apiKey": "sk-tu-token-fishxcode",
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
- Reemplaza `sk-tu-token-fishxcode` por tu token real obtenido en la [consola de FishXCode](https://fishxcode.com/console/token)
- **Para el protocolo Anthropic, el `baseUrl` NO debe incluir `/v1`** — el SDK añade automáticamente la ruta
:::

#### Configurar modelos OpenAI (GPT)

Al llamar a modelos OpenAI a través de FishXCode, el campo `api` debe ser `openai-responses`:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-openai": {
        "baseUrl": "https://api.fishxcode.com/v1",
        "apiKey": "sk-tu-token-fishxcode",
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
**El protocolo OpenAI requiere `/v1`**, es decir, `https://api.fishxcode.com/v1`. Esto se debe a que los dos SDK tienen lógicas de concatenación de rutas diferentes.
:::

#### Configurar Anthropic + OpenAI simultáneamente (Recomendado)

Añade ambos providers uno al lado del otro en `models.providers` para usar modelos de ambas familias:

```json
{
  "models": {
    "mode": "merge",
    "providers": {
      "fishxcode-anthropic": {
        "baseUrl": "https://api.fishxcode.com",
        "apiKey": "sk-tu-token-fishxcode",
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
        "apiKey": "sk-tu-token-fishxcode",
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

### Campos clave de configuración

| Campo | Significado | Anthropic (Claude) | OpenAI (GPT) |
| --- | --- | --- | --- |
| `baseUrl` | Dirección del proxy API | `https://api.fishxcode.com` | `https://api.fishxcode.com/v1` |
| `apiKey` | Tu clave API | `sk-tu-token-fishxcode` | `sk-tu-token-fishxcode` |
| `api` | Tipo de protocolo API | `anthropic-messages` | `openai-responses` |
| `mode` | Modo de fusión de config | `merge` (recomendado) | `merge` (recomendado) |
| `models[].id` | ID del modelo | `claude-opus-4-6` | `gpt-5` |
| `model.primary` | Modelo por defecto | `fishxcode-anthropic/claude-opus-4-6` | `fishxcode-openai/gpt-5` |
| `reasoning` | Activar modo razonamiento | `false` (según modelo) | `true` (GPT-5.x soportado) |

## Verificar la configuración

Ejecuta el siguiente comando para confirmar que la configuración funciona:

```bash
openclaw
```

Ver la lista de modelos:

```bash
openclaw models list
```

Ver el estado de los modelos y la autenticación:

```bash
openclaw models status
```

Diagnóstico completo:

```bash
openclaw doctor
```

## Iniciar el servicio

Una vez completada la configuración, inicia OpenClaw:

```bash
openclaw start
```

Una vez iniciado, puedes interactuar con el asistente de IA a través de los canales configurados.

Reiniciar la pasarela:

```bash
openclaw gateway restart
```

## Solución de problemas

### 403 Bloqueado

**Síntoma**: El provider está configurado, la petición curl directa a la API devuelve 200, pero las peticiones desde OpenClaw reciben un 403 "Your request was blocked".

**Causa**: OpenClaw usa `@anthropic-ai/sdk` internamente, que envía peticiones con el User-Agent oficial del SDK (ej: `Anthropic/JS 0.73.0`). Algunos CDN o WAF bloquean este UA.

**Solución**: Añade un campo `headers` en la config del provider para reemplazar el UA:

```json
{
  "fishxcode-anthropic": {
    "baseUrl": "https://api.fishxcode.com",
    "apiKey": "tu-api-key",
    "api": "anthropic-messages",
    "headers": {
      "User-Agent": "Mozilla/5.0"
    },
    "models": [...]
  }
}
```

### No incluir /v1 en baseUrl (Protocolo Anthropic)

**Síntoma**: La petición devuelve 404, y los logs muestran que la ruta se ha convertido en `/v1/v1/messages`.

**Causa**: El SDK de Anthropic añade automáticamente `/v1/messages` al baseURL. Si tu baseUrl ya incluye `/v1`, la ruta real queda duplicada.

**Solución**: Para el protocolo Anthropic, solo escribe el dominio en baseUrl, sin `/v1`:

```json
{
  "baseUrl": "https://api.fishxcode.com"
}
```

::: tip
El protocolo OpenAI requiere `/v1`, es decir, `https://api.fishxcode.com/v1`. Esto se debe a que los dos SDK tienen lógicas de concatenación de rutas diferentes.
:::

### El campo api solo acepta tres valores

**Síntoma**: Al iniciar aparece "Config invalid", o el provider configurado no aparece en la lista de modelos.

**Causa**: OpenClaw valida estrictamente el campo `api`, aceptando solo estos tres valores:

| Valor | Protocolo |
| --- | --- |
| `anthropic-messages` | Anthropic Messages API |
| `openai-completions` | OpenAI Chat Completions |
| `openai-responses` | OpenAI Responses API |

Valores como `openai-chat`, `openai`, `anthropic`, etc. provocarán errores.

**Solución**: Al usar FishXCode, usa `anthropic-messages` para modelos Claude y `openai-responses` para modelos GPT.

### Respuesta vacía con openai-completions (No usar para modelos GPT)

**Síntoma**: `api` está configurado como `openai-completions`, la petición tiene éxito (`isError=false` en los logs), pero la interfaz muestra un mensaje vacío.

**Causa**: OpenClaw maneja los flujos de mensajes internamente en formato Anthropic. Las respuestas en formato OpenAI de `openai-completions` pueden no mapearse correctamente en algunos casos.

**Solución**: Al llamar a modelos GPT a través de FishXCode, usa `openai-responses` en lugar de `openai-completions`.

### Los cambios de configuración no surten efecto

**Síntoma**: Se modificó `openclaw.json` pero OpenClaw sigue usando la configuración antigua.

**Causa**: OpenClaw tiene dos lugares donde las configuraciones de providers deben sincronizarse:

```
~/.openclaw/openclaw.json              → models.providers
~/.openclaw/agents/main/agent/models.json → providers
```

Modificar solo uno puede causar problemas.

**Solución**: Tras la modificación, confirma con:

```bash
openclaw models status
```

O reinicia la pasarela de OpenClaw:

```bash
openclaw gateway restart
```

### Errores de formato JSON

Errores comunes de formato JSON:

- **Coma extra**: El último elemento no puede tener una coma final
- **Coma faltante**: Dos pares clave-valor adyacentes deben estar separados por una coma
- **Problemas con comillas**: JSON solo acepta comillas dobles inglesas `"`, no comillas simples ni especiales
- **Paréntesis no coincidentes**: Cada `{` debe tener un `}` correspondiente, cada `[` debe tener un `]` correspondiente

Valida el formato con:

```bash
python3 -m json.tool ~/.openclaw/openclaw.json
```

### Referencia de comandos de diagnóstico

| Comando | Propósito |
| --- | --- |
| `openclaw models status` | Ver estado de modelos y autenticación |
| `openclaw models list` | Ver lista de modelos configurados |
| `openclaw doctor` | Diagnóstico completo |
| `openclaw gateway restart` | Reiniciar la pasarela |

::: tip Estrategia de depuración
Primero usa curl para confirmar que la API de FishXCode funciona correctamente, luego verifica qué es diferente en las peticiones enviadas por OpenClaw (UA, ruta, formato).
:::
