# Tutorial de ZCF

[ZCF](https://github.com/UfoMiao/zcf) es una herramienta de configuración cero para Claude Code / Codex. Un solo comando `zcf init` gestiona la configuración de la API, importación de workflows e integración de servicios MCP.

> ¿Sin cuenta? Primero completa el [Registro de cuenta](/es/account) para obtener tu API Key.

---

## Parámetros de uso frecuente

### Configuración de API

| Parámetro | Descripción |
|---|---|
| `-p custom` | Usar proveedor personalizado (FishXCode usa este modo) |
| `-t api_key` | Equivalente a `-p custom`, mismo efecto |
| `-k "sk-xxx"` | API Key |
| `-u "https://api.fishxcode.com/"` | URL base de FishXCode |
| `-M "claude-sonnet-4-5-20250929"` | Especificar modelo principal |
| `-H "claude-haiku-4-5-20251001"` | Especificar modelo rápido |

Ejemplo con modelos específicos:

```bash
npx zcf i -s -t api_key -k "sk-xxx" -u "https://api.fishxcode.com/" \
  -M "claude-sonnet-4-5-20250929" \
  -H "claude-haiku-4-5-20251001"
```

### Workflows

| Parámetro | Descripción |
|---|---|
| `-w all` | Instalar todos los workflows |
| `-w sixStepsWorkflow,gitWorkflow` | Instalar workflows específicos |
| `-w skip` | Omitir |

Workflows disponibles: `commonTools` / `sixStepsWorkflow` / `featPlanUx` / `gitWorkflow` / `bmadWorkflow`

### Servicios MCP

| Parámetro | Descripción |
|---|---|
| `-m all` | Instalar todos los MCP |
| `-m context7,open-websearch` | Instalar servicios específicos |
| `-m skip` | Omitir |

Servicios disponibles: `context7` / `open-websearch` / `spec-workflow` / `mcp-deepwiki` / `Playwright` / `exa` / `serena`

### Otras opciones

| Parámetro | Descripción |
|---|---|
| `-g es` | Establecer interfaz, plantillas y salida de IA en español |
| `-s` | Modo no interactivo, omite todos los prompts |
| `-r backup` | Estrategia de configuración (`backup` / `merge` / `docs-only` / `skip`) |
| `-x false` | No instalar la barra de estado CCometixLine |

---

## Comandos de workflow

Tras la instalación, puedes usar los siguientes comandos de barra en Claude Code:

| Comando | Descripción |
|---|---|
| `/zcf:workflow` | Workflow de desarrollo en seis fases (Investigar→Idear→Planificar→Ejecutar→Optimizar→Revisar) |
| `/zcf:feat` | Desarrollo de nuevas funcionalidades con planificación y diseño UI/UX |
| `/zcf:git-commit` | Generación automática de mensajes de commit Git |
| `/zcf:git-rollback` | Rollback interactivo a versiones anteriores |
| `/zcf:git-worktree` | Gestión de Git Worktrees |
| `/zcf:bmad-init` | Proceso de desarrollo ágil empresarial |

---

## Descripción de servicios MCP

MCP (Model Context Protocol) permite que Claude Code acceda a herramientas y servicios externos. ZCF incluye los siguientes servicios:

| Servicio | Descripción | Key requerida |
|---|---|---|
| `context7` | Consulta documentación y ejemplos de código actualizados de bibliotecas | No |
| `open-websearch` | Búsqueda web con DuckDuckGo/Bing/Brave | No |
| `spec-workflow` | Workflow estructurado de Requisitos→Diseño→Implementación | No |
| `mcp-deepwiki` | Consulta de documentación de repositorios GitHub | No |
| `Playwright` | Automatización del navegador | No |
| `exa` | Búsqueda semántica Exa AI | Sí (`EXA_API_KEY`) |
| `serena` | Asistente IDE con recuperación semántica de código | No |

### Descripción de cada servicio

**context7** — Consulta documentación y ejemplos de código actualizados de cualquier biblioteca, evita que la IA use APIs obsoletas:
```
Consulta la documentación más reciente y ejemplos del hook useState de React
```

**open-websearch** — Soporta múltiples motores de búsqueda: DuckDuckGo, Bing y Brave, sin necesidad de API Key:
```
Busca las novedades más recientes de TypeScript 5.0
```

**spec-workflow** — Workflow estructurado de requisitos a implementación, con análisis de requisitos, diseño técnico y desglose de tareas:
```bash
npx -y @pimzino/spec-workflow-mcp@latest --dashboard  # Inicia el panel de visualización
```

**mcp-deepwiki** — Consulta la documentación de repositorios GitHub:
```
Consulta la documentación de la Composition API del repositorio vuejs/core
```

**Playwright** — Controla el navegador para capturas de pantalla, relleno de formularios y simulación de interacciones. Requiere descargar el navegador en el primer uso.

**exa** — Búsqueda semántica en la web con Exa AI, requiere configurar API Key:
```bash
export EXA_API_KEY="your-api-key"  # Obtén en dashboard.exa.ai
```

**serena** — Recuperación semántica de código y sugerencias de edición inteligentes, con capacidades similares a un IDE.

### Ubicación del archivo de configuración

Tras la instalación de ZCF, la configuración MCP se escribe en `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@context-labs/context7"]
    },
    "open-websearch": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-open-websearch"]
    }
  }
}
```

::: tip Usuarios de Windows
ZCF corrige automáticamente los formatos de rutas de Windows. Si tienes problemas de conexión MCP, ejecuta `npx zcf` → selecciona `4. Configurar MCP` para reparar automáticamente.
:::

---

## Actualización

```bash
npx zcf update
# o
npx zcf u -s -g es
```

La actualización solo sincroniza las plantillas de workflows y los prompts, **no modifica la configuración de API**.

---

## Preguntas frecuentes

### ¿Se sobrescribirá mi settings.json existente?

ZCF realiza una copia de seguridad automática en `~/.claude/backup/YYYY-MM-DD_HH-mm-ss/` antes de cualquier modificación, y ofrece cuatro estrategias de manejo:

```bash
npx zcf i -s -r backup    # Copia de seguridad y sobreescritura (por defecto)
npx zcf i -s -r merge     # Fusión inteligente
npx zcf i -s -r docs-only # Solo actualizar documentos de workflows
npx zcf i -s -r skip      # Omitir sin modificar
```

### ¿Cómo volver a la configuración manual?

El `settings.json` generado por ZCF tiene formato estándar y puede editarse directamente. Consulta la [Configuración de Claude Code](/es/start).

### Requisito de versión de Node.js

ZCF requiere Node.js >= 18:

```bash
node --version
```
