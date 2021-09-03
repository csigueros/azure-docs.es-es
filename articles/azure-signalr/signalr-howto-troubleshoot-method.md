---
title: Práctica de solución de problemas de Azure SignalR Service
description: Obtenga información acerca de cómo solucionar problemas de conectividad y entrega de mensajes
author: yjin81
ms.service: signalr
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: yajin1
ms.openlocfilehash: a85e61536e04bd670cd49127f906c69727acc8e9
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113134986"
---
# <a name="how-to-troubleshoot-connectivity-and-message-delivery-issues"></a>Solución de problemas de conectividad y entrega de mensajes

En esta guía se presentan varias maneras de realizar el autodiagnóstico para encontrar la causa raíz directamente o acercarse al problema. El resultado del autodiagnóstico también resulta útil cuando se informa a Microsoft para realizar una investigación más detallada.

En primer lugar, debe comprobar desde Azure Portal con qué [ServiceMode](./concept-service-mode.md) está configurada la instancia de Azure SignalR Service (también conocida como **ASRS**).

:::image type="content" source="./media/signalr-howto-troubleshoot-method/service-mode.png" alt-text="ServiceMode":::

* En el modo `Default`, consulte la [solución de problemas del modo predeterminado.](#default_mode_tsg)

* En el modo `Serverless`, consulte la [solución de problemas del modo sin servidor.](#serverless_mode_tsg)

* En el modo `Classic`, consulte la [solución de problemas del modo clásico.](#classic_mode_tsg)

En segundo lugar, debe capturar seguimientos de servicio para solucionar problemas. Para obtener información sobre cómo capturar seguimientos, vea [Procedimiento para capturar seguimientos de servicio](#how-to-capture-service-traces).

[¿Tiene problemas o comentarios sobre la solución de problemas? Háganoslo saber.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="how-to-capture-service-traces"></a>Procedimiento para capturar seguimientos de servicio

Para simplificar el proceso de solución de problemas, el servicio Azure SignalR proporciona una **herramienta de seguimiento activo** para exponer los seguimientos de servicio en las categorías de **conectividad** y **mensajería**. Los seguimientos incluyen, entre otros, eventos de conexión conectada o desconectada, y de mensajes recibidos o enviados. Con la **herramienta de seguimiento activo**, puede capturar, ver, ordenar, filtrar y exportar seguimientos activos. Para obtener más información, vea [Uso de la herramienta de seguimiento activo](./signalr-howto-troubleshoot-live-trace.md).

[¿Tiene problemas o comentarios sobre la solución de problemas? Háganoslo saber.](https://aka.ms/asrs/survey/troubleshooting)

<a name="default_mode_tsg"></a>

## <a name="default-mode-troubleshooting"></a>Solución de problemas del modo predeterminado

Cuando **ASRS** está en *modo predeterminado*, hay **tres** roles: *Cliente*, *servidor* y *servicio*:

* *Cliente*: *cliente* significa que los clientes están conectados a **ASRS**. Las conexiones persistentes entre el cliente y **ASRS** se denominan *conexiones de cliente* en esta guía.

* *Servidor*: el *servidor* representa al servidor que realiza la negociación del cliente y hospeda la lógica `Hub` de SignalR. Y las conexiones persistentes entre el *servidor* y **ASRS** se denominan *conexiones de servidor* en esta guía.

* *Servicio*: el *servicio* es el nombre corto de **ASRS** en esta guía.

Consulte [Elementos internos de Azure SignalR Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) para una introducción detallada a la arquitectura y el flujo de trabajo completos.

Hay varias maneras de determinar el problema. 

* Si el problema se produce durante el proceso o puede reproducirse, el método directo consiste en ver el tráfico en curso. 

* Si el problema es difícil de reproducir, los seguimientos y los registros pueden ser de ayuda.

### <a name="how-to-view-the-traffic-and-narrow-down-the-issue"></a>Visualización del tráfico y determinación del problema

La captura del tráfico continuo es la manera más directa de determinar cuál es el problema. Puede capturar los [seguimientos de red](/aspnet/core/signalr/diagnostics#network-traces) mediante las opciones que se describen a continuación:

* [Recopilar un seguimiento de red con Fiddler](/aspnet/core/signalr/diagnostics#network-traces)

* [Recopilar un seguimiento de red con Fiddler](/aspnet/core/signalr/diagnostics#collect-a-network-trace-with-tcpdump-macos-and-linux-only)

* [Recopilar un seguimiento de red en el explorador](/aspnet/core/signalr/diagnostics#collect-a-network-trace-in-the-browser)

<a name="view_traffic_client"></a>

#### <a name="client-requests"></a>Solicitudes de cliente

En el caso de una conexión persistente de SignalR, primero realizará un proceso `/negotiate` en el servidor de aplicaciones hospedadas, se redirigirá a Azure SignalR Service y, a continuación, establecerá la conexión persistente real con Azure SignalR Service. Consulte [Elementos internos de Azure SignalR Service](https://github.com/Azure/azure-signalr/blob/dev/docs/internal.md) para ver los pasos detallados.

Con el seguimiento de red del lado cliente a mano, compruebe las solicitudes que generan un error, con qué código de estado y con qué respuestas, y busque soluciones en la [guía de solución de problemas](./signalr-howto-troubleshoot-guide.md).

#### <a name="server-requests"></a>Solicitudes de servidor

El *servidor* de SignalR conserva la *conexión de servidor* entre el *servidor* y el *servicio*. Cuando se inicia el servidor de aplicaciones, inicia la conexión de **WebSocket** a Azure SignalR Service. Todos los tráficos de cliente se enrutan a través de Azure SignalR Service a estas *conexiones de servidor* y, a continuación, se envían a `Hub`. Cuando se pierde una *conexión de servidor*, se verán afectados los clientes enrutados a esta *conexión de servidor*. Nuestro SDK de Azure SignalR tiene una lógica de tipo "Reintentar siempre" para volver a establecer la *conexión de servidor* con un retraso de un minuto como máximo para minimizar el impacto.

Las *conexiones del servidor* pueden perderse debido a la inestabilidad de la red o a un mantenimiento normal de Azure SignalR Service, o a la actualización/mantenimiento del servidor de aplicaciones hospedado. Siempre que el lado del cliente tenga el mecanismo de desconexión/reconexión, el impacto será mínimo, como cualquier desconexión o reconexión causada por el lado cliente.

Consulte el seguimiento de red del lado servidor para averiguar el código de estado y los detalles del error por el que la *conexión del servidor* se pierde o es rechazada por el *servicio* y busque la causa principal en la [guía de solución de problemas](./signalr-howto-troubleshoot-guide.md).

[¿Tiene problemas o comentarios sobre la solución de problemas? Háganoslo saber.](https://aka.ms/asrs/survey/troubleshooting)

### <a name="how-to-add-logs"></a>Incorporación de registros

Los registros pueden ser útiles para diagnosticar problemas y supervisar el estado de ejecución.

<a name="add_logs_client"></a>

#### <a name="how-to-enable-client-side-log"></a>Habilitación de los registros del lado cliente

La experiencia de registro del lado cliente es exactamente la misma que cuando se usa una instancia de SignalR autohospedada.

##### <a name="enable-client-side-logging-for-aspnet-core-signalr"></a>Habilitación de los registros del lado cliente para `ASP.NET Core SignalR`

* [Registros del cliente de JavaScript](/aspnet/core/signalr/diagnostics#javascript-client-logging)

* [Registros del cliente de .NET](/aspnet/core/signalr/diagnostics#net-client-logging)


##### <a name="enable-client-side-logging-for-aspnet-signalr"></a>Habilitación de los registros del lado cliente para `ASP.NET SignalR`

* [Cliente .NET](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-net-client-windows-desktop-apps)

* [Habilitación del seguimiento en los clientes de Windows Phone 8](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-windows-phone-8-clients)

* [Habilitación del seguimiento en el cliente de JavaScript](/aspnet/signalr/overview/testing-and-debugging/enabling-signalr-tracing#enabling-tracing-in-the-javascript-client)

<a name="add_logs_server"></a>

#### <a name="how-to-enable-server-side-log"></a>Habilitación del registro del lado servidor

##### <a name="enable-server-side-logging-for-aspnet-core-signalr"></a>Habilitación de los registros del lado servidor para `ASP.NET Core SignalR`

El registro del lado servidor para `ASP.NET Core SignalR` se integra con los [registros](/aspnet/core/fundamentals/logging/?tabs=aspnetcore2x&preserve-view=true&view=aspnetcore-2.1) basados en `ILogger` proporcionado en el marco de `ASP.NET Core`. Puede habilitar el registro del lado servidor mediante `ConfigureLogging`. A continuación se incluye un ejemplo de uso:

```cs
.ConfigureLogging((hostingContext, logging) =>
        {
            logging.AddConsole();
            logging.AddDebug();
        })
```

Las categorías de registrador de Azure SignalR Service siempre comienzan con `Microsoft.Azure.SignalR`. Para habilitar los registros detallados de Azure SignalR Service, configure los prefijos anteriores en el nivel `Information` del archivo **appsettings.json** como se indica a continuación:

```JSON
{
    "Logging": {
        "LogLevel": {
            ...
            "Microsoft.Azure.SignalR": "Information",
            ...
        }
    }
}
```

Compruebe si hay algún registro de advertencia o error anómalo grabado. 


##### <a name="enable-server-side-traces-for-aspnet-signalr"></a>Habilitación del seguimiento del lado servidor para `ASP.NET SignalR`

Cuando use una versión del SDK mayor que la `1.0.0`, podrá habilitar los seguimientos al agregar el siguiente código a `web.config`: ([Detalles](https://github.com/Azure/azure-signalr/issues/452#issuecomment-478858102))

```xml
<system.diagnostics>
    <sources>
      <source name="Microsoft.Azure.SignalR" switchName="SignalRSwitch">
        <listeners>
          <add name="ASRS" />
        </listeners>
      </source>
    </sources>
    <!-- Sets the trace verbosity level -->
    <switches>
      <add name="SignalRSwitch" value="Information" />
    </switches>
    <!-- Specifies the trace writer for output -->
    <sharedListeners>
      <add name="ASRS" type="System.Diagnostics.TextWriterTraceListener" initializeData="asrs.log.txt" />
    </sharedListeners>
    <trace autoflush="true" />
  </system.diagnostics>
```

Compruebe si hay algún registro de advertencia o error anómalo grabado. 


#### <a name="how-to-enable-logs-inside-azure-signalr-service"></a>Habilitación de registros en Azure SignalR Service

También puede [habilitar los registros de diagnóstico](./signalr-howto-diagnostic-logs.md) para Azure SignalR Service; estos registros proporcionan información detallada de cada conexión con Azure SignalR Service.

<a name="serverless_mode_tsg"></a>

[¿Tiene problemas o comentarios sobre la solución de problemas? Háganoslo saber.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="serverless-mode-troubleshooting"></a>Solución de problemas del modo sin servidor

Cuando **ASRS** se encuentra en modo *sin servidor*, solo **SignalR de ASP.NET Core** admite el modo `Serverless` y **SignalR de ASP.NET** **no** lo admite.

Para diagnosticar los problemas de conectividad en el modo `Serverless`, la manera más directa consiste en [ver el tráfico del lado cliente](#view_traffic_client). Habilite los [registros del lado cliente](#add_logs_client), y los [registros del lado de servicio](#add_logs_server) también podrán ser útiles.

<a name="classic_mode_tsg"></a>

[¿Tiene problemas o comentarios sobre la solución de problemas? Háganoslo saber.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="classic-mode-troubleshooting"></a>Solución de problemas del modo clásico

El modo `Classic` quedó obsoleto y no se recomienda su uso. En este modo, Azure SignalR Service usa las *conexiones del servidor* establecidas para determinar si el servicio actual está en modo `default` o `serverless`. Esto puede dar lugar a algunos problemas de conectividad con el cliente intermedio porque, cuando hay una caída repentina de todas las *conexiones de servidor* establecidas (por ejemplo, debido a la inestabilidad de la red), Azure SignalR Service cree que se ha cambiado al modo `serverless` y que los clientes conectados durante este período nunca se enrutarán al servidor de aplicaciones hospedado. Habilite los [registros del lado de servicio](#add_logs_server) y compruebe si hay clientes registrados como `ServerlessModeEntered`. No obstante, si tiene un servidor de aplicaciones hospedado, algunos clientes nunca se comunicarán con el lado servidor de la aplicación. Si hay algún cliente de este tipo, [anular estas conexiones de cliente](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md#API) y permitir que los clientes se reinicien puede ser de ayuda.

La solución de problemas de conectividad y entrega de mensajes del modo `classic` es similar a la [solución de problemas del modo predeterminado](#default_mode_tsg).

[¿Tiene problemas o comentarios sobre la solución de problemas? Háganoslo saber.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="service-health"></a>Estado del servicio

Puede comprobar la API de estado para obtener el estado del servicio.

* Solicitud: GET `https://{instance_name}.service.signalr.net/api/v1/health`

* Código de estado de respuesta:
  * 200: correcto.
  * 503: el servicio presenta un estado incorrecto. Puede:
    * Esperar unos minutos para la autorrecuperación.
    * Comprobar que la dirección IP es la misma que la IP del portal.
    * O reiniciar la instancia.
    * Si todas las opciones anteriores no funcionan, póngase en contacto con nosotros al agregar una nueva solicitud de soporte técnico en Azure Portal.

Más información acerca de la [recuperación ante desastres](./signalr-concept-disaster-recovery.md).

[¿Tiene problemas o comentarios sobre la solución de problemas? Háganoslo saber.](https://aka.ms/asrs/survey/troubleshooting)

## <a name="next-steps"></a>Pasos siguientes

En esta guía, obtuvo información acerca de cómo solucionar problemas de conectividad y entrega de mensajes. También puede obtener información acerca de cómo controlar los problemas comunes. 

> [!div class="nextstepaction"]
> [Guía de solución de problemas](./signalr-howto-troubleshoot-guide.md)