# Usar FishXCode en WorkBuddy

> ¿Es tu primera vez? Completa primero el [registro de cuenta y recarga](/es/account), y obtén tu API Key en la [consola de FishXCode](https://fishxcode.com/console/token).

## 1. Abrir el archivo de configuración de WorkBuddy

WorkBuddy permite conectar modelos personalizados mediante el archivo local `models.json`. La ubicación del archivo es:

- macOS / Linux: `~/.workbuddy/models.json`
- Windows: `C:\Users\<用户名>\.workbuddy\models.json`

Si el directorio o el archivo no existen, créalos primero.

## 2. Configurar modelos externos de FishXCode

La configuración de modelos externos de WorkBuddy usa el protocolo compatible con OpenAI Chat Completions. Aquí, `vendor: "OpenAI"` indica el protocolo de la interfaz, no que los modelos tengan que ser de OpenAI; los modelos Claude también se llaman mediante el endpoint OpenAI compatible de FishXCode.

Escribe el siguiente contenido en `~/.workbuddy/models.json`:

```json
{
  "models": [
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    }
  ],
  "availableModels": [
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

::: warning Importante
- Sustituye `sk-你的FishXCode-Token` por la API Key real que creaste en la consola de FishXCode.
- `availableModels` debe incluir los ID de modelo definidos en `models[].id`; de lo contrario, WorkBuddy podría no mostrarlos en la lista desplegable.
- `url` debe ser la dirección completa de Chat Completions: `https://api.fishxcode.com/v1/chat/completions`.
:::

## 3. Mantener Tencent Token Plan junto con FishXCode

Si también necesitas conservar el Token Plan oficial de Tencent, puedes colocar los modelos oficiales y los modelos de FishXCode en el mismo `models.json`:

```json
{
  "models": [
    {
      "id": "tc-code-latest",
      "name": "Auto",
      "vendor": "OpenAI",
      "url": "https://api.lkeap.cloud.tencent.com/plan/v3/chat/completions",
      "apiKey": "your-token-plan-api-key",
      "maxInputTokens": 100000,
      "maxOutputTokens": 4096
    },
    {
      "id": "hunyuan-2.0-instruct",
      "name": "Tencent HY 2.0 Instruct",
      "vendor": "OpenAI",
      "url": "https://api.lkeap.cloud.tencent.com/plan/v3/chat/completions",
      "apiKey": "your-token-plan-api-key",
      "maxInputTokens": 100000,
      "maxOutputTokens": 4096
    },
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "url": "https://api.fishxcode.com/v1/chat/completions",
      "apiKey": "sk-你的FishXCode-Token",
      "maxInputTokens": 200000,
      "maxOutputTokens": 8192
    }
  ],
  "availableModels": [
    "tc-code-latest",
    "hunyuan-2.0-instruct",
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

## 4. Reiniciar y verificar

1. Guarda `models.json`
2. Cierra WorkBuddy por completo
3. Vuelve a iniciar WorkBuddy
4. Abre el selector de modelos
5. Selecciona `gpt-5.5` o `claude-opus-4-7`
6. Envía una pregunta sencilla para comprobar la respuesta

## Preguntas frecuentes

### No aparecen los modelos nuevos

Comprueba primero que `availableModels` incluya el ID de modelo correspondiente y luego verifica que `models.json` sea JSON válido.

### La llamada falla o aparece un error en rojo

Comprueba que `url` sea la dirección completa `https://api.fishxcode.com/v1/chat/completions`, no solo `https://api.fishxcode.com/v1`.

### Falla la autenticación

Copia de nuevo la API Key desde la consola y confirma que no tenga espacios ni saltos de línea adicionales.
