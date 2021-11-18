---
title: Supervisión de Azure IoT Hub
description: Comience aquí para obtener información sobre cómo supervisar Azure IoT Hub
author: eross-msft
ms.author: lizross
ms.topic: conceptual
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/26/2021
ms.openlocfilehash: 5a4c5902a42a2efd0b75bdbcbf5d5d90ddab335b
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550390"
---
# <a name="monitoring-azure-iot-hub"></a>Supervisión de Azure IoT Hub

Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. En este artículo se describen los datos de supervisión generados por Azure IoT Hub y cómo puede usar las características de Azure Monitor para analizar y alertar sobre estos datos.

## <a name="monitor-overview"></a>Información general de supervisión

En la página **Información general** de Azure Portal de cada centro de IoT se incluyen gráficos que proporcionan algunas métricas de uso, como el número de mensajes usados y el número de dispositivos conectados al centro de IoT.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="Gráficos de métricas predeterminados en la página Información general del centro de IoT.":::

Tenga en cuenta que el valor de recuento de mensajes se puede retrasar 1 minuto y que, por motivos relacionados con la infraestructura del servicio IoT Hub, a veces puede rebotar entre los valores superior e inferior al actualizar. Este contador solo debería ser incorrecto en el caso de los valores acumulados durante el último minuto.

La información que se muestra en el panel Información general es útil, pero solo representa una pequeña cantidad de los datos de supervisión disponibles sobre un centro de IoT. Algunos datos de supervisión se recopilan de forma automática y están disponibles para su análisis en cuanto se crea el centro de IoT. Puede habilitar tipos adicionales de recopilación de datos con cierta configuración adicional.

## <a name="what-is-azure-monitor"></a>¿Qué es Azure Monitor?

Azure IoT Hub crea datos de supervisión mediante [Azure Monitor](../azure-monitor/overview.md), un servicio de supervisión de pila completo de Azure que proporciona un conjunto completo de características para supervisar los recursos de Azure, además de los recursos locales y en otras nubes.

Comience con el artículo [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), en el que se describen los conceptos siguientes:

- ¿Qué es Azure Monitor?
- Costos asociados con la supervisión
- Datos de supervisión recopilados en Azure
- Configuración de la recolección de datos
- Herramientas estándar en Azure para analizar datos de supervisión y alertar sobre ellos

Las secciones siguientes se basan en este artículo, donde se describen los datos específicos recopilados para Azure IoT Hub, y se proporcionan ejemplos para configurar la recopilación de datos y el análisis de estos datos con herramientas de Azure.

## <a name="monitoring-data"></a>Supervisión de datos

Azure IoT Hub recopila los mismos tipos de datos de supervisión que otros recursos de Azure, que se describen en [Supervisión de datos de recursos de Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data).

Vea [Referencia de supervisión de datos de Azure IoT Hub](monitor-iot-hub-reference.md) para obtener información detallada sobre las métricas y los registros creados por Azure IoT Hub.

> [!IMPORTANT]
> No se garantiza que los eventos que emite el servicio IoT Hub mediante registros de recursos de Azure Monitor sean confiables o estén ordenados. Algunos eventos podrían perderse o entregarse fuera del pedido. Los registros de recursos tampoco están diseñados para que tengan lugar en tiempo real y los eventos pueden tardar varios minutos en registrarse en el destino elegido.

## <a name="collection-and-routing"></a>Recopilación y enrutamiento

Las métricas de la plataforma y el registro de actividad se recopilan y almacenan de forma automática, pero se pueden enrutar a otras ubicaciones mediante una configuración de diagnóstico.

Los registros de recursos no se recopilan ni almacenan hasta que se crea una configuración de diagnóstico y se enrutan a una o más ubicaciones.

Las métricas y los registros se pueden enrutar a varias ubicaciones, que incluyen:
- El almacén de registros de Azure Monitor a través de un área de trabajo de Log Analytics asociada. Allí se pueden analizar mediante Log Analytics.
- Azure Storage para el archivado y el análisis sin conexión. 
- Un punto de conexión de Event Hubs, donde pueden ser leídos por aplicaciones externas, por ejemplo, herramientas de SIEM de terceros.

En Azure Portal, puede seleccionar **Configuración de diagnóstico** en **Supervisión** en el panel izquierdo del centro de IoT y después **Agregar configuración de diagnóstico** para crear la configuración de diagnóstico en el ámbito de los registros y las métricas de plataforma que emite el centro de IoT.

En la captura de pantalla siguiente se muestra una configuración de diagnóstico para enrutar el tipo de registro de recursos *Operaciones de conexión* y todas las métricas de plataforma a un área de trabajo de Log Analytics.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="Panel Configuración de diagnóstico para un centro de IoT.":::

Consulte [Creación de una configuración de diagnóstico para recopilar registros de plataforma y métricas en Azure](../azure-monitor/essentials/diagnostic-settings.md) para ver el proceso detallado para crear una configuración de diagnóstico mediante el Azure Portal, la CLI o PowerShell. Cuando se crea una configuración de diagnóstico, se especifican las categorías de registros que se van a recopilar. Las categorías de Azure IoT Hub se enumeran en [Registros de recursos en la referencia de supervisión de datos de Azure IoT Hub](monitor-iot-hub-reference.md#resource-logs).

Al enrutar métricas de la plataforma de IoT Hub a otras ubicaciones, tenga en cuenta lo siguiente:

- Las métricas de plataforma siguientes no se pueden exportar a través de la configuración de diagnóstico: *Dispositivos conectados (versión preliminar)* y *Número total de dispositivos (versión preliminar)* .

- Las métricas multidimensionales, por ejemplo algunas [métricas de enrutamiento](monitor-iot-hub-reference.md#routing-metrics), se exportan en la actualidad como métricas planas de una sola dimensión que se agregan a través de valores de dimensión. Para obtener más información, vea [Exportación de métricas de plataforma a otras ubicaciones](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations).

## <a name="analyzing-metrics"></a>Análisis de métricas

Puede analizar las métricas de Azure IoT Hub con las de otros servicios de Azure mediante el explorador de métricas si abre **Métricas** en el menú de **Azure Monitor**. Consulte [Introducción al explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md) para más información sobre esta herramienta.

En Azure Portal, puede seleccionar **Métricas** en **Supervisión** en el panel izquierdo del centro de IoT para abrir el explorador de métricas con el ámbito establecido de forma predeterminada en las métricas de plataforma emitidas por el centro de IoT:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="Captura de pantalla que muestra la página del explorador de métricas de un centro de IoT." border="true":::

Para obtener una lista de las métricas de plataforma recopiladas para Azure IoT Hub, vea [Métricas en la referencia de supervisión de datos de Azure IoT Hub](monitor-iot-hub-reference.md#metrics). Para obtener una lista de las métricas de plataforma recopiladas para todos los servicios de Azure, vea [Métricas compatibles con Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

Para las métricas de plataforma de IoT Hub que se recopilan en unidades de recuento, es posible que algunas agregaciones no estén disponibles o se puedan usar. Para obtener más información, vea [Agregaciones admitidas en la referencia de supervisión de datos de Azure IoT Hub](monitor-iot-hub-reference.md#supported-aggregations).

Algunas métricas de IoT Hub, como las [métricas de enrutamiento](monitor-iot-hub-reference.md#routing-metrics), son multidimensionales. Para estas métricas, puede aplicar [filtros](../azure-monitor/essentials/metrics-charts.md#filters) y [divisiones](../azure-monitor/essentials/metrics-charts.md#apply-splitting) en los gráficos en función de una dimensión.

## <a name="analyzing-logs"></a>Análisis de datos

Los datos de los registros de Azure Monitor se almacenan en tablas, cada una con un conjunto propio de propiedades únicas. Los datos de estas tablas están asociados a un área de trabajo de Log Analytics y se pueden consultar en Log Analytics. Para obtener más información sobre los registros de Azure Monitor, vea [Información general sobre los registros Azure Monitor](../azure-monitor/logs/data-platform-logs.md) en la documentación de Azure Monitor. 

Para enrutar los datos a registros de Azure Monitor, debe crear una configuración de diagnóstico para enviar los registros de recursos o las métricas de plataforma a un área de trabajo de Log Analytics. Para obtener más información, vea [Recopilación y enrutamiento](#collection-and-routing).

En Azure Portal, puede seleccionar **Registros** en **Supervisión** en el panel izquierdo del centro de IoT para realizar consultas de Log Analytics que, de forma predeterminada, tendrán como ámbito los registros y las métricas recopiladas en los registros de Azure Monitor para el centro de IoT.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="Página de registros de un centro de IoT.":::

Para obtener una lista de las tablas que usan los registros de Azure Monitor y que Log Analytics puede consultar, vea [Tablas de registros de Azure Monitor en la referencia de supervisión de datos de Azure IoT Hub](monitor-iot-hub-reference.md#azure-monitor-logs-tables).

Todos los registros de recursos de Azure Monitor tienen los mismos campos seguidos de campos específicos del servicio. El esquema común se describe en [Esquema de registros de recursos de Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Puede encontrar el esquema y las categorías de registros de recursos recopilados para Azure IoT Hub en [Registros de recursos en la referencia de supervisión de datos de Azure IoT Hub](monitor-iot-hub-reference.md#resource-logs).

El [registro de actividad](../azure-monitor/essentials/activity-log.md) es un registro de plataforma de Azure que proporciona conclusiones sobre los eventos del nivel de suscripción. Puede verlo de forma independiente o enrutarlo a registros de Azure Monitor, donde puede realizar consultas mucho más complejas mediante Log Analytics.  

Al enrutar métricas de plataforma de IoT Hub a registros de Azure Monitor, tenga en cuenta lo siguiente:

- Las métricas de plataforma siguientes no se pueden exportar a través de la configuración de diagnóstico: *Dispositivos conectados (versión preliminar)* y *Número total de dispositivos (versión preliminar)* .

- Las métricas multidimensionales, por ejemplo algunas [métricas de enrutamiento](monitor-iot-hub-reference.md#routing-metrics), se exportan en la actualidad como métricas planas de una sola dimensión que se agregan a través de valores de dimensión. Para obtener más información, vea [Exportación de métricas de plataforma a otras ubicaciones](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations).

Para obtener algunas consultas comunes con IoT Hub, vea [Consultas de Kusto de ejemplo](#sample-kusto-queries). Para obtener más información sobre el uso de consultas de Log Analytics, vea [Introducción a las consultas de registro en Azure Monitor](../azure-monitor/logs/log-query-overview.md).

### <a name="sdk-version-in-iot-hub-logs"></a>Versión del SDK en registros de IoT Hub

Algunas operaciones en los registros de recursos de IoT Hub devuelven una propiedad `sdkVersion` en su objeto `properties`. En estas operaciones, cuando una aplicación de back-end o de dispositivo usa uno de los SDK de Azure IoT, esta propiedad contiene información sobre el SDK que se usa, la versión del SDK y la plataforma donde se ejecuta el SDK. En el ejemplo siguiente se muestra la propiedad `sdkVersion` emitida para una operación [`deviceConnect`](monitor-iot-hub-reference.md#connections) cuando se usa el SDK de dispositivo de Node.js: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"`. Este es un ejemplo del valor emitido para el SDK de .NET (C#): `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"`.

En la tabla siguiente muestra el nombre del SDK que se usa para los distintos SDK de Azure IoT:

| Nombre del SDK en la propiedad sdkVersion | Lenguaje |
|----------|----------|
| .NET | .NET (C#) |
| microsoft.azure.devices | SDK de servicio de .NET (C#) |
| microsoft.azure.devices.client | SDK de dispositivo de .NET (C#) |
| iothubclient | SDK de dispositivo de C o Python v1 (en desuso) |
| iothubserviceclient | SDK de servicio de C o Python v1 (en desuso) |
| azure-iot-device-iothub-py | SDK de dispositivo de Python |
| azure-iot-device | SDK de dispositivo de Node.js |
| azure-iothub | SDK de servicio de Node.js |
| com.microsoft.azure.iothub-java-client | SDK de dispositivo de Java |
| com.microsoft.azure.iothub.service.sdk | SDK de servicio de Java |
| com.microsoft.azure.sdk.iot.iot-device-client | SDK de dispositivo de Java |
| com.microsoft.azure.sdk.iot.iot-service-client | SDK de servicio de Java |
| C | C insertado |
| C + (OSSimplified = Azure RTOS) | Azure RTOS |

Puede extraer la propiedad de la versión del SDK al realizar consultas en los registros de recursos de IoT Hub. Por ejemplo, la consulta siguiente extrae la propiedad de la versión del SDK (y el id. del dispositivo) de las propiedades devueltas por las operaciones de conexiones. Estas dos propiedades se escriben en los resultados junto con la hora de la operación y el id. de recurso del centro de IoT al que se conecta el dispositivo.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Ejemplos de consultas de Kusto

> [!IMPORTANT]
> Al seleccionar **Registros** en el menú del centro de IoT, Log Analytics se abre con el ámbito de la consulta establecido en el centro de IoT actual. Esto significa que las consultas de registro solo incluirán datos de ese recurso. Si quiere ejecutar una consulta que incluya datos de otros centros de IoT o de otros servicios de Azure, seleccione **Registros** en el menú **Azure Monitor**. Consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](../azure-monitor/logs/scope.md) para obtener más información.

A continuación se muestran las consultas que puede usar para facilitar la supervisión del centro de IoT.

- Errores de conectividad: se identifican los errores de conexión del dispositivo.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Errores de limitación: se identifican los dispositivos que han realizado la mayoría de las solicitudes que han provocado errores de limitación.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Puntos de conexión inactivos: se identifican los puntos de conexión inactivos o incorrectos en función de cuántas veces se ha notificado el problema, así como el motivo.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Resumen de errores: recuento de los errores en todas las operaciones por tipo.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Dispositivos conectados recientemente: lista de los dispositivos que IoT Hub ha visto conectarse en el período de tiempo especificado.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- Versión del SDK de los dispositivos: lista de dispositivos y sus versiones de SDK para conexiones de dispositivos u operaciones gemelas de dispositivo en la nube.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Lectura de registros de Azure Event Hubs

Una vez configurados los registros mediante la configuración de diagnóstico, puede crear aplicaciones que lean los registros, a fin de poder actuar en función de la información que contienen. Este código de ejemplo recupera registros de un centro de eventos:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>Alertas

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Puede establecer alertas en [métricas](../azure-monitor/alerts/alerts-metric-overview.md), [registros](../azure-monitor/alerts/alerts-unified-log.md) y el [registro de actividad](../azure-monitor/alerts/activity-log-alerts.md). Cada tipo de alerta tiene sus ventajas y desventajas.

Al crear una regla de alerta basada en métricas de plataforma, recuerde que para las métricas de plataforma de IoT Hub que se recopilan en unidades de recuento, es posible que algunas agregaciones no estén disponibles o se puedan usar. Para obtener más información, vea [Agregaciones admitidas en la referencia de supervisión de datos de Azure IoT Hub](monitor-iot-hub-reference.md#supported-aggregations).

## <a name="monitor-per-device-disconnects-with-event-grid"></a>Supervisión de las desconexiones por dispositivo con Event Grid

Azure Monitor proporciona una métrica, *Dispositivos conectados*, que puede usar para supervisar el número de dispositivos conectados a IoT Hub y desencadenar una alerta cuando el número de dispositivos conectados caiga por debajo de un umbral. Si bien esto puede ser suficiente para algunos escenarios, [Azure Event Grid](../event-grid/index.yml) proporciona una solución de supervisión por dispositivo de baja latencia que puede usar para realizar un seguimiento de las conexiones de los dispositivos para la infraestructura y los dispositivos críticos.

Con Event Grid, puede suscribirse a los [eventos **DeviceConnected** y **DeviceDisconnected**](iot-hub-event-grid.md#event-types) de IoT Hub para desencadenar alertas y supervisar el estado de conexión del dispositivo. Event Grid proporciona una latencia de eventos mucho menor que la de Azure Monitor y se puede supervisar por dispositivo, en lugar de por el número total de dispositivos conectados. Estos factores hacen de Event Grid el método preferido para supervisar las conexiones de los dispositivos críticos y la infraestructura. Se recomienda encarecidamente usar Event Grid para supervisar las conexiones de dispositivos en entornos de producción.

Para obtener información más detallada sobre la supervisión de las conexiones de dispositivo con Event Grid y Azure Monitor, consulte [Supervisión, diagnóstico y solución de problemas de desconexión con Azure IoT Hub](iot-hub-troubleshoot-connectivity.md).

## <a name="next-steps"></a>Pasos siguientes

- Vea [Referencia de supervisión de datos de Azure IoT Hub](monitor-iot-hub-reference.md) para obtener una referencia de las métricas, los registros y otros valores importantes creados por [nombre del servicio].

- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
