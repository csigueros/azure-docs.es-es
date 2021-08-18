---
title: Referencia de host.json para Azure Functions 1.x
description: Documentación de referencia para el archivo host.json de Azure Functions con el entorno en tiempo de ejecución de la versión 1.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 8466827402a5a5f785b65ea70887cc868f8667af
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121742654"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>Referencia de host.json para Azure Functions 1.x

> [!div class="op_single_selector" title1="Seleccione la versión del entorno de ejecución de Azure Functions que usa: "]
> * [Versión 1](functions-host-json-v1.md)
> * [Versión 2](functions-host-json.md)

El archivo de metadatos *host.json* contiene las opciones de configuración global que afectan a todas las funciones de dicha aplicación de función. En este artículo se incluye una lista de las opciones de configuración disponibles para el entorno en tiempo de ejecución de la versión 1. El esquema JSON está en http://json.schemastore.org/host.

> [!NOTE]
> Este artículo trata sobre Azure Functions 1.x.  Para obtener una referencia de host.json en Functions 2.x y versiones posteriores, consulte [Referencia de host.json para Azure Functions 2.x](functions-host-json.md).

Otras opciones de configuración de aplicación de función se administran en su [configuración de la aplicación](functions-app-settings.md).

Algunas opciones de configuración de host.json solo se usan con la ejecución local en el archivo [local.settings.json](functions-develop-local.md#local-settings-file).

## <a name="sample-hostjson-file"></a>Archivo host.json de ejemplo

El siguiente archivo *host.json* de ejemplo tiene especificadas todas las opciones posibles.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

Las siguientes secciones de este artículo explican cada propiedad de nivel superior. Todas son opcionales, a menos que se indique lo contrario.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Opciones de configuración para los [desencadenadores y enlaces de Azure Cosmos DB](functions-bindings-cosmosdb.md).

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------|
|GatewayMode|Puerta de enlace|Modo de conexión que usa la función al conectarse al servicio de Azure Cosmos DB. Las opciones son `Direct` y `Gateway`|
|Protocolo|Https|Protocolo de conexión que usa la función al conectarse al servicio de Azure Cosmos DB.  Lea [aquí para obtener una explicación de los dos modos](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|N/D|Prefijo de concesión que se usará en todas las funciones de una aplicación.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Opciones de configuración para los [desencadenadores y enlaces del Centro de eventos](functions-bindings-event-hubs.md#functions-1x).

## <a name="functions"></a>functions

Lista de las funciones que el host de trabajo ejecuta. Una matriz vacía significa ejecutar todas las funciones. Su uso está previsto solo cuando se [ejecuta localmente](functions-run-local.md). En cambio, en las aplicaciones de función de Azure, siga los pasos de [Deshabilitamiento de funciones en Azure Functions](disable-function.md) para deshabilitar funciones específicas en lugar de usar esta configuración.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Indica la duración del tiempo de espera para todas las funciones. En un plan de consumo sin servidor, el intervalo válido es de 1 segundo a 10 minutos, y el valor predeterminado es 5 minutos. En un plan de App Service, no hay ningún límite general y el valor predeterminado es _null_, lo que indica que no hay tiempo de espera.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Configuración del [monitor de estado de host](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|enabled|true|Especifica si está habilitada la característica. | 
|healthCheckInterval|10 segundos|El intervalo de tiempo entre las comprobaciones periódicas de mantenimiento en segundo plano. | 
|healthCheckWindow|2 minutes|Una ventana de tiempo deslizante usada en combinación con el valor `healthCheckThreshold`.| 
|healthCheckThreshold|6|Número máximo de veces que puede producirse un error en la comprobación de mantenimiento antes de que se inicie un reciclaje del host.| 
|counterThreshold|0.80|El umbral en el que un contador de rendimiento se considerará incorrecto.| 

## <a name="http"></a>http

Opciones de configuración para los [desencadenadores y enlaces HTTP](functions-bindings-http-webhook.md).

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|Cuando se habilita, esta configuración hace que la canalización de procesamiento de la solicitud compruebe periódicamente contadores de rendimiento del sistema como conexiones, subprocesos, procesos, memoria o cpu y, si cualquiera de esos contadores superan un umbral alto integrado (80 %), las solicitudes se rechazarán con una respuesta 429 "Ocupado" hasta que los contadores vuelvan a niveles normales.|
|maxConcurrentRequests|sin enlazar (`-1`)|Número máximo de funciones HTTP que se ejecutarán en paralelo. Esto permite controlar la simultaneidad, que a su vez puede ayudar a administrar el uso de recursos. Por ejemplo, podría tener una función HTTP que utiliza una gran cantidad de recursos del sistema (memoria/cpu/sockets) y causa problemas cuando la simultaneidad es demasiado alta. O bien podría tener una función que realiza solicitudes de salida a un servicio de terceros y puede que haya que limitar la velocidad de dichas llamadas. En estos casos puede ayudar aplicar una limitación.|
|maxOutstandingRequests|sin enlazar (`-1`)|Número máximo de solicitudes pendientes que se mantienen en un momento dado. Este límite incluye las solicitudes que están en cola pero no han empezado a ejecutarse, así como todas las ejecuciones en curso. Se rechazan todas las solicitudes entrantes que superen este límite con una respuesta 429 "Too Busy" (demasiado ocupado). Esto permite que los llamadores empleen estrategias de reintento basadas en tiempo y también le ayuda a controlar las latencias de solicitud máximas. Únicamente se controlan los movimientos de la cola que se producen dentro de la ruta de ejecución del host del script. Otras colas, como la cola de solicitudes de ASP.NET, siguen en efecto y no se ven alteradas por esta opción de configuración.|
|routePrefix|api|Prefijo de ruta que se aplica a todas las rutas. Use una cadena vacía para quitar el prefijo predeterminado. |

## <a name="id"></a>id

Identificador único de un host de trabajo. Puede ser un GUID en minúsculas sin guiones. Requerido cuando se realiza la ejecución localmente. Cuando se ejecuta en Azure, se recomienda no establecer un valor de identificador. Cuando `id` se omite, se genera un identificador automáticamente en Azure. 

Si comparte una cuenta de almacenamiento entre varias aplicaciones de función, asegúrese de que cada aplicación de función tiene otro `id`. Puede omitir la propiedad `id` o establecer manualmente el `id` de cada aplicación de función en un valor diferente. El desencadenador de temporizador utiliza un bloqueo de almacenamiento para asegurarse de que solo hay una instancia de temporizador cuando una aplicación de función se escala a varias instancias. Si dos aplicaciones de función comparten el mismo `id` y cada una usa un desencadenador de temporizador, solo se ejecutará un temporizador.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>logger

Controla el filtrado de los registros escritos por un [objeto ILogger](functions-dotnet-class-library.md#ilogger) o [context.log](functions-reference-node.md#contextlog-method).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|categoryFilter|N/D|Especifica el filtrado por categoría| 
|defaultLevel|Information|Para las categorías no especificadas en la matriz `categoryLevels`, envía registros en este nivel y superiores a Application Insights.| 
|categoryLevels|N/D|Matriz de categorías que especifica el nivel de registro mínimo para enviar a Application Insights para cada categoría. La categoría especificada aquí controla todas las categorías que comienzan por el mismo valor, y los valores más largos tienen prioridad. En el archivo *host.json* del ejemplo anterior, todas las categorías que comienzan por "Host.Aggregator" se registran en el nivel `Information`. Todas las demás categorías que comienzan por "Host", como "Host.Executor", se registran en el nivel `Error`.| 

## <a name="queues"></a>queues

Opciones de configuración para los [desencadenadores y enlaces de la cola de Storage](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|maxPollingInterval|60000|Intervalo máximo, en milisegundos, entre sondeos de la cola.| 
|visibilityTimeout|0|Intervalo de tiempo entre los reintentos cuando se produce un error al procesar un mensaje.| 
|batchSize|16|El número de mensajes en cola que el runtime de Functions recupera simultáneamente y procesa en paralelo. Cuando el número que se está procesando llega a `newBatchThreshold` el runtime obtiene otro lote y empieza a procesar esos mensajes. Por lo tanto, el número máximo de mensajes simultáneos que se procesan por función es `batchSize` más `newBatchThreshold`. Este límite se aplica por separado a cada función desencadenada por la cola. <br><br>Si desea evitar la ejecución en paralelo de los mensajes de una cola, puede establecer `batchSize` en 1. Sin embargo, este valor solo elimina la simultaneidad siempre y cuando la aplicación de función se ejecute en una única máquina virtual (VM). Si la aplicación de función se escala horizontalmente a varias máquinas virtuales, cada una de ellas podría ejecutar una instancia de cada función desencadenada por la cola.<br><br>El valor máximo de `batchSize` es 32. | 
|maxDequeueCount|5|Número de veces que se intenta procesar un mensaje antes de pasarlo a la cola de mensajes dudosos.| 
|newBatchThreshold|batchSize/2|Siempre que el número de mensajes que se procesan simultáneamente llega a este número, el runtime recupera otro lote.| 

## <a name="sendgrid"></a>SendGrid

Opción de configuración para [el enlace de salida de SendGrind](functions-bindings-sendgrid.md).

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
}    
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|desde|N/D|Dirección de correo electrónico del remitente en todas las funciones.| 

## <a name="servicebus"></a>serviceBus

Opción de configuración para los [desencadenadores y enlaces de Service Bus](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00",
      "autoComplete": true
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|maxConcurrentCalls|16|Número máximo de llamadas simultáneas a la devolución de llamada que el bombeo de mensajes debe iniciar. De forma predeterminada, el entorno de ejecución de Functions procesa simultáneamente varios mensajes. Para indicar al entorno de ejecución que procese solo los mensajes de una única cola o tema, establezca `maxConcurrentCalls` en 1. | 
|prefetchCount|N/D|Valor predeterminado de PrefetchCount que utilizará el receptor de mensajes subyacente.| 
|autoRenewTimeout|00:05:00|Duración máxima dentro de la cual el bloqueo de mensajes se renovará automáticamente.|
|autoComplete|true|Cuando sea true, el desencadenador completará el procesamiento de mensajes automáticamente cuando la operación se ejecute correctamente. Cuando sea false, es responsabilidad de la función completar el mensaje antes de volver.|

## <a name="singleton"></a>singleton

Opciones de configuración para el comportamiento de bloqueo Singleton. Para más información, consulte [problema de compatibilidad de GitHub con singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|lockPeriod|00:00:15|Período durante el cual se producen los bloqueos de nivel de función. Los bloqueos se renuevan automáticamente.| 
|listenerLockPeriod|00:01:00|Período durante el cual se producen los bloqueos de agente de escucha.| 
|listenerLockRecoveryPollingInterval|00:01:00|Intervalo de tiempo que se utiliza para la recuperación de bloqueos del agente de escucha si no se pudo adquirir un bloqueo de agente de escucha durante el inicio.| 
|lockAcquisitionTimeout|00:01:00|Cantidad máxima de tiempo que el entorno en tiempo de ejecución intentará adquirir un bloqueo.| 
|lockAcquisitionPollingInterval|N/D|Intervalo entre intentos de adquisición de bloqueo.| 

## <a name="tracing"></a>tracing

*Versión 1.x*

Opciones de configuración para los registros que se crean mediante un objeto `TraceWriter`. Para más información, consulte [Registro de C#].

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------| 
|consoleLevel|info|Nivel de seguimiento para el registro de la consola. Las opciones son: `off`, `error`, `warning`, `info` y `verbose`.|
|fileLoggingMode|debugOnly|Nivel de seguimiento para el registro de archivos. Las opciones son `never`, `always`, `debugOnly`.| 

## <a name="watchdirectories"></a>watchDirectories

Conjunto de [directorios de código compartido](functions-reference-csharp.md#watched-directories) en los que se deben supervisar los cambios.  Garantiza que cuando se cambie el código en estos directorios, las funciones recibirán los cambios.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Vea cómo actualizar el archivo host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Consulte las opciones de configuración global en las variables de entorno](functions-app-settings.md)
