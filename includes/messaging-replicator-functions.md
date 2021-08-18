---
title: Archivo de inclusión
description: archivo de inclusión
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 38a2f3cd5f1b391b651c89801e4ab3abe92ed448
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112255209"
---
## <a name="what-is-a-replication-task"></a>¿Qué es una tarea de replicación?

Una tarea de replicación recibe eventos de un origen y los reenvía a un destino.
La mayoría de las tareas de replicación reenvían eventos sin cambios y, como máximo, realizan asignaciones entre estructuras de metadatos si los protocolos de origen y de destino varían. 

Normalmente, las tareas de replicación son sin estado, lo que significa que no comparten el estado u otros efectos secundarios en las ejecuciones secuenciales o en paralelo de una tarea. Esto también es cierto para el procesamiento por lotes y el encadenamiento, que se pueden implementar sobre el estado existente de una secuencia. 

Esto hace que las tareas de replicación sean distintas de las de agregación, que suelen ser con estado, y son el dominio de los marcos y servicios de análisis como [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Aplicaciones y tareas de replicación en Azure Functions

En Azure Functions, una tarea de replicación se implementa mediante un [desencadenador](../articles/azure-functions/functions-triggers-bindings.md) que adquiere uno o más mensajes de entrada de un origen configurado y un [enlace de salida](../articles/azure-functions/functions-triggers-bindings.md#binding-direction) que reenvía los mensajes copiados desde el origen a un destino configurado. 

| Desencadenador  | Output |
|----------|--------|
| [Desencadenador de Azure Event Hubs](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Enlace de salida de Azure Event Hubs](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Desencadenador de Azure Service Bus](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Enlace de salida de Azure Service Bus](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp)
| [Desencadenador de Azure IoT Hub](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Enlace de salida de Azure IoT Hub](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp)
| [Desencadenador de Azure Event Grid](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Enlace de salida de Azure Event Grid](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp)
| [Desencadenador Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Enlace de salida de Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp)
| [Desencadenador de Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) | [Enlace de salida de Apache Kafka](https://github.com/azure/azure-functions-kafka-extension)
| [Desencadenador de RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) | [Enlace de salida de RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Enlace de salida de Azure Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md)
||[Enlace de salida de Azure SignalR Service](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp)
||[Enlace de salida de Twilio SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp)

Las tareas de replicación se implementan como en la aplicación de replicación, a través de los mismos métodos de implementación que cualquier otra aplicación de Azure Functions. Puede configurar varias tareas en la misma aplicación. 

Con Azure Functions Premium, varias aplicaciones de replicación pueden compartir el mismo grupo de recursos subyacente, denominado Plan de App Service. Esto significa que puede colocar fácilmente las tareas de replicación escritas en .NET con tareas de replicación escritas en Java, por ejemplo. Esto tendrá importancia si quiere aprovechar bibliotecas específicas, como Apache Camel, que solo están disponibles para Java y si estas son la mejor opción para una ruta de acceso de integración determinada, aunque normalmente preferiría un lenguaje y un entorno de ejecución diferentes para las otras tareas de replicación. 

Siempre que esté disponible, son preferibles los desencadenadores orientados por lotes a los que entregan eventos o mensajes individuales, y siempre debe obtener el evento completo o la estructura del mensaje en lugar de depender de las [expresiones de enlace de parámetros ](../articles/azure-functions/functions-bindings-expressions-patterns.md) de la función de Azure.

El nombre de la función debe reflejar el par de origen y destino que se va a conectar, y debe prefijar las referencias a las cadenas de conexión u otros elementos de configuración de los archivos de configuración de la aplicación con ese nombre. 

### <a name="data-and-metadata-mapping"></a>Asignación de datos y metadatos

Una vez que haya decidido un par de desencadenador de entrada y enlace de salida, tendrá que realizar alguna asignación entre los distintos tipos de eventos o mensajes, a menos que el tipo del desencadenador y la salida sean los mismos.

En el caso de las tareas de replicación sencillas que copian mensajes entre Event Hubs y Service Bus, no es necesario escribir su propio código, sino que puede apoyarse en una [biblioteca de utilidades proporcionada](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) con los ejemplos de replicación.

### <a name="retry-policy"></a>Directiva de reintentos

Para evitar la pérdida de datos durante el evento de disponibilidad en cualquier lado de una función de replicación, debe configurar la directiva de reintentos para que sea sólida. Consulte la [documentación de Azure Functions sobre reintentos](../articles/azure-functions/functions-bindings-error-pages.md) para configurar la directiva de reintentos. 

La configuración de directiva elegida en los proyectos de ejemplo del [repositorio de ejemplo](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) configura una estrategia de retroceso exponencial con intervalos de reintento de 5 segundos a 15 minutos, con reintentos infinitos para evitar la pérdida de datos. 

En el caso de Service Bus, revise la sección ["Uso de la compatibilidad con los reintentos sobre la resistencia del desencadenador"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) para entender la interacción de los desencadenadores y el número máximo de entregas definidos para la cola.

### <a name="setting-up-a-replication-application-host"></a>Configuración de un host de aplicación de replicación

Una aplicación de replicación es un host de ejecución para una o varias tareas de replicación. 

Se trata de una aplicación Azure Functions que está configurada para ejecutarse en el plan de consumo o en un plan de Azure Functions Premium (recomendado). Todas las aplicaciones de replicación deben ejecutarse en una [identidad administrada asignada por el usuario o el sistema](../articles/app-service/overview-managed-identity.md). 

Las plantillas vinculadas Azure Resource Manager (ARM) crean y configuran una aplicación de replicación con lo siguiente:

* una cuenta de Azure Storage para realizar un seguimiento del progreso de la replicación y de los registros,
* una identidad administrada asignada por el sistema y 
* integración de Azure Monitoring y Application Insights para la supervisión.

Las aplicaciones de replicación que deben acceder a Event Hubs enlazadas a una red virtual de Azure (VNet) deben usar el plan Premium de Azure Functions y configurarse para asociarse a la misma VNet, que también es una de las opciones disponibles.


|       | Implementar | Visualización  
|-------|------------------|--------------|---------------|
| **Plan de consumo de Azure Functions** | [![Implementar en Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)|[![Visualizar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Plan Premium de Azure Functions** |[![Implementar en Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Visualizar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **Plan Premium de Azure Functions con VNet** | [![Implementar en Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Visualizar](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Ejemplos

El [repositorio de ejemplos](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) contiene varias muestras de tareas de replicación que copian eventos entre centros de Event Hubs o entre entidades de Service Bus.

Para copiar eventos entre centros de Event Hubs, se usa un desencadenador de centro de eventos con un enlace de salida del centro de eventos:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

Para copiar mensajes entre entidades de Service Bus, se usa el desencadenador de Service Bus y el enlace de salida:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Los métodos asistentes pueden facilitar la replicación entre Event Hubs y Service Bus:

| Source      | Destino      | Punto de entrada 
|-------------|-------------|------------------------------------------------------------------------
| Centro de eventos   | Centro de eventos   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Centro de eventos   | Azure Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Centro de eventos   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Azure Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>Supervisión

Para obtener información sobre cómo puede supervisar la aplicación de replicación, consulte la [sección de supervisión](../articles/azure-functions/configure-monitoring.md) de la documentación de Azure Functions.

Una herramienta visual especialmente útil para supervisar las tareas de replicación es el [Mapa de aplicación](../articles/azure-monitor/app/app-map.md) de Application Insights, que se genera automáticamente a partir de la información de supervisión capturada y permite explorar la fiabilidad y el rendimiento de las transferencias de origen y destino de la tarea de replicación.

Para obtener información de diagnóstico inmediata, puede trabajar con la herramienta [Live Metrics](../articles/azure-monitor/app/live-stream.md) del portal, que proporciona una visualización de baja latencia de los detalles de registro.

## <a name="next-steps"></a>Pasos siguientes

* [Implementaciones de Azure Functions](../articles/azure-functions/functions-deployment-technologies.md)
* [Diagnósticos de Azure Functions](../articles/azure-functions/functions-diagnostics.md)
* [Opciones de redes de Azure Functions](../articles/azure-functions/functions-networking-options.md)
* [Azure Application Insights](../articles/azure-monitor/app/app-insights-overview.md)