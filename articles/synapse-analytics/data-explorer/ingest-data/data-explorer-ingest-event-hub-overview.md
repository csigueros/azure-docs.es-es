---
title: Conexión de datos de centro de eventos para el Explorador de datos de Azure Synapse (versión preliminar)
description: En este artículo se proporciona información general sobre cómo ingerir (cargar) datos en el Explorador de datos de Azure Synapse desde el centro de eventos.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: a87bb5a7876cc1f6d83919c3faa7dc2f3de3b2f2
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715784"
---
# <a name="event-hub-data-connection-preview"></a>Conexión de datos de centro de eventos (versión preliminar)

[Azure Event Hubs](../../../event-hubs/event-hubs-about.md) es una plataforma de streaming de macrodatos y un servicio de ingesta de eventos. El Explorador de datos de Azure Synapse ofrece la ingesta de datos continua desde centros de eventos administrados por el cliente.

La canalización de ingesta del centro de eventos transfiere los eventos al Explorador de datos de Azure Synapse en varios pasos. En primer lugar, se crea un centro de eventos en Azure Portal. Después, se crea una tabla de destino en el Explorador de datos de Azure Synapse en la que se realizará la ingesta de [datos en un formato determinado](#data-format) mediante el uso de las [propiedades de ingesta](#ingestion-properties) especificadas. La conexión de Event Hubs debe conocer el [enrutamiento de eventos](#events-routing). Los datos se insertan con las propiedades seleccionadas según la [asignación de propiedades del sistema de eventos](#event-system-properties-mapping). [Cree una conexión](#event-hub-connection) a Event Hubs para [crear un centro de eventos](#create-an-event-hub) y [enviar eventos](#send-events). Este proceso se puede administrar desde [Azure Portal](data-explorer-ingest-event-hub-portal.md), mediante programación con [C#](data-explorer-ingest-event-hub-csharp.md) o [Python](data-explorer-ingest-event-hub-python.md) o con una [plantilla de Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md).

Para obtener información general sobre la ingesta de datos en el Explorador de datos de Azure Synapse, consulte [Introducción a la ingesta de datos del Explorador de datos de Azure Synapse](data-explorer-ingest-data-overview.md).

## <a name="data-format"></a>Formato de datos

* Los datos se leen desde el centro de eventos en forma de objetos [EventData](/dotnet/api/microsoft.servicebus.messaging.eventdata).
* Consulte los [formatos admitidos](data-explorer-ingest-data-supported-formats.md).
    > [!NOTE]
    > Event Hubs no admite el formato .raw.

* Los datos se pueden comprimir mediante el algoritmo de compresión `GZip`. Especifique el valor de `Compression` en [propiedades de la ingesta](#ingestion-properties).
   * No se admite la compresión de datos para los formatos comprimidos (Avro, Parquet, ORC).
   * No se admiten la codificación personalizada ni las [propiedades del sistema](#event-system-properties-mapping) insertadas para los datos comprimidos.
  
## <a name="ingestion-properties"></a>Propiedades de la ingesta

Las propiedades de la ingesta indican el proceso de ingesta, dónde enrutar los datos y cómo procesarlos. Puede especificar las [propiedades de la ingesta](data-explorer-ingest-data-properties.md) de eventos mediante [EventData.Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties#Microsoft_ServiceBus_Messaging_EventData_Properties). Puede establecer las siguientes propiedades:

|Propiedad |Descripción|
|---|---|
| Tabla | Nombre (distingue mayúsculas de minúsculas) de la tabla de destino existente. Invalida el valor de `Table` establecido en el panel `Data Connection`. |
| Formato | Formato de datos. Invalida el valor de `Data format` establecido en el panel `Data Connection`. |
| IngestionMappingReference | Nombre de la [asignación de ingesta](/azure/data-explorer/kusto/management/create-ingestion-mapping-command?context=/azure/synapse-analytics/context/context) existente que se va a usar. Invalida el valor de `Column mapping` establecido en el panel `Data Connection`.|
| Compresión | Compresión de datos, `None` (predeterminado) o compresión `GZip`.|
| Encoding | Codificación de datos, el valor predeterminado es UTF8. Puede ser cualquiera de las [codificaciones compatibles con .NET](/dotnet/api/system.text.encoding#remarks). |
| Etiquetas | Una lista de [etiquetas](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#extent-tagging) que se asocian a los datos ingeridos, cuyo formato es una cadena de matriz JSON. Hay [implicaciones de rendimiento](/azure/data-explorer/kusto/management/extents-overview?context=/azure/synapse-analytics/context/context#ingest-by-extent-tags) al usar etiquetas. |

> [!NOTE]
> Solamente se ingieren los eventos en cola después de crear la conexión de datos.

## <a name="events-routing"></a>Enrutamiento de eventos

Al configurar una conexión del centro de eventos con el clúster del Explorador de datos de Azure Synapse, se especifican las propiedades de la tabla de destino (nombre de tabla, formato de datos, compresión y asignación). El enrutamiento predeterminado de los datos también es conocido como `static routing` (enrutamiento estático).
También puede especificar las propiedades de la tabla de destino para cada evento mediante las propiedades del evento. La conexión enrutará dinámicamente los datos tal y como se especifica en [EventData.Properties](/dotnet/api/microsoft.servicebus.messaging.eventdata.properties#Microsoft_ServiceBus_Messaging_EventData_Properties), lo que invalida las propiedades estáticas de este evento.

En el ejemplo siguiente, se establecen los detalles del centro de eventos y se envían datos de métricas meteorológicas a la tabla `WeatherMetrics`.
Los datos están en formato `json`. `mapping1` está predefinido en la tabla `WeatherMetrics`.

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="event-system-properties-mapping"></a>Asignación de propiedades del sistema de eventos

Las propiedades del sistema almacenan las propiedades establecidas por el servicio Event Hubs en el momento en el que se pone en cola el evento. La conexión con el centro de eventos del Explorador de datos de Azure Synapse insertará las propiedades seleccionadas en la carga de datos de la tabla.

[!INCLUDE [event-hub-system-mapping](../includes/data-explorer-event-hub-system-mapping.md)]

### <a name="system-properties"></a>Propiedades del sistema

Event Hubs expone las siguientes propiedades del sistema:

|Propiedad |Tipo de datos |Descripción|
|---|---|---|
| x-opt-enqueued-time |datetime | Hora UTC en la que se puso en cola el evento |
| x-opt-sequence-number |long | Número de secuencia lógica del evento en el flujo de partición del centro de eventos
| x-opt-offset |string | Desplazamiento del evento en relación con el flujo de partición del centro de eventos. El identificador de desplazamiento es único dentro de una partición del flujo del centro de eventos. |
| x-opt-publisher |string | Nombre del publicador, si el mensaje se envió a un punto de conexión del publicador |
| x-opt-partition-key |string |Clave de partición de la partición correspondiente que almacenó el evento |

<!-- When you work with [IoT Central](https://azure.microsoft.com/services/iot-central/) event hubs, you can also embed IoT Hub system properties in the payload. For the complete list, see [IoT Hub system properties](ingest-data-iot-hub-overview.md#event-system-properties-mapping). -->

Si ha seleccionado **Propiedades del sistema de eventos** en la sección **Origen de datos** de la tabla, debe incluir las propiedades en el esquema de la tabla y la asignación.

[!INCLUDE [data-explorer-container-system-properties](../includes/data-explorer-container-system-properties.md)]

## <a name="event-hub-connection"></a>Conexión del centro de eventos

> [!Note]
> Para obtener el mejor rendimiento, cree todos los recursos en la misma región que el clúster del Explorador de datos de Azure Synapse.

### <a name="create-an-event-hub"></a>Creación de un centro de eventos

Si aún no lo tiene, [cree un centro de eventos](../../../event-hubs/event-hubs-create.md). La conexión a Event Hubs se puede administrar desde [Azure Portal](data-explorer-ingest-event-hub-portal.md), mediante programación con [C#](data-explorer-ingest-event-hub-csharp.md) o [Python](data-explorer-ingest-event-hub-python.md) o con una [plantilla de Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md).

> [!Note]
> * El número de particiones no es modificable, por lo que debería tener en cuenta el escalado a largo plazo a la hora de configurar este número.
> * El grupo de consumidores *debe* ser único por consumidor. Cree un grupo de consumidores dedicado para la conexión del Explorador de datos de Azure Synapse.

## <a name="send-events"></a>Envío de eventos

Examine la [aplicación de ejemplo](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que genera los datos y los envía a un centro de eventos.

Para obtener un ejemplo de cómo generar datos de ejemplo, consulte [Ingesta de datos del centro de eventos en el Explorador de datos de Azure Synapse](data-explorer-ingest-event-hub-portal.md#generate-sample-data).

## <a name="set-up-geo-disaster-recovery-solution"></a>Configuración de una solución de recuperación ante desastres geográfica

Event Hubs ofrece una solución de [recuperación ante desastres geográfica](../../../event-hubs/event-hubs-geo-dr.md). El Explorador de datos de Azure Synapse no admite los espacios de nombres del centro de eventos con `Alias`. Para implementar la recuperación ante desastres geográfica en la solución, cree dos conexiones de datos del centro de eventos: una para el espacio de nombres principal y otra para el espacio de nombres secundario. El Explorador de datos de Azure Synapse escuchará en ambas conexiones del centro de eventos.

> [!NOTE]
> Es responsabilidad del usuario implementar una conmutación por error del espacio de nombres principal en el espacio de nombres secundario.

## <a name="next-steps"></a>Pasos siguientes

- [Ingesta de datos del centro de eventos en el Explorador de datos de Azure Synapse](data-explorer-ingest-event-hub-portal.md)
- [Creación de una conexión de datos de centro de eventos para el Explorador de datos de Azure Synapse con C#](data-explorer-ingest-event-hub-csharp.md)
- [Creación de una conexión de datos de centro de eventos para el Explorador de datos de Azure Synapse con Python](data-explorer-ingest-event-hub-python.md)
- [Creación de una conexión de datos de centro de eventos para el Explorador de datos de Azure Synapse mediante una plantilla de Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md)