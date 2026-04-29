# Preguntas frecuentes

## General

### Que es FishXCode?

FishXCode es una estacion de retransmision de AI Coding que soporta los modelos Claude y Codex en multiples plataformas.

### Que herramientas son compatibles?

Herramientas disponibles actualmente:

- **Claude Code** — CLI oficial de Anthropic
- **OpenAI Codex** — Asistente de programacion de OpenAI
- **RooCode** — Extension de IA para VS Code
- **Qwen Code** — Herramienta de programacion basada en Qwen de Alibaba
- **Droid CLI** — CLI ligero para programacion con IA
- **OpenCode** — Herramienta terminal de codigo abierto para programacion con IA

### Como se protegen mis datos?

FishXCode funciona unicamente como un servicio de retransmision de API. Tu codigo y tus conversaciones no se almacenan en nuestros servidores. Todas las solicitudes se envian directamente a los proveedores de modelos correspondientes.

::: tip Recomendacion
Para proyectos sensibles, te sugerimos revisar la politica de privacidad de cada proveedor de modelos antes de usar el servicio.
:::

## Cuenta y Token

### Como me registro?

Visita [fishxcode.com/register](https://fishxcode.com/register?aff=9CTW) y sigue las instrucciones para crear tu cuenta.

### Como obtengo un token de API?

Despues de iniciar sesion, ve a la [pagina de gestion de tokens](https://fishxcode.com/console/token) en la consola para generar un nuevo token.

### Cuanto dura un token?

Los tokens permanecen activos hasta que los elimines o regeneres manualmente. Te recomendamos rotarlos periodicamente por seguridad.

### Cuales son los limites de la cuota de uso?

Cada usuario recibe una cuota de uso. La cantidad exacta se ajusta dinamicamente segun los recursos de la plataforma. Consulta tu consola para ver el consumo actual.

::: warning Aviso
Cuando se agote tu cuota, las solicitudes seran rechazadas. Planifica tu uso de forma adecuada.
:::

## Configuracion de herramientas

### Mis variables de entorno no funcionan

Revisa estos problemas habituales:

1. **Terminal sin reiniciar** — Tras editar `.bashrc` / `.zshrc`, ejecuta `source ~/.bashrc` o abre una nueva terminal
2. **Error en el nombre de la variable** — Los nombres distinguen entre mayusculas y minusculas: `ANTHROPIC_BASE_URL` no es lo mismo que `Anthropic_Base_Url`
3. **Espacios o comillas de mas** — Asegurate de no incluir espacios extra en `export KEY="value"`

::: details Comandos de diagnostico rapido
```bash
# Verificar que las variables estan definidas
echo $ANTHROPIC_BASE_URL
echo $ANTHROPIC_AUTH_TOKEN

# Si la salida esta vacia, la variable no esta configurada
```
:::

### Donde estan los archivos de configuracion?

| Herramienta | Ubicacion |
|-------------|-----------|
| Claude Code | Variables de entorno |
| Codex | `~/.codex/config.toml` y `~/.codex/auth.json` |
| RooCode | Configuracion JSON de VS Code |
| Qwen Code | Variables de entorno |

### No puedo conectarme a FishXCode

1. Confirma que `BASE_URL` este configurado como `https://fishxcode.com/` (ojo con la `/` al final)
2. Verifica que `fishxcode.com` sea accesible desde tu red
3. Si estas detras de un proxy corporativo, revisa la configuracion del proxy

## Seleccion de modelo

### Como elijo el modelo adecuado?

Elige segun tu caso de uso:

| Caso de uso | Modelo recomendado | Motivo |
|-------------|-------------------|--------|
| Programacion diaria | `claude-sonnet-4-5-20250929` | Buen equilibrio entre capacidad y velocidad |
| Completados rapidos | `claude-3-5-haiku-20241022` | Tiempos de respuesta cortos |
| Arquitectura compleja | `claude-sonnet-4-5-20250514` | Gran capacidad de razonamiento |

### Cuales son las diferencias entre los modelos?

- **Serie Sonnet** — Versatil, ideal para la mayoria de tareas de programacion
- **Serie Haiku** — Ligera y rapida, pensada para completados simples y formateo
- Para comparaciones detalladas, consulta la documentacion oficial de cada proveedor

### Como cambio de modelo?

Configura la variable de entorno `ANTHROPIC_MODEL`:

```bash
export ANTHROPIC_MODEL=claude-sonnet-4-5-20250929
```

## Solucion de problemas

### Error de autenticacion (Auth Error)

::: warning Causas comunes
- El token contiene espacios extra o esta incompleto
- El token fue eliminado o regenerado
- `API_KEY` y `AUTH_TOKEN` deben estar configurados a la vez
:::

Solucion: Ve a la [consola](https://fishxcode.com/console/token), verifica el estado de tu token y copialo de nuevo.

### Como leer los registros de error?

Ve a [Consola -> Registros de uso](https://fishxcode.com/console/log), cambia el tipo a **Registros de error** y filtra por periodo, modelo, token, grupo, request ID, mensaje de error o codigo de estado.

::: tip Consejos de diagnostico
- Copia primero el `request_id` desde la respuesta API o el registro y busca exactamente esa solicitud
- Usa el filtro de mensaje de error para buscar palabras clave en `content`, como `Invalid token` o `Upstream request failed`
- Usa el codigo de estado para agrupar problemas rapidamente, como `401`, `429`, `502`, `503` o `524`
:::

Mensajes de error comunes:

| Contenido del registro | Significado | Que hacer |
|------------------------|-------------|-----------|
| `status_code=401, Invalid token` | El token no es valido, se copio mal o expiro | Copia el token de nuevo desde la consola y elimina espacios extra |
| `status_code=429, Account RPM limit exceeded` | La cuenta upstream alcanzo el limite de solicitudes por minuto | Reduce concurrencia y frecuencia de reintentos, luego intenta mas tarde |
| `status_code=502, Upstream request failed` / `bad response status code 502` | El servicio upstream o la red devolvio un error | Reintenta mas tarde; si persiste, cambia de modelo o contacta soporte con el `request_id` |
| `status_code=502, The origin web server returned an invalid or incomplete response to Cloudflare` | El origen upstream devolvio una respuesta invalida a traves de Cloudflare | Normalmente es temporal del upstream; reintenta mas tarde |
| `status_code=500, upstream error: do request failed` | La solicitud fallo al enviarse al servicio upstream, normalmente por red o indisponibilidad temporal | Reintenta mas tarde; si persiste, envia el `request_id` a soporte |
| `status_code=520, bad response status code 520` | Cloudflare devolvio un error desconocido, normalmente por respuesta upstream anomala o conexion interrumpida | Reintenta mas tarde; si aparece con frecuencia, tratalo como incidente upstream |
| `status_code=524` / `bad response status code 524` | La respuesta upstream supero el timeout de lectura de 120 segundos de Cloudflare | Reduce el contexto o la longitud de salida |
| `status_code=503, model gpt-image-2 is only supported on /v1/images/generations and /v1/images/edits` | Un modelo de imagen se llamo desde el endpoint incorrecto | Envia solicitudes de imagen al endpoint images correspondiente |
| `status_code=500, Image source is a local path that is not readable from this server` | La solicitud uso una ruta local de imagen que el servidor no puede leer | Usa una URL `http(s)` publica o payload base64 `data:image/...` |

### Tiempo de espera agotado (Timeout)

Posibles causas:
1. Alta latencia de red
2. Contexto de entrada muy grande que requiere mayor tiempo de procesamiento
3. El servicio esta bajo carga elevada

Intenta reducir el contexto de entrada o vuelve a intentarlo en unos minutos.

### Limite de frecuencia alcanzado (429)

Un codigo `429` indica que estas enviando solicitudes con demasiada frecuencia.

::: tip Como solucionarlo
- Reduce la frecuencia y espera unos segundos entre solicitudes
- Evita llamar a la API en bucles sin pausas
- Asegurate de que ningun otro proceso este usando el mismo token al mismo tiempo
:::

### Modelo no disponible

Verifica que el nombre del modelo sea correcto. Consulta la lista de modelos recomendados en [Comenzar](/es/start). Algunos modelos pueden no estar disponibles aun en FishXCode.
