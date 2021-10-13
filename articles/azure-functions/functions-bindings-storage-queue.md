---
title: Introducción al desencadenador y enlaces de Azure Queue Storage para Azure Functions
description: Obtenga información acerca de cómo usar enlaces de salida y desencadenador de Azure Queue Storage en Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 252a3316ae14e17c2712547f088477d74da2c66e
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658695"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Introducción al desencadenador y enlaces de Azure Queue Storage para Azure Functions

Azure Functions puede ejecutarse a medida que se crean nuevos mensajes de Azure Queue Storage y puede escribir mensajes de cola dentro de una función.

| Acción | Tipo |
|---------|---------|
| Ejecución de una función cuando cambian los datos de Queue Storage | [Desencadenador](./functions-bindings-storage-queue-trigger.md) |
| Escritura de mensajes de Queue Storage |[Enlace de salida](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>Adición a la aplicación de Functions

### <a name="functions-2x-and-higher"></a>Functions 2.x y superiores

Para trabajar con el desencadenador y los enlaces, es necesario hacer referencia al paquete adecuado. En las bibliotecas de clases de .NET se usa el paquete NuGet, mientras que en los demás tipos de aplicaciones se emplea el conjunto de extensiones.

| Idioma                                        | Agregar mediante...                                   | Observaciones 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalación del [paquete NuGet], versión 3.x | |
| Script de C#, Java, JavaScript, Python, PowerShell | Registro de [conjunto de extensiones]          | Se recomienda usar la [extensión Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) con Visual Studio Code. |
| Script de C# (solo en línea en Azure Portal)         | Adición de un enlace                            | Para actualizar extensiones de enlace existentes sin tener que volver a publicar la aplicación de funciones, vea [Actualización de las extensiones]. |

#### <a name="storage-extension-5x-and-higher"></a>Extensión de Storage 5.x y versiones posteriores

Hay disponible una nueva versión de la extensión de enlaces de Storage en un [paquete NuGet en versión preliminar](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.3). Esta versión preliminar presenta la posibilidad de [conectarse con una identidad en lugar de un secreto](./functions-reference.md#configure-an-identity-based-connection). En el caso de las aplicaciones .NET, también cambian los tipos con los que se puede enlazar; así, los tipos `WindowsAzure.Storage` y `Microsoft.Azure.Storage` se reemplazan por otros más recientes de [Azure.Storage.Queues](/dotnet/api/azure.storage.queues).

> [!NOTE]
> El paquete en versión preliminar no se incluye en un conjunto de extensiones y debe instalarse manualmente. En el caso de las aplicaciones .NET, agregue una referencia al paquete. Con el resto de tipos de aplicaciones, consulte [Actualización de las extensiones].

[core tools]: ./functions-run-local.md
[conjunto de extensiones]: ./functions-bindings-register.md#extension-bundles
[Paquete NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Actualización de las extensiones]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

Las aplicaciones de Functions 1.x tienen automáticamente una referencia al paquete NuGet [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs), versión 2.x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>configuración de host.json

[!INCLUDE [functions-host-json-section-intro](../../includes/functions-host-json-section-intro.md)]

> [!NOTE]
> Para obtener una referencia de host.json en Functions 1.x, consulte la [referencia de host.json para Azure Functions, versión 1.x](functions-host-json-v1.md).

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------|
|maxPollingInterval|00:01:00|Intervalo máximo entre sondeos de la cola. El intervalo mínimo es 00:00:00.100 (100 milisegundos). Los intervalos se incrementan hasta `maxPollingInterval`. El valor predeterminado de `maxPollingInterval` es 00:01:00 (1 minuto). `maxPollingInterval` no debe ser menor que 00:00:00.100 (100 milisegundos). En Functions 2.x y versiones posteriores, el tipo de datos es `TimeSpan`. En Functions 1.x, se expresa en milisegundos.|
|visibilityTimeout|00:00:00|Intervalo de tiempo entre los reintentos cuando se produce un error al procesar un mensaje. |
|batchSize|16|El número de mensajes en cola que el runtime de Functions recupera simultáneamente y procesa en paralelo. Cuando el número que se está procesando llega a `newBatchThreshold` el runtime obtiene otro lote y empieza a procesar esos mensajes. Por lo tanto, el número máximo de mensajes simultáneos que se procesan por función es `batchSize` más `newBatchThreshold`. Este límite se aplica por separado a cada función desencadenada por la cola. <br><br>Si desea evitar la ejecución en paralelo de los mensajes de una cola, puede establecer `batchSize` en 1. Sin embargo, este valor elimina la simultaneidad siempre y cuando la aplicación de funciones se ejecute en una única máquina virtual (VM). Si la aplicación de función se escala horizontalmente a varias máquinas virtuales, cada una de ellas podría ejecutar una instancia de cada función desencadenada por la cola.<br><br>El valor máximo de `batchSize` es 32. |
|maxDequeueCount|5|Número de veces que se intenta procesar un mensaje antes de pasarlo a la cola de mensajes dudosos.|
|newBatchThreshold|N*batchSize/2|Siempre que el número de mensajes que se procesan simultáneamente llega a este número, el runtime recupera otro lote.<br><br>`N` representa el número de vCPU disponibles al ejecutarse en planes Premium o App Service. Su valor es `1` para el plan de consumo.|
|messageEncoding|base64| Esta opción solo está disponible en la [versión de extensión 5.0.0 y posteriores](#storage-extension-5x-and-higher). Representa el formato de codificación de los mensajes. Los valores válidos son `base64` y `none`.|

## <a name="next-steps"></a>Pasos siguientes

- [ de una función cuando cambian los datos de Queue Storage (desencadenador)](./functions-bindings-storage-queue-trigger.md)
- [Escritura de mensajes de Queue Storage (enlace de salida)](./functions-bindings-storage-queue-output.md)
