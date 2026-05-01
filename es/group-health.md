---
title: Estado de salud de los grupos
description: Estado de salud de grupos en FishXCode, campos, categorias de error y flujo de diagnostico.
---

# Estado de salud de los grupos

El estado de salud de los grupos sirve para saber si una anomalia es de una solicitud aislada o si se concentra en un paquete, modelo o grupo upstream. Al diagnosticar errores de API, revisa primero el estado del grupo y luego entra al registro individual para ubicar el `request_id`.

::: info Alcance de los datos
La pagina publica `status` incrustada aqui consulta el estado de salud de los grupos usados por todos los usuarios de FishXCode durante el periodo seleccionado. Refleja la disponibilidad global de los grupos de la plataforma y es en tiempo real, imparcial y estable.

La vista **Registros de uso -> Estado de salud de grupos** dentro de la consola solo cuenta las solicitudes del usuario actual. Usala para diagnosticar tus propios tokens, modelos y solicitudes.
:::

<iframe
  src="https://status.fishxcode.com/group/global?view=list&sort=group&window=24h"
  title="Estado de salud de grupos FishXCode"
  loading="lazy"
  referrerpolicy="no-referrer-when-downgrade"
  style="width: 100%; height: 720px; border: 1px solid var(--vp-c-divider); border-radius: 8px; background: var(--vp-c-bg-soft);"
></iframe>

Si la pagina de estado no carga correctamente arriba, abre directamente [Estado de salud de grupos FishXCode](https://status.fishxcode.com/group/global?view=list&sort=group&window=24h).

Entrada de consola: [Consola -> Registros de uso](https://fishxcode.com/console/log). En registros de error o vistas estadisticas, filtra por periodo, modelo, Token, grupo, mensaje de error y codigo de estado.

## Ejemplo de consola

Esta es una captura de ejemplo de **Registros de uso -> Estado de salud de grupos** en la consola, con periodo de 6 de abril de 2026 al 1 de mayo de 2026. Muestra tasa de exito, numero de solicitudes, tiempo medio, ultima solicitud y causas de fallo del grupo global.

![Ejemplo de estado de salud de grupos en consola](/img/group-health.png)

::: tip Principio de uso
Primero decide el alcance y luego trata el error individual. Un registro concreto sirve para ubicar una solicitud; el estado de salud de grupos sirve para saber si el problema esta concentrado.
:::

Para explicar mensajes de error individuales, consulta la [guia de registros de error](/es/error-logs).

## Campos

| Campo | Significado | Valor para diagnostico |
|-------|-------------|------------------------|
| `group` | Grupo de la solicitud, como paquete, grupo por defecto o grupo de modelo | Saber si el problema se concentra en un paquete, modelo o pool upstream |
| `total_count` | Total de solicitudes en el periodo actual | Confirmar que la muestra es suficiente y no dejarse guiar por pocos casos |
| `success_count` | Solicitudes correctas | Compararlo con `total_count` para estimar disponibilidad |
| `error_count` | Solicitudes fallidas | Si sube de forma continua, revisar primero `error_reasons` |
| `success_rate` | Tasa de exito | Una tasa claramente baja suele indicar problema concentrado en el grupo |
| `avg_use_time` | Tiempo medio en segundos | Si sube, revisar contexto largo, salida larga, cadena de herramientas y lentitud upstream |
| `quota` | Cuota o estadistica de cuota del grupo | Cruzarlo con paquete y saldo de consola para ver si se acerca al limite |
| `tokens` | Consumo de Token en el periodo actual | Detectar consumo anomalo o solicitudes con contexto grande |
| `first_seen_at` | Primera aparicion dentro del periodo | Ubicar el inicio del problema |
| `last_seen_at` | Ultima aparicion dentro del periodo | Saber si el problema continua |
| `error_reasons` | Causas de error frecuentes y recuentos | Priorizar el error mas repetido, no solo el ultimo registro |

## Flujo de diagnostico

### 1. Revisa tasa de exito y errores

Si `success_rate` esta cerca de lo normal y `error_count` es bajo, suele ser un error aislado. Copia el `request_id` de la solicitud y sigue el diagnostico en registros.

Si la `success_rate` de un grupo es claramente menor que la de otros grupos, o `error_count` sube de forma concentrada, diagnostica por grupo: modelo, Token, cuenta upstream, permisos de paquete y recursos de plataforma.

### 2. Revisa las causas Top

`error_reasons` normalmente muestra causas ordenadas por frecuencia. Empieza por la mas frecuente y despues revisa las de menor volumen. Los errores frecuentes explican mejor el fallo principal del periodo.

| Tipo de error | Palabras clave | Atribucion inicial | Primera comprobacion |
|---------------|----------------|--------------------|----------------------|
| Limite de frecuencia | `Account RPM limit exceeded`, `Max 10/min`, `Max 5/min` | Uso o limite upstream | Concurrencia o solicitudes por minuto demasiado altas |
| Limite diario | `Account daily limit exceeded` | Limite upstream | La cuenta upstream agoto la cuota diaria |
| Credenciales en cooldown | `All credentials ... are cooling down` | Limite upstream | Todas las credenciales upstream del modelo estan en cooldown |
| Request body grande | `status_code=413`, `openai_error` | Uso | Contexto, archivo, imagen o resultado de herramienta demasiado grande |
| Permisos o autenticacion | `401`, `403`, `Invalid API key`, `pending admin approval` | Uso o estado de cuenta | Token, paquete, grupo o permisos de modelo anormales |
| Sin recursos disponibles | `No available accounts`, `No available channel`, `auth_unavailable` | Plataforma o configuracion | El grupo no tiene cuenta, canal o recurso de autenticacion disponible |
| Error upstream | `502`, `all upstreams failed`, `Upstream request failed` | Upstream | Servicio upstream o red intermedia con error |
| Timeout de gateway | `504`, `521`, `522`, `524` | Upstream o enlace | Timeout de conexion, lectura o respuesta upstream |
| Proteccion de plataforma | `system disk overloaded`, `Service Unavailable` | Plataforma | Nodo o recurso upstream temporalmente no disponible |
| Formato de imagenes | `gpt-image-2`, `prompt is required`, `multipart form` | Uso | Endpoint de imagen, prompt o formato de subida incorrecto |
| Formato de herramientas | `tool_use`, `tool_result`, `Invalid schema` | Uso | Mensajes de herramienta o JSON Schema del cliente incorrectos |

### 3. Trata segun alcance

| Sintoma | Causa mas probable | Accion sugerida |
|---------|--------------------|-----------------|
| Solo falla un Token | Configuracion de Token, permisos o formato local | Copiar Token de nuevo, revisar cliente y request body |
| Solo falla un modelo | Permisos, canal del modelo o recursos upstream del modelo | Cambiar a modelo similar y confirmar que el paquete lo soporta |
| Solo un grupo tiene baja tasa de exito | Pool del grupo, permisos de paquete o cuenta upstream | Cambiar grupo/modelo; al contactar soporte, aportar grupo y periodo |
| Varios grupos muestran `502`, `504`, `521`, `522`, `524` | Problema upstream o de red | Reintentar mas tarde y reducir tareas largas; contactar soporte si persiste |
| Varias solicitudes muestran `413` | Request body demasiado grande | Reducir contexto, dividir archivos, comprimir imagenes o reducir resultados de herramientas |
| Varias solicitudes muestran `429` | Frecuencia alta, cuota diaria agotada o credenciales en cooldown | Bajar concurrencia; distinguir en el registro entre RPM, daily limit y cooling down |

## Informacion para soporte

Para problemas simples, consulta primero la [guia de registros de error](/es/error-logs) y el [estado de salud de los grupos](/es/group-health). Si el problema sigue sin resolverse, abre los detalles del registro de error en `console/log` y haz clic en el icono de copiar para copiar los detalles de diagnostico en un clic. Cuando contactes soporte, aporta estos datos de una vez para que el equipo tecnico pueda investigar con menos idas y vueltas:

- ID de usuario
- Periodo: inicio y ultima aparicion del problema
- Grupo: `group`
- Modelo usado
- Codigo de estado, como `429`, `413`, `502`, `503`
- Contenido de error: `error_reasons.content`
- Request ID: `request_id` de un registro o de la respuesta API
- Alcance: un solo Token, modelo, grupo o varios grupos a la vez

::: tip Resumen rapido
`401` / `403` suelen apuntar a permisos, `413` a tamano del request body, `429` a frecuencia o cuota, `502` / `504` / `524` a upstream o tareas largas, y `503` a recursos temporalmente no disponibles.
:::
