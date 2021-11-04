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
ms.openlocfilehash: f4d7b9d272f097f5be332c8ff0acdf7cb30395ea
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570653"
---
Azure Functions permite crear tareas de replicación de solo configuración que se apoyan en un punto de entrada pregenerado. Los [ejemplos de replicación basada en la configuración para Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) muestran cómo aprovechar [asistentes pregenerados](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) en el propio código o evitar por completo el control de código y tan solo usar la configuración.

## <a name="create-a-replication-task"></a>Creación de una tarea de replicación
Para crear este tipo de tarea de replicación, cree primero otra carpeta debajo de la carpeta de proyecto. El nombre de la nueva carpeta es el nombre de la función, por ejemplo `europeToAsia` o `telemetry`. El nombre no tiene correlación directa con las entidades de mensajería que se usan y solo sirve para identificarlas. Puede crear docenas de funciones en el mismo proyecto.

A continuación, cree un archivo `function.json` en la carpeta. El archivo configura la función. Empiece con el contenido siguiente:

```json
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

En ese archivo, ha de completar tres pasos de configuración que dependen de las entidades que quiera conectar:

1. [Configuración de la dirección de entrada](#configure-the-input-direction)
2. [Configuración de la dirección de salida](#configure-the-output-direction)
3. [Configuración del punto de entrada](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>Configuración de la dirección de entrada

#### <a name="event-hub-input"></a>Entrada del Centro de eventos

Si quiere recibir eventos de un Centro de eventos, agregue información de configuración a la sección superior dentro de "enlaces" que establece los siguientes valores:

* **type**: tipo "eventHubTrigger".
* **connection**: nombre del valor de configuración de la aplicación de la cadena de conexión de Event Hubs. 
* **eventHubName**: nombre de la instancia de Event Hub en el espacio de nombres identificado mediante la cadena de conexión.
* **consumerGroup**: nombre del grupo de consumidores. Recuerde que el nombre se incluye entre signos de porcentaje y, por lo tanto, también hace referencia a un valor de configuración de la aplicación.

```json
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Entrada de la cola de Service Bus

Si quiere recibir eventos de una cola de Service Bus, agregue información de configuración a la sección superior dentro de "enlaces" que establece los siguientes valores:

* **type**: tipo "serviceBusTrigger".
* **connection**: nombre del valor de configuración de la aplicación de la cadena de conexión de Service Bus.
* **queueName**: nombre de la cola de Service Bus en el espacio de nombres identificado mediante la cadena de conexión.

```json
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Entrada de un tema de Service Bus

Si quiere recibir eventos de un tema de Service Bus, agregue información de configuración a la sección superior dentro de "enlaces" que establece los siguientes valores:

* **type**: tipo "serviceBusTrigger".
* **connection**: nombre del valor de configuración de la aplicación de la cadena de conexión de Service Bus. Este valor debe ser `{FunctionName}-source-connection` si quiere utilizar los scripts proporcionados.
* **topicName**: nombre del tema de Service Bus en el espacio de nombres identificado mediante la cadena de conexión.
* **subscriptionName**: nombre de la suscripción de Service Bus en el tema especificado en el espacio de nombres identificado mediante la cadena de conexión.

```json
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>Configuración de la dirección de salida

#### <a name="event-hub-output"></a>Salida del Centro de eventos

Si quiere enviar eventos a un Centro de eventos, agregue información de configuración a la sección inferior dentro de "enlaces" que establece los siguientes valores:

* **type**: tipo "eventHub".
* **connection**: nombre del valor de configuración de la aplicación de la cadena de conexión de Event Hubs. 
* **eventHubName**: nombre de la instancia de Event Hub en el espacio de nombres identificado mediante la cadena de conexión.

```json
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Salida de la cola de Service Bus

Si quiere enviar eventos a una cola de Service Bus, agregue información de configuración a la sección inferior dentro de "enlaces" que establece los siguientes valores:

* **type**: tipo "serviceBus".
* **connection**: nombre del valor de configuración de la aplicación de la cadena de conexión de Service Bus. 
* **queueName**: nombre de la cola de Service Bus en el espacio de nombres identificado mediante la cadena de conexión.

```json
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Salida de un tema de Service Bus

Si quiere enviar eventos a un tema de Service Bus, agregue información de configuración a la sección inferior dentro de "enlaces" que establece los siguientes valores:

* **type**: tipo "serviceBus".
* **connection**: nombre del valor de configuración de la aplicación de la cadena de conexión de Service Bus. 
* **topicName**: nombre del tema de Service Bus en el espacio de nombres identificado mediante la cadena de conexión.

```json
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Configuración del punto de entrada

La configuración del punto de entrada elige una de las tareas de replicación estándar. Si va a modificar el proyecto `Azure.Messaging.Replication`, aquí también puede agregar tareas y hacer referencia a ellas. Por ejemplo:

```json
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

En la tabla siguiente se proporcionan los valores correctos para las combinaciones de orígenes y destinos:

| Source      | Destino      | Punto de entrada 
|-------------|-------------|------------------------------------------------------------------------
| Centro de eventos   | Centro de eventos   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Centro de eventos   | Azure Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Centro de eventos   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Azure Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Directiva de reintentos

Consulte la [documentación de Azure Functions sobre reintentos](../articles/azure-functions/functions-bindings-error-pages.md) para configurar la directiva de reintentos. La configuración de directiva elegida en todos los proyectos de este repositorio configura una estrategia de retroceso exponencial con intervalos de reintento de 5 segundos a 5 minutos con reintentos infinitos para evitar la pérdida de datos.

En el caso de Service Bus, revise la sección ["Uso de la compatibilidad con los reintentos sobre la resistencia del desencadenador"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) para entender la interacción de los desencadenadores y el número máximo de entregas definidos para la cola.

### <a name="build-deploy-and-configure"></a>Compilación, implementación y configuración

Mientras se centra en la configuración, las tareas aún requieren la compilación de una aplicación implementable y la configuración de los hosts de Azure Functions de forma que disponga de toda la información necesaria para conectarse a los puntos de conexión especificados. 

Esto se muestra, junto con scripts reutilizables, en los [ejemplos de replicación basada en la configuración para Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config).
