---
title: Registro de cuenta y gestión
description: Registro en FishXCode, obtención de API Key, recarga y seguridad de cuenta.
---

# Registro de cuenta y gestión

> ¿Ya tienes cuenta? Ve directamente a [Comenzar](/es/start) para configurar tus herramientas.
> ¿Explorar toda la plataforma? Consulta la [Guía de plataforma](/es/platform).

## 1. Obtener una API Key

### 1. Registrarse

Visita [fishxcode.com](https://fishxcode.com/register?aff=9CTW) y haz clic en **Registrarse**:

![Página de inicio de FishXCode](/img/start/api-01-home.png)

Elige el método de registro (GitHub, LinuxDO o nombre de usuario):

![Selección del método de registro](/img/start/api-02-register.png)

Completa el formulario con nombre de usuario, contraseña y confirmación de contraseña:

![Formulario de registro](/img/start/api-03-register-form.png)

### 2. Iniciar sesión

Una vez registrado, inicia sesión con tu nombre de usuario y contraseña:

![Página de inicio de sesión](/img/start/api-04-login.png)

Tras iniciar sesión, accederás a la consola:

![Página principal de la consola](/img/start/api-05-console.png)

### 3. Crear un token

Ve a **Consola → Gestión de tokens → Agregar token** y rellena el formulario:

![Agregar token](/img/start/api-06-token-create.png)

- En **Grupo de tokens**, se recomienda seleccionar el **canal oficial**. Este grupo incluye toda la gama de modelos Claude y selecciona automáticamente el modelo según la complejidad de la tarea. Los canales y modelos disponibles están sujetos a cambios — consulta la [página de Precios](https://fishxcode.com/pricing) para información actualizada.

Una vez creado, haz clic en el botón **Copiar** en la lista de tokens para obtener tu API Key (formato: `sk-xxx`):

![Copiar token](/img/start/api-07-token-copy.png)

### 4. Recargar saldo

Ve a **Consola → Gestión de billetera**. Se admite Alipay, WeChat o código de canje:

![Página de recarga](/img/start/api-08-wallet.png)

| Método | Ruta |
|---|---|
| Alipay | Ingresar/seleccionar monto → Alipay |
| WeChat Pay | Ingresar/seleccionar monto → WeChat |
| Código de canje | Ingresar código en el área de canje → Clic en Canjear saldo |

::: tip Explicación del multiplicador
El `0.5x multiplicador` en el nombre del grupo indica la tasa de consumo. Cuanto menor sea el valor, más tokens obtienes. Por ejemplo, `0.5x` significa que por ¥1 puedes usar 10 millones de tokens oficiales (frente a los 5 millones al precio oficial).
:::

---

## 2. Gestión de tokens

### Detalles de grupos de tokens

Al crear un token es necesario seleccionar un grupo, que determina el rango de modelos disponibles y la tasa de consumo:

| Tipo de grupo | Descripción | Escenario recomendado |
|---|---|---|
| **Canal oficial** | Incluye toda la gama de modelos Claude con enrutamiento automático | Recomendado para la mayoría de usuarios |
| **Grupos de tarifa baja** | Tasa de consumo inferior, mayor relación calidad-precio | Llamadas frecuentes o escenarios sensibles al coste |

::: tip Recomendación
Si no estás seguro de qué grupo elegir, selecciona directamente el **canal oficial**. El sistema seleccionará automáticamente el modelo más adecuado según la complejidad de la tarea.
:::

### Recomendaciones de seguridad para tokens

- **Rotación periódica**: se recomienda renovar los tokens cada 30-90 días para reducir el riesgo de filtración
- **No almacenar en texto plano**: evita escribir la API Key directamente en el código o subirla a un repositorio Git; utiliza variables de entorno
- **Un token por proyecto**: crea tokens independientes para cada proyecto, lo que facilita la gestión y el seguimiento
- **Eliminación ante fuga**: si sospechas que un token se ha filtrado, elimínalo de inmediato en la consola y crea uno nuevo

### Modificar y eliminar tokens

En la página **Consola → Gestión de tokens**:

- **Ver detalles**: haz clic en el nombre del token para consultar el grupo, el rango de modelos y la fecha de creación
- **Copiar token**: haz clic en el botón de copiar en la lista
- **Eliminar token**: haz clic en el botón de eliminar; una vez eliminado, el token dejará de funcionar de inmediato y las herramientas que lo utilicen no podrán realizar más llamadas

::: warning Atención
La eliminación no se puede deshacer. Asegúrate de que ya no necesitas el token antes de eliminarlo.
:::

---

## 3. Seguridad de la cuenta

### Contraseña y seguridad de inicio de sesión

- Utiliza una **contraseña segura** (mínimo 8 caracteres, con mayúsculas, minúsculas y números)
- No reutilices la misma contraseña en varias plataformas
- Si olvidaste tu contraseña, puedes restablecerla desde la página de inicio de sesión mediante el flujo de recuperación

### Inicio de sesión con terceros

FishXCode admite los siguientes métodos de inicio de sesión con terceros:

| Método | Descripción |
|---|---|
| **GitHub** | Recomendado para desarrolladores, inicio de sesión con un clic |
| **LinuxDO** | Usuarios de la comunidad pueden iniciar sesión directamente |

Una vez vinculada una cuenta de terceros, podrás iniciar sesión mediante la autorización de la plataforma correspondiente, sin necesidad de introducir contraseña.

### Detección de anomalías y resolución

Si detectas alguna de las siguientes situaciones anómalas, te recomendamos actuar de inmediato:

1. **Aumento inusual del consumo** → Comprueba si algún token se ha filtrado; si es necesario, elimina todos los tokens y créalos de nuevo
2. **No puedes iniciar sesión** → Intenta recuperar la contraseña; si el problema persiste, contacta con el soporte oficial
3. **Descuento anómalo del saldo** → Revisa los registros de uso para identificar las llamadas concretas y confirmar si se trata de un consumo normal

::: tip Contactar con soporte
Si tienes problemas con tu cuenta, accede a la página [Contacto](https://fishxcode.com/contact) para obtener ayuda.
:::

---

## 4. Preguntas frecuentes

### ¿No recibes el código de verificación?

1. Comprueba que el correo electrónico esté escrito correctamente (presta atención a la ortografía y al dominio)
2. Revisa la carpeta de correo no deseado / spam
3. Espera 1-2 minutos y vuelve a intentarlo; evita solicitudes frecuentes
4. Si sigues sin recibirlo, prueba con otro correo electrónico o utiliza el inicio de sesión con terceros

### ¿Dónde encontrar los tokens creados?

Ve a **Consola → Gestión de tokens**; los tokens creados aparecerán en la lista. Haz clic en el botón de copiar para obtener la API Key en formato `sk-xxx`.

### ¿Recarga no reflejada?

1. Confirma que el pago se haya completado (consulta el historial de transacciones en Alipay/WeChat)
2. Espera entre 1 y 5 minutos, ya que puede haber un breve retraso en el sistema
3. Actualiza la página de billetera en la consola
4. Si transcurridos más de 10 minutos el saldo no aparece, contacta con el servicio de atención al cliente y proporciona una captura de pantalla del pago

### ¿Cómo consultar el detalle de consumo?

Ve a **Consola → Registro de uso**, donde podrás ver de cada llamada a la API:
- Fecha y hora, y nombre del modelo
- Consumo de tokens y coste
- IP de la solicitud y registro detallado
