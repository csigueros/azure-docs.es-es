---
title: Uso de la herramienta de seguimiento activo para Azure SignalR Service
description: Aprenda a usar la herramienta de seguimiento activo para Azure SignalR Service.
author: wanlwanl
ms.author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: 1f361b8a785f108f985dbae23290150c46170daa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705308"
---
# <a name="how-to-use-live-trace-tool-for-azure-signalr-service"></a>Uso de la herramienta de seguimiento activo para Azure SignalR Service

La herramienta de seguimiento activo es una única aplicación web para capturar y mostrar los seguimientos en vivo de Azure SignalR Service. Los seguimientos activos se pueden recopilar en tiempo real sin ninguna dependencia de otros servicios.
Puede habilitar y deshabilitar la característica de seguimiento activo con un solo clic. También puede elegir cualquier categoría de registro que le interese.

> [!NOTE]
> Tenga en cuenta que los seguimientos activos se cuentan como mensajes salientes.

## <a name="launch-the-live-trace-tool"></a>Inicio de la herramienta de seguimiento activo

1. Vaya a Azure Portal.
2. Active **Enable Live Trace** (Habilitar seguimiento activo).
3. Haga clic en el botón **Save** (Guardar) en la barra de herramientas y espere a que los cambios surtan efecto.
4. En la página **Configuración de diagnóstico** de la instancia del servicio Azure Web PubSub, seleccione **Open Live Trace Tool** (Abrir herramienta de seguimiento activo). 

    :::image type="content" source="media/signalr-howto-troubleshoot-live-trace/live-traces-with-live-trace-tool.png" alt-text="Captura de pantalla del inicio de la herramienta de seguimiento activo":::

## <a name="capture-live-traces"></a>Captura de seguimientos activos

La herramienta de seguimiento activo proporciona algunas funcionalidades esenciales que le ayudarán a capturar los seguimientos activos para solucionar problemas.

* **Capture** (Capturar): empiece a capturar los registros de diagnóstico en tiempo real de la instancia de Azure Web PubSub con la herramienta de seguimiento activo.
* **Clear** (Borrar): borre los seguimientos activos capturados en tiempo real.
* **Export** (Exportar): exporte los seguimientos activos a un archivo. El formato de archivo admitido actualmente es el archivo CSV.
* **Log filter** (Filtro de registro): la herramienta de seguimiento activo permite filtrar los seguimientos activos capturados en tiempo real con una palabra clave específica. El separador común (por ejemplo, espacio, coma, punto y coma, etc.) se considerará parte de la palabra clave. 
* **Status** (Estado): el estado muestra si la herramienta de seguimiento activo está conectada o desconectada de la instancia específica.

:::image type="content" source="./media/signalr-howto-troubleshoot-live-trace/live-trace-tool-capture.png" alt-text="Captura de pantalla de la captura de seguimientos activos con la herramienta de seguimiento activo":::.

Los seguimientos activos capturados en tiempo real por la herramienta de seguimiento activo contienen información detallada para la solución de problemas. 

| Nombre | Descripción |
| ------------ |  ------------------------ | 
| Time | Hora del evento de registro |
| Nivel de registro | Nivel del evento de registro (seguimiento/depuración/informativo/advertencia/error) |
| Nombre del evento | Nombre de operación del evento |
| Message | Mensaje detallado del evento de registro |
| Excepción | Excepción en tiempo de ejecución del servicio Azure Web PubSub |
| Hub | Nombre del centro de conectividad definido por el usuario |
| Id. de conexión | Identidad de la conexión |
| Tipo de conexión. | Tipo de la conexión. Los valores permitidos son `Server` (conexiones entre el servidor y el servicio) y `Client` (conexiones entre cliente y servicio).|
| Id. de usuario | Identidad del usuario |
| IP | Dirección IP del cliente |
| Persistente del servidor | Modo de enrutamiento del cliente. Los valores permitidos son `Disabled`, `Preferred` y `Required`. Para más información, consulte [ServerStickyMode](https://github.com/Azure/azure-signalr/blob/master/docs/run-asp-net-core.md#serverstickymode). |
| Transporte | Transporte que el cliente puede usar para enviar solicitudes HTTP. Los valores permitidos son: `WebSockets`, `ServerSentEvents` y `LongPolling`. Para más información, consulte [HttpTransportType](/dotnet/api/microsoft.aspnetcore.http.connections.httptransporttype). |
| Id. de seguimiento de mensajes | El identificador único de un mensaje |
| Plantilla de ruta | La plantilla de ruta de la API |
| Método HTTP | Método HTTP (POST/GET/PUT/DELETE) |
| Resolución | Localizador uniforme de recursos |
| Identificación de seguimiento | El identificador único para representar una solicitud |
| Código de estado | El código de respuesta HTTP |
| Duration | Duración entre la recepción y el procesamiento de la solicitud |
| Encabezados | La información adicional pasada por el cliente y el servidor con una solicitud o respuesta HTTP |
| Id. de invocación | El identificador único que representa una invocación (solo disponible para ASP.NET SignalR) |
| Tipo de mensaje | El tipo del mensaje (BroadcastDataMessage/JoinGroupMessage/LeaveGroupMessage/...) |

## <a name="next-steps"></a>Pasos siguientes

En esta guía, ha aprendido a usar la herramienta de seguimiento activo. También puede obtener información acerca de cómo controlar los problemas comunes.
* Guías de solución de problemas: para obtener información sobre cómo solucionar problemas típicos basados en seguimientos activos, consulte nuestra [guía de solución de problemas](./signalr-howto-troubleshoot-guide.md).
* Métodos de solución de problemas: para que el autodiagnóstico encuentre la causa principal directamente o limite el problema, consulte nuestra [introducción a los métodos de solución de problemas](./signalr-howto-troubleshoot-method.md).