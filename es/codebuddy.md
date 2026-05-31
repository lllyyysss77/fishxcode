# Usar FishXCode en CodeBuddy

> ¿Es tu primera vez? Completa primero el [registro de cuenta y recarga](/es/account), y obtén tu API Key en la [consola de FishXCode](https://fishxcode.com/console/token).

## 1. Instalar e iniciar CodeBuddy Code

Este tutorial configura la herramienta de línea de comandos **CodeBuddy Code** de Tencent Cloud. Primero confirma que tienes instalado Node.js 18.0 o una versión posterior y luego instala:

```bash
npm install -g @tencent-ai/codebuddy-code
```

Comprueba la versión:

```bash
codebuddy --version
```

Inicia CodeBuddy Code:

```bash
codebuddy
```

En el primer inicio, completa el inicio de sesión siguiendo las instrucciones. Después puedes escribir `/model` en la línea de comandos para comprobar si los modelos personalizados se cargaron correctamente.

## 2. Abrir el archivo de configuración de CodeBuddy

CodeBuddy Code permite conectar modelos personalizados mediante el archivo local `models.json`. La ubicación del archivo es:

- macOS / Linux: `~/.codebuddy/models.json`
- Windows: `C:\Users\<用户名>\.codebuddy\models.json`

Si el directorio o el archivo no existen, créalos primero.

## 3. Escribir la configuración de modelos de FishXCode

La configuración de modelos externos de CodeBuddy Code usa el protocolo compatible con OpenAI Chat Completions. Aquí, `vendor: "OpenAI"` indica compatibilidad de protocolo, no que solo se puedan usar modelos OpenAI; los modelos Claude también se llaman mediante el endpoint OpenAI compatible de FishXCode.

Escribe el siguiente contenido en `~/.codebuddy/models.json`:

```json
{
  "models": [
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
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
- `availableModels` debe incluir los ID de modelo definidos en `models[].id`; de lo contrario, los modelos nuevos podrían no aparecer en `/model`.
- Para un gateway de terceros se necesita la dirección completa de Chat Completions: `https://api.fishxcode.com/v1/chat/completions`.
:::

## 4. Mantener Tencent Coding Plan junto con FishXCode

Si también necesitas conservar el Coding Plan oficial de Tencent, puedes añadir los modelos de FishXCode al mismo `models.json`:

```json
{
  "models": [
    {
      "id": "tc-code-latest",
      "name": "Auto",
      "vendor": "Tencent Cloud",
      "apiKey": "YOUR_TENCENT_CODING_PLAN_KEY",
      "url": "https://api.lkeap.cloud.tencent.com/coding/v3"
    },
    {
      "id": "gpt-5.5",
      "name": "FishXCode GPT-5.5",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    },
    {
      "id": "claude-opus-4-7",
      "name": "FishXCode Claude Opus 4.7",
      "vendor": "OpenAI",
      "apiKey": "sk-你的FishXCode-Token",
      "url": "https://api.fishxcode.com/v1/chat/completions"
    }
  ],
  "availableModels": [
    "tc-code-latest",
    "gpt-5.5",
    "claude-opus-4-7"
  ]
}
```

## 5. Reiniciar y seleccionar el modelo

1. Guarda `~/.codebuddy/models.json`
2. Cierra por completo cualquier instancia de CodeBuddy Code en ejecución
3. Ejecuta de nuevo `codebuddy`
4. Escribe `/model`
5. Selecciona `gpt-5.5` o `claude-opus-4-7`
6. Envía un prompt de prueba, por ejemplo `解释当前项目结构`

## Preguntas frecuentes

### `/model` no muestra los modelos

Comprueba primero `availableModels`. El ID del modelo debe aparecer tanto en `models[].id` como en `availableModels`.

### El modelo aparece, pero la llamada falla

Comprueba primero `url`. La configuración de modelos externos de FishXCode debe usar la dirección completa `https://api.fishxcode.com/v1/chat/completions`.

### Falla la autenticación

Copia de nuevo la API Key desde la consola y confirma que no tenga espacios ni saltos de línea adicionales.
