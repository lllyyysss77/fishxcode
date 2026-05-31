# Usar FishXCode en QClaw

> ¿Es tu primera vez? Completa primero el [registro de cuenta y recarga](/es/account), y obtén tu API Key en la [consola de FishXCode](https://fishxcode.com/console/token).

## 1. Confirmar los parámetros de conexión

Al conectar QClaw con una API de modelos externos, usa preferentemente el protocolo OpenAI Compatible. Aunque llames a un modelo Claude, rellena los parámetros de la interfaz compatible con OpenAI.

| Elemento de configuración | Valor |
| --- | --- |
| Protocol | `OpenAI Compatible` |
| Base URL | `https://api.fishxcode.com/v1` |
| API Key | API Key creada en la consola de FishXCode |
| Modelo OpenAI | `gpt-5.5` |
| Modelo Claude | `claude-opus-4-7` |

Si QClaw exige un endpoint completo, usa:

```text
https://api.fishxcode.com/v1/chat/completions
```

## 2. Configurar desde la interfaz de ajustes

Si tu versión de QClaw ofrece una interfaz gráfica, puedes rellenarla así:

```text
Provider: OpenAI Compatible
Base URL: https://api.fishxcode.com/v1
API Key: sk-你的FishXCode-Token
Model: claude-opus-4-7
```

Si quieres usar el modelo GPT, cambia `Model` por:

```text
gpt-5.5
```

## 3. Configurar mediante config.yaml

Si tu versión de QClaw usa `~/.qclaw/config.yaml`, toma como referencia esta plantilla:

```yaml
llm:
  provider: openai-compatible
  base_url: https://api.fishxcode.com/v1
  api_key: sk-你的FishXCode-Token
  model: claude-opus-4-7
  max_tokens: 8192
```

Para cambiar al modelo OpenAI, solo reemplaza `model`:

```yaml
llm:
  provider: openai-compatible
  base_url: https://api.fishxcode.com/v1
  api_key: sk-你的FishXCode-Token
  model: gpt-5.5
  max_tokens: 8192
```

Si el cliente requiere la dirección completa de la interfaz, puedes cambiarla a:

```yaml
llm:
  provider: openai-compatible
  url: https://api.fishxcode.com/v1/chat/completions
  api_key: sk-你的FishXCode-Token
  model: claude-opus-4-7
  max_tokens: 8192
```

::: warning Importante
Los nombres de campo pueden variar según la versión de QClaw, por ejemplo `base_url`, `baseURL`, `apiBase` o `url`. Si no surte efecto después de guardar, comprueba primero los nombres de campo y la ruta del archivo de configuración que QClaw lee realmente.
:::

## 4. Configurar mediante variables de entorno

Si QClaw admite variables de entorno, también puedes usar:

::: code-group

```bash [Linux/macOS]
export OPENAI_BASE_URL="https://api.fishxcode.com/v1"
export OPENAI_API_KEY="sk-你的FishXCode-Token"
export QCLAW_MODEL="claude-opus-4-7"
qclaw
```

```powershell [Windows PowerShell]
$env:OPENAI_BASE_URL="https://api.fishxcode.com/v1"
$env:OPENAI_API_KEY="sk-你的FishXCode-Token"
$env:QCLAW_MODEL="claude-opus-4-7"
qclaw
```

:::

Cuando necesites usar el modelo GPT, cambia `QCLAW_MODEL` a `gpt-5.5`.

## 5. Verificar la configuración

Primero verifica con la interfaz compatible con OpenAI que la API Key sea válida:

::: code-group

```bash [Modelo Claude]
curl https://api.fishxcode.com/v1/chat/completions \
  -H "content-type: application/json" \
  -H "authorization: Bearer sk-你的FishXCode-Token" \
  -d '{
    "model": "claude-opus-4-7",
    "messages": [
      { "role": "user", "content": "用一句话说明你是谁" }
    ]
  }'
```

```bash [Modelo GPT]
curl https://api.fishxcode.com/v1/chat/completions \
  -H "content-type: application/json" \
  -H "authorization: Bearer sk-你的FishXCode-Token" \
  -d '{
    "model": "gpt-5.5",
    "messages": [
      { "role": "user", "content": "用一句话说明你是谁" }
    ]
  }'
```

:::

Si curl no funciona, es muy probable que tampoco funcione en QClaw. Comprueba primero la API Key, los permisos del modelo y la Base URL.

1. Guarda los ajustes o `config.yaml`
2. Reinicia QClaw
3. Selecciona `gpt-5.5` o `claude-opus-4-7`
4. Envía una pregunta breve, por ejemplo `用一句话说明你是谁`
5. Si devuelve una respuesta normal, la integración se completó correctamente

## Preguntas frecuentes

### Error 404 o dirección de interfaz incorrecta

Si rellenas Base URL, usa `https://api.fishxcode.com/v1`. Si rellenas la URL completa, usa `https://api.fishxcode.com/v1/chat/completions`.

### ¿Se puede usar el modelo Claude?

Sí. La condición es que FishXCode admita actualmente ese modelo y que QClaw lo llame mediante el protocolo OpenAI Compatible. Como nombre de modelo, usa `claude-opus-4-7`.

### Falla la autenticación

Copia de nuevo la API Key, confirma que no haya espacios adicionales y comprueba que la clave no se haya eliminado ni desactivado.
