---
title: Solución de problemas con los registros de recursos del servicio Azure Web PubSub
description: Aprenda a obtener registros de recursos y a usarlos para solucionar problemas.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: e8bf85166b0557f30909d8109ecdd36782a4adc1
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995110"
---
# <a name="how-to-troubleshoot-with-resource-logs"></a>Solución de problemas con registros de recursos

En esta guía paso a paso se muestran las opciones para obtener los registros de recursos y cómo usarlos para solucionar problemas.

## <a name="whats-the-resource-logs"></a>¿Qué son los registros de recursos?

Los registros de recursos proporcionan una vista más completa de la información de conectividad, de mensajería y de las solicitudes HTTP a la instancia del servicio Azure Web PubSub. Se pueden usar para la identificación de problemas, el seguimiento de la conexión, de los mensajes y de las solicitudes HTTP, y el análisis.

Hay tres tipos de registros: registros de conectividad, registros de mensajería y registros de solicitudes HTTP.

### <a name="connectivity-logs"></a>Registros de conectividad

Los registros de conectividad proporcionan información detallada para las conexiones del centro de conectividad de Azure Web PubSub. Por ejemplo, información básica (identificador de usuario, identificador de conexión, etc.) e información de eventos (evento de conexión, desconexión y anulación, etc.). Es por esto que el registro de conectividad resulta útil para solucionar problemas relacionados con la conexión.

### <a name="messaging-logs"></a>Registros de mensajería

Los registros de mensajería proporcionan información de seguimiento de los mensajes del centro de conectividad de Azure Web PubSub recibidos y enviados a través del servicio Azure Web PubSub. Por ejemplo, el identificador de seguimiento y el tipo de mensaje del mensaje. Por lo general, el mensaje se registra cuando llega al servidor o cuando sale de este. Por lo tanto, los registros de mensajería resultan útiles para solucionar problemas relacionados con los mensajes.

### <a name="http-request-logs"></a>Registros de solicitudes HTTP

Los registros de solicitudes HTTP proporcionan información de seguimiento de las solicitudes HTTP al servicio Azure Web PubSub. Por ejemplo, el método HTTP y el código de estado. Por lo general, una solicitud HTTP se registra cuando llega al servicio o cuando sale de este. Por tanto, los registros de solicitudes HTTP son útiles para solucionar problemas relacionados con la solicitud.

## <a name="capture-resource-logs-with-live-trace-tool"></a>Captura de registros de recursos con la herramienta de seguimiento activo 

La herramienta de seguimiento activo del servicio Azure Web PubSub tiene la capacidad de recopilar registros de recursos en tiempo real y es útil para realizar un seguimiento con el entorno de desarrollo del cliente. La herramienta de seguimiento activo puede capturar registros de conectividad, registros de mensajería y registros de solicitudes HTTP.

> [!NOTE]
> Los registros de recursos en tiempo real que captura la herramienta de seguimiento activo se facturarán como mensajes (tráfico saliente).

> [!NOTE]
> La instancia del servicio Azure Web PubSub creada como nivel gratis tiene el límite de mensajes diario (tráfico saliente).

### <a name="launch-the-live-trace-tool"></a>Inicio de la herramienta de seguimiento activo

1. Vaya a Azure Portal. 
2. En la página **Configuración de seguimiento en directo** de su instancia del servicio Azure Web PubSub, active la opción **Habilitar el seguimiento en directo** si está desactivada.
3. Seleccione las categorías de registro que necesite.
4. Haga clic en el botón **Guardar** y espere hasta que la configuración surta efecto.
5. Haga clic en *Abrir la herramienta de seguimiento activo*.

    :::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-logs-with-live-trace-tool.png" alt-text="Captura de pantalla del inicio de la herramienta de seguimiento activo":::

### <a name="capture-the-resource-logs"></a>Captura de registros de recursos

La herramienta de seguimiento activo proporciona algunas funcionalidades esenciales que lo ayudarán a capturar los registros de recursos para solucionar problemas.

* **Capture** (Capturar): empiece a capturar los registros de recursos en tiempo real de la instancia de Azure Web PubSub con la herramienta de seguimiento activo.
* **Clear** (Borrar): borre los registros de recursos capturados en tiempo real.
* **Log filter** (Filtro de registro): la herramienta de seguimiento activo permite filtrar los registros de recursos capturados en tiempo real con una palabra clave específica. El separador común (por ejemplo, espacio, coma, punto y coma, etc.) se considerará parte de la palabra clave. 
* **Status** (Estado): el estado muestra si la herramienta de seguimiento activo está conectada o desconectada de la instancia específica.

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/live-trace-tool-capture.png" alt-text="Captura de pantalla de la captura de registros de recursos con la herramienta de seguimiento activo.":::

Los registros de recursos en tiempo real que captura la herramienta de seguimiento activo contienen información detallada para la solución de problemas. 

| Nombre | Descripción |
| ------------ |  ------------------------ | 
| Time | Hora del evento de registro |
| Nivel de registro | Nivel del evento de registro (seguimiento/depuración/informativo/advertencia/error) |
| Nombre del evento | Nombre de operación del evento |
| Message | Mensaje detallado del evento de registro |
| Excepción | Excepción en tiempo de ejecución del servicio Azure Web PubSub |
| Hub | Nombre del centro de conectividad definido por el usuario |
| Id. de conexión | Identidad de la conexión |
| Id. de usuario | Identidad del usuario |
| IP | Dirección IP del cliente |
| Plantilla de ruta | La plantilla de ruta de la API |
| Método HTTP | Método HTTP (POST/GET/PUT/DELETE) |
| URL | El localizador uniforme de recursos |
| Identificación de seguimiento | Identificador único de la invocación |
| Código de estado | Código de respuesta HTTP |
| Duration | La duración entre la recepción y el procesamiento de la respuesta |
| Encabezados | Información adicional pasada por el cliente y el servidor con una solicitud o respuesta HTTP |

Una vez que el servicio Azure Web PubSub esté disponible de manera general, la herramienta de seguimiento activo también admitirá la exportación de los registros como un formato específico y, luego, lo ayudará a compartir con otros usuarios para solucionar problemas. 

## <a name="capture-resource-logs-with-azure-monitor"></a>Captura de registros de recursos con Azure Monitor

### <a name="how-to-enable-resource-logs"></a>Habilitación de los registros de recursos

Actualmente, Azure Web PubSub admite la integración con [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage).

1. Vaya a Azure Portal.
2. En la página **Configuración de diagnóstico** de la instancia del servicio Azure Web PubSub, seleccione el vínculo **+ Agregar configuración de diagnóstico**.
3. En **Nombre de configuración de diagnóstico**, introduzca el nombre de la configuración.
4. En **Detalles de la categoría**, seleccione cualquier categoría de registro que necesite.
5. En **Detalles del destino**, seleccione la casilla **Archivar en una cuenta de almacenamiento**.
6. Haga clic en el botón **Guardar** para guardar la configuración de diagnóstico.

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-list.png" alt-text="Captura de pantalla de la visualización de la configuración de diagnóstico y la creación de una nueva configuración":::

:::image type="content" source="./media/howto-troubleshoot-diagnostic-logs/diagnostic-settings-details.png" alt-text="Captura de pantalla de los detalles de la configuración de diagnóstico":::

> [!NOTE]
> La cuenta de almacenamiento debe estar en la misma región que el servicio Azure Web PubSub.

### <a name="archive-to-a-storage-account"></a>Archivar en una cuenta de almacenamiento

Los registros se almacenan en la cuenta de almacenamiento configurada en el panel **Configuración de diagnóstico**. Se crea automáticamente un contenedor denominado `insights-logs-<CATEGORY_NAME>` para almacenar los registros de recursos. Dentro del contenedor, los registros se almacenan en el archivo `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json`. Básicamente, la ruta de acceso se combina mediante `resource ID` y `Date Time`. Los archivos de registro se dividen por `hour`. Por lo tanto, los minutos siempre son `m=00`.

Todos los registros se almacenan en el formato de notación de objetos JavaScript (JSON). Cada entrada tiene campos de cadena que usan el formato descrito en las secciones siguientes.

Las cadenas JSON de registros de archivo incluyen elementos enumerados en las tablas siguientes:

**Formato**

Nombre | Descripción
------- | -------
time | Hora del evento de registro
level | Nivel del evento de registro
resourceId | Identificador de recurso de Azure SignalR Service
ubicación | Ubicación de Azure SignalR Service
category | Categoría del evento de registro
operationName | Nombre de operación del evento
callerIpAddress | Dirección IP del servidor o cliente
properties | Propiedades detalladas relacionadas con este evento de registro. Para más información, consulte la siguiente tabla de propiedades.

**Propiedades de tabla**

Nombre | Descripción
------- | -------
collection | Colección del evento de registro. Los valores permitidos son: `Connection`, `Authorization` y `Throttling`
connectionId | Identidad de la conexión
userId | Identidad del usuario
message | Mensaje detallado del evento de registro
centro | Nombre del centro de conectividad definido por el usuario |
routeTemplate | Plantilla de ruta de la API |
httpMethod | Método HTTP (POST/GET/PUT/DELETE) |
url | Localizador uniforme de recursos |
traceId | Identificador único de la invocación |
statusCode | Código de respuesta HTTP |
duration | Duración entre la recepción y el procesamiento de la solicitud |
headers | Información adicional pasada por el cliente y el servidor con una solicitud o respuesta HTTP |

El código siguiente es un ejemplo de una cadena JSON de registro de archivo:

```json
{
  "properties": {
    "message": "Connection started",
    "collection": "Connection",
    "connectionId": "LW61bMG2VQLIMYIVBMmyXgb3c418200",
    "userId": null
  },
  "operationName": "ConnectionStarted",
  "category": "ConnectivityLogs",
  "level": "Informational",
  "callerIpAddress": "167.220.255.79",
  "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYGROUP/PROVIDERS/MICROSOFT.SIGNALRSERVICE/WEBPUBSUB/MYWEBPUBSUB",
  "time": "2021-09-17T05:25:05Z",
  "location": "westus"
}
```

## <a name="troubleshoot-with-the-resource-logs"></a>Solución de problemas con los registros de recursos

Cuando se encuentra en una situación de crecimiento o interrupción inesperada de la conexión, puede aprovechar los registros de recursos para solucionar problemas. Los problemas típicos suelen ser cambios inesperados en el número de conexiones, conexiones que llegan a los límites de conexión y errores de autorización.

### <a name="unexpected-connection-number-changes"></a>Cambios inesperados en el número de conexiones

#### <a name="unexpected-connection-dropping"></a>Interrupción inesperada de la conexión

Si se interrumpe una conexión, los registros de recursos registrarán este evento de desconexión con `ConnectionAborted` o `ConnectionEnded` en `operationName`.

La diferencia entre `ConnectionAborted` y `ConnectionEnded` es que `ConnectionEnded` es una desconexión esperada desencadenada por el lado cliente o servidor. Aunque `ConnectionAborted` suele ser un evento de interrupción inesperada de la conexión, se proporcionará el motivo de la anulación en `message`.

Las opciones de la anulación se muestran en la siguiente tabla:

| Motivo | Descripción |
| ------- | ------- |
| El número de conexiones llega al límite | El número de conexiones llega al límite del nivel de precios actual. Considere la posibilidad de escalar la unidad de servicio.
| Recarga de servicios, reconexión | El servicio Azure Web PubSub se está recargando. Debe implementar su propio mecanismo de reconexión o volver a conectarse manualmente al servicio Azure Web PubSub. |
| Error transitorio del servidor interno | Se produce un error transitorio en el servicio Azure Web PubSub, se debería recuperar automáticamente.

#### <a name="unexpected-connection-growing"></a>Crecimiento de conexiones inesperado

Para solucionar los problemas relacionados con un crecimiento de conexiones inesperado, lo primero que debe hacer es filtrar las conexiones adicionales. Puede agregar un identificador único de usuario de prueba a la conexión del cliente de prueba. A continuación, compruébelo con los registros de recursos; si ve que hay más de una conexión de cliente con el mismo identificador de usuario o IP de prueba, es probable que el lado de cliente cree y establezca más conexiones de las esperadas. Compruebe el lado cliente.

### <a name="authorization-failure"></a>Error de autorización

Si recibe un mensaje 401 No autorizado para las solicitudes de cliente, compruebe los registros de recursos. Si encuentra `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>`, significa que no hay ninguna audiencia del token de acceso válida. Intente usar las audiencias válidas sugeridas en el registro.

### <a name="throttling"></a>Limitaciones

Si encuentra que no puede establecer conexiones de cliente con el servicio Azure Web PubSub, compruebe los registros de recursos. Si encuentra `Connection count reaches limit` en el registro de recursos, es porque establece demasiadas conexiones con el servicio Azure Web PubSub, lo que provoca que se alcance el límite del número de conexiones. Considere la posibilidad de escalar verticalmente la instancia del servicio Azure Web PubSub. Si encuentra `Message count reaches limit` en el registro de recursos, significa que usa el nivel gratuito y ha llegado al máximo de la cuota de mensajes. Si desea enviar más mensajes, considere la posibilidad de cambiar la instancia del servicio Azure Web PubSub al nivel estándar para enviar mensajes adicionales. Para más información, consulte [Precios de Azure Web PubSub](https://azure.microsoft.com/pricing/details/web-pubsub/).
