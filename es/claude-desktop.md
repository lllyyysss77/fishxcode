# Usar FishXCode con Claude Desktop

## Alcance

Esta guía cubre el nuevo modo **Cowork / Code con inferencia de terceros** en Claude Desktop, documentado por Anthropic como `third-party inference`.

Usa esta configuración si quieres:

- usar **Cowork** en Claude Desktop
- usar **Code** en Claude Desktop
- enrutar las solicitudes a través de FishXCode con una API o gateway personalizado

::: warning Nota
No es el flujo antiguo de "iniciar sesión y chatear". Cuando activas la inferencia de terceros, usarás sobre todo las pestañas **Cowork** y **Code**.
:::

## Requisitos previos

Prepara lo siguiente:

1. La última versión de Claude Desktop
2. Un token de FishXCode
3. Un token con acceso a modelos Claude

Obtén el token aquí:
- [https://fishxcode.com/console/token](https://fishxcode.com/console/token)

Modelos recomendados para empezar:

- `claude-sonnet-4-5-20250929`
- `claude-opus-4-5-20251101`
- `claude-haiku-4-5-20251001`

## Método 1: Configurar directamente en Claude Desktop (Recomendado)

Anthropic recomienda usar el flujo integrado de configuración de inferencia de terceros dentro de la app.

### 1. Activar Developer mode

En Claude Desktop, ve a:

```text
Help → Troubleshooting → Enable Developer mode
```

Después de activarlo, deberías ver entradas de menú `Developer`.

### 2. Abrir la configuración de inferencia de terceros

Luego entra en:

```text
Developer → Configure third-party inference
```

Esto abre el flujo oficial de configuración de Anthropic.

### 3. Elegir el modo Gateway

Selecciona:

```text
Gateway
```

FishXCode encaja en el modo `gateway` dentro de este flujo.

### 4. Rellenar la configuración de FishXCode

Usa estos valores:

- Gateway URL: `https://api.fishxcode.com`
- Authentication: `x-api-key`
- API Key: tu token de FishXCode

Si la interfaz pide una lista de modelos, empieza con:

```text
claude-sonnet-4-5-20250929
claude-opus-4-5-20251101
claude-haiku-4-5-20251001
```

Si hay una opción para habilitar `Code`, déjala activada.

### 5. Aplicar localmente

Al terminar, elige:

```text
Apply locally
```

Esto escribe la configuración en el archivo local de Claude Desktop del usuario actual.

Después reinicia Claude Desktop. En la mayoría de los casos verás entonces el espacio de trabajo **Cowork / Code**.

## Método 2: Gestionarlo mediante el archivo de configuración local (Avanzado)

Si quieres hacer copia de seguridad, migrar o depurar la configuración, puedes revisar el archivo local.

Rutas habituales:

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

::: tip Consejo
El archivo local guarda la configuración de inferencia de terceros bajo `enterpriseConfig`. Lo más seguro es configurarlo primero desde la app y luego hacer copia del resultado generado, en lugar de escribir el JSON a mano desde cero.
:::

## Valores recomendados para FishXCode

Si quieres revisar los campos clave manualmente, céntrate en estos:

| Campo | Valor recomendado |
| --- | --- |
| `inferenceProvider` | `gateway` |
| `inferenceGatewayBaseUrl` | `https://api.fishxcode.com` |
| `inferenceGatewayAuthScheme` | `x-api-key` |
| `inferenceGatewayApiKey` | tu token de FishXCode |
| `inferenceModels` | lista de modelos Claude |
| `isClaudeCodeForDesktopEnabled` | `true` |

Lista recomendada de modelos:

```text
claude-sonnet-4-5-20250929
claude-opus-4-5-20251101
claude-haiku-4-5-20251001
```

## Uso

Una vez configurado, puedes usar Claude Desktop para:

- abrir **Cowork** para tareas generales, documentos y colaboración sobre archivos
- abrir **Code** para programación, terminal y tareas de ingeniería

Si el selector de modelos es visible, empieza con:

- uso diario: `claude-sonnet-4-5-20250929`
- razonamiento profundo: `claude-opus-4-5-20251101`
- tareas ligeras y rápidas: `claude-haiku-4-5-20251001`

## Preguntas frecuentes

### ¿Por qué no encuentro `Enable Developer mode`?

Comprueba primero:

1. Que estés usando una versión reciente de Claude Desktop
2. Que estés mirando la barra de menús real del sistema y no solo los controles de la ventana

Si sigue sin aparecer tras actualizar, cierra completamente Claude Desktop y vuelve a abrirlo.

### ¿Qué hago si Windows muestra `Virtual Machine Platform not available`?

Si Claude Desktop muestra:

```text
Claude's workspace requires the Virtual Machine Platform on Windows. Enable this feature, then restart.
```

Significa que **Virtual Machine Platform** no está activado en Windows. Abre PowerShell como administrador y ejecuta:

```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

Cuando termine el comando, reinicia Windows y vuelve a abrir Claude Desktop.

### ¿Por qué no veo Cowork / Code después de configurarlo?

Revísalo en este orden:

1. El Developer mode está activado
2. Has completado `Configure third-party inference`
3. Elegiste `Gateway`
4. `gatewayUrl` es `https://api.fishxcode.com`
5. El token es válido y puede acceder a modelos Claude
6. Claude Desktop se reinició por completo tras la configuración

### ¿Por qué la Gateway URL no es `https://api.fishxcode.com/v1`?

Porque la inferencia de terceros de Claude Desktop usa una configuración de gateway al estilo Anthropic, no la ruta compatible con OpenAI `/v1/chat/completions`. Aquí debes usar la URL raíz del gateway:

```text
https://api.fishxcode.com
```

### ¿Qué hago si falla la autenticación después de configurar todo?

Comprueba primero:

1. que la autenticación esté configurada como `x-api-key`
2. que el campo API Key contenga directamente tu token de FishXCode
3. que el token siga siendo válido y tenga los permisos necesarios

### ¿Puedo editar el JSON local manualmente?

Sí, pero no es la mejor opción para la primera configuración. Los campos de inferencia de terceros evolucionan con las versiones de Claude Desktop, y el flujo integrado suele ser más estable y más fácil de recuperar si algo falla.
