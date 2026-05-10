---
title: Estado de salud de los grupos
description: Estado de salud de grupos en FishXCode, detalles de tokens de equipo, columnas de exportacion y flujo de diagnostico.
---

# Estado de salud de los grupos

El estado de salud de los grupos sirve para saber si una anomalia es de una solicitud aislada o si se concentra en un paquete, modelo, grupo upstream o miembro del equipo. Los administradores de empresa y equipo pueden usarlo para responder rapido tres preguntas:

- Que grupo tiene menor tasa de exito en el periodo seleccionado
- Que usuario o token concentra mas solicitudes, consumo o errores
- Si el error esta concentrado en un solo Token o ya afecta a todo el grupo

Al diagnosticar errores de API, revisa primero el estado del grupo y luego entra al registro individual para ubicar el `request_id`.

::: info Alcance de los datos
La pagina publica `status` incrustada aqui consulta el estado de salud de los grupos usados por todos los usuarios de FishXCode durante el periodo seleccionado. Refleja la disponibilidad global de los grupos de la plataforma y es en tiempo real, imparcial y estable.

La vista **Registros de uso -> Estado de salud de grupos** dentro de la consola muestra los datos visibles segun los permisos de la cuenta actual. Un usuario personal normalmente solo ve sus propios tokens; los administradores de empresa y equipo pueden revisar el uso por usuario, nombre de usuario, token y grupo.
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

Esta es una captura de ejemplo de **Registros de uso -> Estado de salud de grupos** en la consola. Muestra tasa de exito, numero de solicitudes, consumo, cache, tiempo medio, ultima solicitud y causas de fallo.

![Ejemplo de estado de salud de grupos en consola](/img/group-health.png)

::: tip Principio de uso
Primero decide el alcance y luego trata el error individual. Un registro concreto sirve para ubicar una solicitud; el estado de salud de grupos sirve para saber si el problema esta concentrado.
:::

Para explicar mensajes de error individuales, consulta la [guia de registros de error](/es/error-logs).

## Columnas de la lista

La lista de la consola y la exportacion CSV usan las mismas columnas visibles. La lista contiene dos tipos de fila:

- **Fila de grupo**: resume la salud general de un grupo en el periodo seleccionado.
- **Fila de token**: muestra detalles de usuario y token bajo un grupo, util para que administradores de empresa y equipo ubiquen miembros, proyectos o servicios.

| Columna visible | Aplica a | Descripcion | Uso recomendado |
|-----------------|----------|-------------|-----------------|
| Tipo | Fila de grupo, fila de token | Indica si la fila es resumen `Grupo` o detalle `Token` | Mira primero las filas de grupo y despues las de token para ubicar miembro o Token |
| Grupo | Fila de grupo, fila de token | Grupos que aparecieron en el periodo seleccionado, incluidos grupos por uso, grupos de paquete, grupo por defecto o grupo de modelo | Saber si el problema se concentra en un paquete, modelo o pool upstream |
| ID de usuario | Fila de token | ID del usuario que uso el token | Ubicar la cuenta del miembro en diagnosticos empresariales |
| Nombre de usuario | Fila de token | Nombre del usuario que uso el token | Reportes de equipo, comunicacion con miembros y revision de permisos |
| Token | Fila de token | Nombre del Token configurado en la consola | Saber si la anomalia esta aislada a un Token |
| Tasa de exito | Fila de grupo, fila de token | Tasa de exito = solicitudes correctas / total de solicitudes | Conviene revisarla si baja de 80 %; si es claramente menor que otras filas, revisar primero ese grupo o token |
| Solicitudes | Fila de grupo, fila de token | Total de solicitudes en el periodo seleccionado | No sobreinterpretar la tasa de exito si la muestra es pequena |
| Exito | Fila de grupo, fila de token | Solicitudes correctas que devolvieron 2xx | Leerlo junto con Solicitudes y Errores para estimar disponibilidad |
| Errores | Fila de grupo, fila de token | Solicitudes con error (4xx/5xx) | Si sube, revisar primero Causa de fallo y registros de error |
| Consumo | Fila de grupo, fila de token | Consumo acumulado de cuota/costo en el periodo, exportado con formato monetario de la consola | Costeo de equipo, reparto por proyecto y deteccion de consumo anomalo |
| Tasa de cache | Fila de grupo, fila de token | Tasa de cache = tokens con hit de cache / total de tokens | Cuanto mas alta, mas ahorro; la parte con cache suele cobrarse mas barata o gratis |
| Tokens cache | Fila de grupo, fila de token | Numero de tokens que hicieron hit de cache en el periodo | Esta parte suele cobrarse con alto descuento; cuanto mas, mayor ahorro |
| Solicitudes cache | Fila de grupo, fila de token | Numero de solicitudes que hicieron hit de cache al menos una vez | Mide cuantas solicitudes usaron cache realmente |
| Proporcion solicitudes cache | Fila de grupo, fila de token | Proporcion solicitudes cache = solicitudes con cache / solicitudes totales | Cuanto mas alta, mas llamadas reciben descuento por cache |
| Tokens cache medios | Fila de grupo, fila de token | Promedio de tokens por hit de cache | Comparar eficiencia de reutilizacion entre miembros, servicios o grupos |
| Tiempo medio | Fila de grupo, fila de token | Tiempo medio por solicitud, en segundos | Cuanto mas bajo, mas rapido responde el upstream; si sube, revisar contexto largo, salida larga y herramientas |
| Hora de inicio | Fila de grupo, fila de token | Primera aparicion de este grupo o token en el periodo actual | Ubicar inicio del problema o del trafico |
| Ultima solicitud | Fila de grupo, fila de token | Aparicion mas reciente de este grupo o token en el periodo actual | Saber si el problema o trafico continua |
| Causa de fallo | Fila de grupo | Principales causas de fallo por frecuencia, con codigo de estado y recuento; vacio o `-` si no hay errores | Priorizar el error mas repetido, no solo el ultimo registro |

::: info Fuente de campos
Las columnas visibles se generan desde estadisticas agregadas. Para uso diario, toma como referencia la lista de consola y las columnas CSV; solo mapealas a nombres tecnicos de campos al integrar una API o hacer diagnostico tecnico.
:::

::: tip Diagnostico de equipo
Mira primero las filas de grupo para decidir si es un problema del pool de recursos, y despues las filas de token para ver si lo causa un usuario o Token. Si la tasa de exito del grupo es normal pero un token tiene muchos errores, revisa primero el Token, nombre de modelo, configuracion del cliente o request body de ese miembro.
:::

## Exportacion CSV

La exportacion CSV usa las mismas columnas que la lista actual. Es util para reportes semanales, reparto de costos, revisiones de incidentes y conciliacion de uso por miembro.

Despues de exportar, puedes previsualizar el archivo con el [visor CSV en linea](https://tools.beer/zh/csv/viewer/). Permite arrastrar o seleccionar un archivo CSV, y tambien pegar texto CSV, util para revisar rapido columnas y causas de fallo.

| Comportamiento de exportacion | Descripcion |
|-------------------------------|-------------|
| Fila de grupo | `Tipo` es `Grupo`; ID de usuario, nombre de usuario y Token suelen estar vacios, representando el resumen del grupo |
| Fila de token | `Tipo` es `Token`; muestra ID de usuario, nombre de usuario y Token, representando detalle de miembro o Token bajo el grupo |
| Formato monetario | `Consumo` usa el formato monetario de la consola, por ejemplo `¥905.48` |
| Formato porcentual | Tasa de exito, tasa de cache y proporcion de solicitudes cache se exportan como porcentajes |
| Formato numerico | Los numeros grandes pueden llevar separadores de miles, utiles para lectura directa o importacion a hojas de calculo |
| Formato de tiempo | Hora de inicio y Ultima solicitud se exportan como hora local para alinearlas con el incidente |
| Causa de fallo | Varios errores frecuentes se combinan con su recuento al final; vacio o `-` si no hay errores |

## Flujo de diagnostico

### 1. Determina el alcance

Primero mira las filas donde `Tipo=Grupo`. Si la Tasa de exito esta cerca de lo normal y Errores es bajo, suele ser un error aislado. Copia el `request_id` de la solicitud y sigue el diagnostico en registros.

Si la Tasa de exito de un grupo es claramente menor que la de otros grupos, o Errores sube de forma concentrada, diagnostica por grupo: modelo, Token, cuenta upstream, permisos de paquete y recursos de plataforma.

En escenarios de empresa o equipo, mira despues las filas `Tipo=Token` dentro de ese grupo. Si solo un usuario o token es anormal, revisa primero la configuracion del cliente, Token, nombre de modelo, request body y estrategia de concurrencia de ese miembro.

### 2. Revisa las principales causas de fallo

Causa de fallo normalmente muestra causas ordenadas por frecuencia. Empieza por la mas frecuente y despues revisa las de menor volumen. Los errores frecuentes explican mejor el fallo principal del periodo.

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

### 4. Combina consumo y cache

| Sintoma | Causa mas probable | Accion sugerida |
|---------|--------------------|-----------------|
| Consumo claramente mayor que otros tokens del mismo grupo | Contexto grande, salida larga, llamadas frecuentes o tareas repetidas | Combinar Solicitudes, Tiempo medio y registros de error para ubicar servicio o miembro |
| Tasa de cache alta pero Proporcion solicitudes cache baja | Pocas solicitudes grandes hacen hit de cache | Revisar si solo tareas fijas reutilizan contexto |
| Proporcion solicitudes cache alta pero Tokens cache medios bajos | Muchas solicitudes hacen hit, pero cada una ahorra poco | Revisar si el contexto es demasiado corto o el contenido cache es inestable |
| Un token tiene Tiempo medio claramente alto | Tareas de cliente pesadas, contexto largo, salida larga o upstream lento | Comparar Solicitudes, cache, Causa de fallo y registros individuales de ese token |

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
