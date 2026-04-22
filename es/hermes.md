# Usar FishXCode con Hermes

::: info Introducción del proyecto
Hermes Agent es un agente de IA de propósito general de Nous Research. Soporta chat por CLI, llamadas a herramientas, memoria, skills, gateways y tareas programadas. Puede conectarse tanto a servicios en la nube compatibles oficialmente como a cualquier endpoint compatible con OpenAI.

- Sitio oficial: [https://hermes-agent.nousresearch.com](https://hermes-agent.nousresearch.com)
- Documentación: [https://hermes-agent.nousresearch.com/docs](https://hermes-agent.nousresearch.com/docs)
- GitHub: [https://github.com/NousResearch/hermes-agent](https://github.com/NousResearch/hermes-agent)
:::

## Requisitos previos

- API Key de FishXCode ([Obtener desde la consola](https://fishxcode.com/console/token))
- `git` disponible en tu equipo

## Instalar Hermes

::: info Requisitos del entorno
- macOS / Linux / WSL2
- Es posible instalarlo en Windows con PowerShell, pero WSL2 sigue siendo la opción recomendada
- El instalador gestiona automáticamente Python, Node.js, ripgrep y ffmpeg
:::

::: code-group

```bash [Instalador oficial]
curl -fsSL https://raw.githubusercontent.com/NousResearch/hermes-agent/main/scripts/install.sh | bash
```

```powershell [Windows PowerShell]
irm https://res1.hermesagent.org.cn/install.ps1 | iex
```

:::

Después de la instalación, recarga la configuración de tu shell:

```bash
source ~/.zshrc
```

Si usas `bash`, ejecuta:

```bash
source ~/.bashrc
```

Si usas Windows PowerShell, solo tienes que cerrar y volver a abrir la terminal.

## Configurar FishXCode

Hermes recomienda oficialmente usar `hermes model` para la configuración interactiva. Para FishXCode, debes elegir **Custom endpoint**, porque FishXCode ofrece una API compatible con OpenAI.

Si quieres completar toda la configuración posterior a la instalación de una sola vez, también puedes ejecutar:

```bash
hermes setup
```

Si solo quieres revisar o reconfigurar los permisos de herramientas, ejecuta:

```bash
hermes tools
```

### Opción 1: Configuración interactiva con `hermes model` (Recomendada)

Ejecuta:

```bash
hermes model
```

Completa los campos así:

- Provider: `Custom endpoint (self-hosted / VLLM / etc.)`
- API base URL: `https://fishxcode.com/v1`
- API key: tu token de FishXCode
- Model name: `gpt-5.4`
- Context length: usa al menos `65536`

Después de completar la configuración, Hermes guardará el modelo, el proveedor y el endpoint en `~/.hermes/config.yaml`.

::: warning Importante
Hermes requiere un modelo con al menos `64K` de contexto para flujos reales de agente con múltiples pasos. En endpoints personalizados, elige un modelo y una ventana de contexto que cumplan ese requisito.
:::

### Opción 2: Editar el archivo de configuración manualmente

Hermes usa `~/.hermes/config.yaml` como archivo principal de configuración. Si el directorio aún no existe, créalo primero:

```bash
mkdir -p ~/.hermes
touch ~/.hermes/config.yaml
touch ~/.hermes/.env
```

Luego guarda tu token en `~/.hermes/.env`:

```bash
OPENAI_API_KEY=sk-your-fishxcode-token
```

Después escribe esto en `~/.hermes/config.yaml`:

```yaml
model:
  default: gpt-5.4
  provider: custom
  base_url: https://fishxcode.com/v1
```

::: tip Consejo
Para endpoints personalizados, Hermes lee `provider`, `default` y `base_url` desde `config.yaml`. La API key puede escribirse directamente en `config.yaml`, o colocarse en `~/.hermes/.env` como `OPENAI_API_KEY`, como arriba. Se recomienda usar `.env` para evitar guardar la clave en texto plano.
:::

## Cambiar de modelo

Si quieres usar otro modelo compatible con FishXCode, solo cambia `model.default` o vuelve a ejecutar `hermes model`.

Por ejemplo:

```yaml
model:
  default: claude-sonnet-4-5-20250929
  provider: custom
  base_url: https://fishxcode.com/v1
```

::: warning Nota
Esto supone que el modelo está disponible a través del endpoint compatible con OpenAI de FishXCode y que cumple el requisito mínimo de contexto de Hermes. Si no estás seguro del ID exacto del modelo, consulta primero la página de [Modelos compatibles](/es/models) y luego rellena el campo `default`.
:::

## Empezar a usar Hermes

Inicia una sesión interactiva:

```bash
hermes
```

O envía un mensaje de prueba rápido:

```bash
hermes chat "Responde en una frase: FishXCode está conectado"
```

Si la configuración ya funciona, también puedes cambiar a otro modelo configurado dentro de la sesión con `/model`.

## Verificar la configuración

Empieza con estas comprobaciones:

```bash
hermes doctor
```

```bash
hermes config check
```

```bash
hermes chat "Responde solo ok"
```

Si el último comando devuelve una respuesta normal, la integración con FishXCode está funcionando.

## Preguntas frecuentes

### ¿Por qué elegir `Custom endpoint`?

Porque Hermes trata cualquier API compatible con OpenAI como `provider: custom`. FishXCode encaja exactamente en ese modelo, así que no necesitas un adaptador específico para Hermes.

### ¿Por qué no funcionan `OPENAI_BASE_URL` o `LLM_MODEL`?

Hermes ha eliminado la compatibilidad con esas variables de entorno antiguas. Ahora el modelo, el proveedor y el endpoint se configuran desde `~/.hermes/config.yaml`.

### ¿Qué hago si la API key está configurada pero la autenticación sigue fallando?

Revísalo en este orden:

1. Asegúrate de que `base_url` sea `https://fishxcode.com/v1`
2. Asegúrate de que el token venga de la [Consola de FishXCode](https://fishxcode.com/console/token)
3. Asegúrate de que `model.default` sea un ID de modelo válido, como `gpt-5.4`
4. Asegúrate de que el contexto del modelo sea de al menos `65536`
5. Ejecuta `hermes config check` y `hermes doctor` para ver el error exacto

### ¿Puedo instalar Hermes directamente en Windows?

Sí, es posible instalarlo con PowerShell, pero WSL2 sigue siendo la opción más segura por compatibilidad y por ofrecer un flujo de trabajo tipo Unix más estable.
