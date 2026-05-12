# Usar FishXCode con Codex

## Tutorial en video

<VideoPlayer src="https://www.hi168.com/api/v2/s3/api/public/share/dl/992402691ac054896144c542cd7003e5" />

## Instalación

::: code-group

```bash [pnpm]
pnpm install -g @openai/codex
```

```bash [npm]
npm install -g @openai/codex
```

```bash [yarn]
yarn global add @openai/codex
```

```bash [bunx]
bunx --global @openai/codex
```

:::

## Configurar Variables de Entorno

1. Visita [https://fishxcode.com/console/token](https://fishxcode.com/console/token) para obtener tu API Key
2. Crea `~/.codex/config.toml`:

   ```toml
   model = "gpt-5.3-codex"
   model_provider = "fishxcode"
   preferred_auth_method = "apikey"

   [model_providers.fishxcode]
   name = "OpenAI using Chat Completions"
   base_url = "https://fishxcode.com/v1"
   wire_api = "responses"
   query_params = {}
   stream_idle_timeout_ms = 300000
   ```

3. Crea `~/.codex/auth.json`, estableciendo `OPENAI_API_KEY` con tu API Key de FishXCode:

   ```json
   {
     "OPENAI_API_KEY": "tu_api_key"
   }
   ```

## Lanzamiento Directo

```bash
cd mi-proyecto
codex
```

## Usar Codex en VSCode

1. Instala la extensión Codex
2. Accede a configuración en modo JSON
3. Agrega:

```json
{
  "chatgpt.apiBase": "https://fishxcode.com/v1",
  "chatgpt.config": {
    "preferred_auth_method": "api_key",
    "model_provider": "fishxcode"
  }
}
```
