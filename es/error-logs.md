---
title: Guia de registros de error
description: Campos, codigos de estado, errores comunes y flujo de diagnostico de los registros de error en la consola de FishXCode.
---

# Guia de registros de error

Los registros de error ayudan a encontrar la causa concreta de una solicitud fallida. Para diagnosticar un problema, revisa primero el [estado de salud de los grupos](/es/group-health) para entender el alcance y luego usa el `request_id` del registro para localizar la solicitud exacta.

Entrada: [Consola -> Registros de uso](https://fishxcode.com/console/log). Cambia el tipo de registro a **Registros de error** y filtra por periodo, modelo, Token, grupo, request ID, mensaje de error y codigo de estado.

::: tip Lectura rapida
- `request_id` localiza una solicitud concreta
- `status_code` agrupa errores por tipo
- `content` permite buscar palabras clave del error
- `group` ayuda a saber si el problema se concentra en un grupo
:::

## Campos

| Campo | Significado | Valor para diagnostico |
|-------|-------------|------------------------|
| Tiempo | Momento en que fallo la solicitud | Saber si el problema sigue ocurriendo |
| Modelo | Modelo usado por la solicitud | Saber si solo afecta a un modelo |
| Token | API Key o nombre de Token usado | Saber si solo afecta a un Token |
| Grupo | Paquete, modelo o grupo de recursos upstream | Saber si el fallo se concentra en un grupo |
| Request ID | Identificador unico de la solicitud, normalmente `request_id` | Dato prioritario al contactar soporte |
| Mensaje de error | Texto de la causa del fallo, normalmente incluye `content` | Buscar palabras clave concretas |
| Codigo de estado | Codigo HTTP o upstream, como `401`, `429`, `502` | Clasificar rapido el tipo de error |

## Orden de diagnostico

1. Copia el `request_id` desde la respuesta API o el registro y busca exactamente esa solicitud.
2. Si no tienes `request_id`, acota por periodo, modelo y Token.
3. Agrupa por codigo de estado, por ejemplo `401`, `413`, `429`, `502`, `503`.
4. Busca palabras clave del mensaje, como `Invalid API key`, `daily limit exceeded` o `Upstream request failed`.
5. Si el mismo error se concentra en un grupo, revisa el [estado de salud de los grupos](/es/group-health) para confirmar el alcance.

## Atribucion del problema

Los registros de error ayudan a distinguir si el problema viene del uso, del upstream o de la plataforma. No basta con mirar solo el codigo de estado: hay que combinarlo con el alcance.

| Atribucion | Criterio | Senales tipicas | Prioridad |
|------------|----------|-----------------|-----------|
| Uso | Afecta solo a una solicitud, un Token o una configuracion de cliente | Cuerpo de solicitud incorrecto, parametros incompatibles, Token mal copiado, contexto demasiado grande, endpoint incorrecto | Revisar configuracion local, request body, nombre de modelo, Token y version de herramienta |
| Upstream | Varias solicitudes concentran fallos upstream, timeouts de gateway o limites upstream | `Upstream request failed`, `all upstreams failed`, `bad response status code 502/524`, limite de cuenta upstream | Reintentar mas tarde, bajar concurrencia o cambiar de modelo/grupo si es necesario |
| Plataforma | Nodo, pool de recursos o canal de la plataforma no disponible | `system disk overloaded`, `No available accounts`, `No available channel`, `Service Unavailable` de plataforma | Reintentar mas tarde; si persiste, contactar soporte con grupo, periodo y `request_id` |
| Requiere revisar grupo | Una sola linea no basta para atribuir responsabilidad | Errores como `429`, `502`, `503`, que pueden tener varias causas | Revisar [estado de salud de los grupos](/es/group-health) para confirmar si esta concentrado |

::: warning Atencion
No atribuyas la causa solo por un `502` o `503` aislado. Una solicitud puede fallar por fluctuacion temporal del upstream; si muchas solicitudes del mismo grupo fallan en el mismo periodo, tiene mas sentido tratarlo como problema upstream o de recursos.
:::

## Codigos de estado

| Codigo | Significado habitual | Atribucion inicial | Prioridad |
|--------|----------------------|--------------------|-----------|
| `400` | Formato del request body, parametros o mensajes de herramienta invalidos | Uso | Revisar request body, endpoint y tool schema |
| `401` | Token invalido, deshabilitado o fallo de autenticacion | Uso | Copiar el Token de nuevo y confirmar su estado |
| `403` | Upstream rechaza la solicitud, normalmente por permisos o acceso al modelo | Uso o upstream | Revisar paquete, grupo y permisos de modelo |
| `413` | Request body demasiado grande | Uso | Reducir contexto, dividir archivos o comprimir imagenes |
| `429` | Limite de frecuencia, limite diario o credenciales en cooldown | Requiere revisar grupo | Bajar concurrencia y esperar recuperacion de cuota o cooldown |
| `500` | Error interno empaquetado por la capa intermedia o upstream | Depende del mensaje | Leer el mensaje completo para encontrar la causa real |
| `502` | Servicio upstream o red intermedia con error | Upstream o enlace | Reintentar mas tarde o cambiar modelo/grupo si hace falta |
| `503` | Servicio, canal, cuenta o recurso temporalmente no disponible | Upstream o plataforma | Reintentar mas tarde; contactar soporte si persiste |
| `504` / `521` / `522` / `524` | Timeout de conexion, lectura o respuesta de gateway | Upstream o enlace | Reducir tareas largas y reintentar mas tarde |

## Mensajes de error comunes

| Contenido del registro | Significado | Atribucion inicial | Accion sugerida |
|------------------------|-------------|--------------------|-----------------|
| `status_code=401, Invalid token` | Token invalido, mal copiado o expirado | Uso | Copiar el Token de nuevo desde la consola y confirmar que no tenga espacios extra |
| `status_code=401, Invalid API key or key is pending admin approval` | API Key invalida o Key nueva pendiente de revision/activacion | Uso o estado de cuenta | Confirmar que usas el Token mas reciente; si acabas de crearlo o cambiar paquete, esperar activacion y reintentar |
| `status_code=403, bad response status code 403` | Upstream rechaza la solicitud por permisos, estado de cuenta o acceso al modelo | Uso o upstream | Revisar Token, paquete, grupo y permisos de modelo; si persiste, cambiar modelo o contactar soporte con `request_id` |
| `status_code=413, openai_error` / `bad response status code 413` | Request body demasiado grande: contexto, archivos, imagenes o resultados de herramientas | Uso | Reducir contexto, subir menos contenido a la vez, dividir archivos o comprimir imagenes |
| `status_code=429, Account RPM limit exceeded` | La cuenta upstream alcanzo el limite de solicitudes por minuto | Uso o limite upstream | Bajar concurrencia y frecuencia de reintentos |
| `status_code=429, Account daily limit exceeded` | La cuenta upstream alcanzo el limite diario | Limite upstream | Esperar el reinicio diario o cambiar a otro modelo/grupo disponible |
| `status_code=429, All credentials for model ... are cooling down` | Todas las credenciales upstream del modelo estan en cooldown | Limite upstream | Bajar frecuencia de reintentos y esperar; para tareas urgentes, cambiar temporalmente de modelo |
| `status_code=500, auth_unavailable: no auth available` | El modelo o grupo no tiene recursos de autenticacion upstream disponibles temporalmente | Plataforma | Reintentar mas tarde; si persiste, cambiar modelo/grupo o contactar soporte |
| `status_code=502, Upstream request failed` / `bad response status code 502` / `all upstreams failed` | El servicio upstream o la red intermedia fallo, o todos los upstream disponibles fallaron | Upstream | Reintentar mas tarde; si persiste, cambiar modelo o contactar soporte con `request_id` |
| `status_code=502, openai_error` | Un upstream compatible con OpenAI devolvio error sin causa mas especifica | Upstream | Tratarlo como error upstream; si se reproduce siempre, reducir contexto y enviar `request_id` a soporte |
| `status_code=502, The origin web server returned an invalid or incomplete response to Cloudflare` | El origen upstream devolvio una respuesta invalida por Cloudflare | Upstream | Normalmente temporal; reintentar mas tarde |
| `status_code=500, upstream error: do request failed` | Fallo al enviar la solicitud al upstream, normalmente por red o indisponibilidad temporal | Upstream o enlace | Reintentar mas tarde; si persiste, enviar `request_id` a soporte |
| `status_code=520, bad response status code 520` | Cloudflare devolvio un error desconocido por respuesta upstream anomala o conexion interrumpida | Upstream o enlace | Reintentar mas tarde; si se concentra, tratarlo como incidente upstream |
| `status_code=521` / `522` / `504` / `524` | Timeout o error de conexion/lectura/respuesta entre Cloudflare, gateway o upstream | Upstream o enlace | Reintentar; si ocurre en tareas largas, reducir contexto, salida o cadena de herramientas |
| `status_code=503, Service Unavailable` | Servicio upstream no disponible o grupo sin recursos disponibles | Requiere revisar grupo | Reintentar mas tarde; si persiste, cambiar modelo/grupo o confirmar recursos con soporte |
| `status_code=503, system disk overloaded` | Un nodo de servicio esta protegiendose por uso alto de disco | Plataforma | Reintentar mas tarde; si persiste, contactar soporte |
| `status_code=503, No available accounts: no available accounts` | El grupo actual no tiene cuentas upstream disponibles | Plataforma | Cambiar modelo/grupo o reintentar mas tarde; si dura mucho, contactar soporte |
| `status_code=503, No available channel for model ... under group ...` | El grupo actual no tiene canal disponible para ese modelo | Uso o plataforma | Revisar nombre del modelo y soporte del paquete; cambiar a un modelo compatible |
| `status_code=503, model gpt-image-2 is only supported on /v1/images/generations and /v1/images/edits` | Un modelo de imagen se uso en el endpoint incorrecto | Uso | Enviar solicitudes de imagen al endpoint images correspondiente |
| `status_code=500, Image source is a local path that is not readable from this server` | La solicitud contiene una ruta local de imagen que el upstream no puede leer | Uso | En proyectos frontend, revisar archivos `lock`, quitar campos `png` anomalos o usar URL publica `http(s)` / payload base64 `data:image/...` |
| `status_code=500, failed to parse multipart form` | El cuerpo de subida de imagen o archivo no cumple el formato requerido | Uso | Revisar campos `multipart/form-data`, campo de archivo y headers; no escribir mal el boundary |
| `status_code=400, Invalid request: prompt is required` / `解析 Images 请求失败: prompt 不能为空` | Falta un `prompt` no vacio en imagenes | Uso | Completar `prompt` y confirmar que la solicitud va al endpoint de imagenes |
| `status_code=400, Unsupported parameter: messages` | Endpoint y parametros no coinciden, por ejemplo enviar `messages` a un endpoint incompatible | Uso | Revisar `base_url`, endpoint y tipo de modelo; ajustar el request body |
| `tool_use ids were found without tool_result blocks immediately after` | La secuencia de mensajes de herramientas no cumple el protocolo Claude | Uso | Mantener cada `tool_use` seguido por su `tool_result`; si lo genera una herramienta, actualizar cliente o reiniciar sesion |
| `Invalid schema for function ... None is not of type 'array'` | El schema de herramienta no cumple JSON Schema, normalmente por campos `parameters` o arrays vacios/tipo incorrecto | Uso | Revisar MCP/definicion de herramienta y usar `[]` en arrays vacios |
| `status_code=500, not implemented` | Endpoint, capacidad de modelo o ruta de tool call no implementada | Uso o plataforma | Confirmar endpoint/modelo soportado; cambiar modelo o contactar soporte si hace falta |

## Tratamiento por tipo

### Autenticacion y permisos

Revisa `401`, `403`, `Invalid API key`, `pending admin approval` y `No available channel`. Primero confirma que el Token viene de la consola, esta completo y tiene permisos para el modelo y grupo actuales.

### Formato de solicitud

Revisa `400`, `413`, `Unsupported parameter`, `prompt is required` e `Invalid schema`. Primero valida endpoint, campos del request body, tool schema, parametros de imagen y tamano de contexto.

### Limites y cuota

Revisa `429`, `Max 10/min`, `daily limit exceeded` y `cooling down`. No reintentes en bucle sin pausa; reduce concurrencia segun el limite del registro o espera recuperacion de cuota/credenciales.

### Upstream y recursos de plataforma

Revisa `502`, `503`, `504`, `520`, `521`, `522`, `524`, `Service Unavailable` y `system disk overloaded`. Reintenta mas tarde; si se concentra en un grupo, revisa el [estado de salud de los grupos](/es/group-health).

## Informacion para soporte

En la lista `console/log`, puedes copiar con un clic los detalles de diagnostico desde el mensaje de error. Cuando contactes soporte, aporta estos datos de una vez para que el equipo tecnico pueda investigar con menos idas y vueltas:

- ID de usuario
- Periodo: inicio y ultima aparicion del problema
- Grupo: `group`
- Modelo usado
- Codigo de estado, como `429`, `413`, `502`, `503`
- Contenido de error: `error_reasons.content`
- Request ID: `request_id` de un registro o de la respuesta API
- Alcance: un solo Token, modelo, grupo o varios grupos a la vez

::: tip Conclusion
Los registros de error explican por que fallo una solicitud concreta; el estado de salud de grupos muestra si el problema esta concentrado. Usarlos juntos acelera el diagnostico.
:::
