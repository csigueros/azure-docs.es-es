---
title: Enlace de salida de Azure Queue Storage para Azure Functions
description: Aprenda a crear mensajes de Azure Queue Storage en Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: devx-track-csharp, cc996988-fb4f-47, devx-track-python
ms.openlocfilehash: 015c61b7f02bec8cec274f3f746b3d693ccdffed
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992748"
---
# <a name="azure-queue-storage-output-bindings-for-azure-functions"></a>Enlaces de salida de Azure Queue Storage para Azure Functions

Azure Functions puede crear nuevos mensajes de Azure Queue Storage mediante la configuración de un enlace de salida.

Para obtener información sobre los detalles de instalación y configuración, vea la [información general](./functions-bindings-storage-queue.md).

## <a name="example"></a>Ejemplo

# <a name="c"></a>[C#](#tab/csharp)

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que crea un mensaje de cola para cada una de las solicitudes HTTP que se reciben.

```csharp
[StorageAccount("MyStorageConnectionAppSetting")]
public static class QueueFunctions
{
    [FunctionName("QueueOutput")]
    [return: Queue("myqueue-items")]
    public static string QueueOutput([HttpTrigger] dynamic input,  ILogger log)
    {
        log.LogInformation($"C# function processed: {input.Text}");
        return input.Text;
    }
}
```

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

En el ejemplo siguiente se muestra un enlace de desencadenador HTTP de un archivo *function.json* y el código de [script de C# (.csx)](functions-reference-csharp.md) que usa el enlace. La función crea un elemento de cola con una carga del objeto **CustomQueueMessage** para cada una de las solicitudes HTTP recibidas.

Este es el archivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "$return",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

En la sección de [configuración](#configuration) se explican estas propiedades.

Este es el código de script de C# que crea un único mensaje de cola:

```cs
public class CustomQueueMessage
{
    public string PersonName { get; set; }
    public string Title { get; set; }
}

public static CustomQueueMessage Run(CustomQueueMessage input, ILogger log)
{
    return input;
}
```

Puede enviar varios mensajes a la vez mediante el uso de un parámetro `ICollector` o `IAsyncCollector`. Este es el código de script de C# que envía varios mensajes, uno con los datos de la solicitud HTTP y otro con valores codificados de forma rígida:

```cs
public static void Run(
    CustomQueueMessage input, 
    ICollector<CustomQueueMessage> myQueueItems, 
    ILogger log)
{
    myQueueItems.Add(input);
    myQueueItems.Add(new CustomQueueMessage { PersonName = "You", Title = "None" });
}
```

# <a name="java"></a>[Java](#tab/java)

 En el ejemplo siguiente se muestra una función de Java que crea un mensaje de cola cuando una solicitud HTTP la desencadena.

```java
@FunctionName("httpToQueue")
@QueueOutput(name = "item", queueName = "myqueue-items", connection = "MyStorageConnectionAppSetting")
 public String pushToQueue(
     @HttpTrigger(name = "request", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS)
     final String message,
     @HttpOutput(name = "response") final OutputBinding<String> result) {
       result.setValue(message + " has been added.");
       return message;
 }
```

En la [biblioteca en tiempo de ejecución de funciones de Java](/java/api/overview/azure/functions/runtime), utilice la anotación `@QueueOutput` en los parámetros cuyo valor se escribiría en Queue Storage.  El tipo de parámetro debe ser `OutputBinding<T>`, donde `T` es cualquier tipo nativo de Java de un POJO.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

En el ejemplo siguiente se muestra un enlace de desencadenador HTTP de un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función crea un elemento de cola para cada una de las solicitudes HTTP recibidas.

Este es el archivo *function.json*:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "authLevel": "function",
      "name": "input"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "myQueueItem",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

En la sección de [configuración](#configuration) se explican estas propiedades.

Este es el código de JavaScript:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

Puede enviar varios mensajes a la vez mediante la definición de una matriz de mensajes para el enlace de salida `myQueueItem`. El código JavaScript envía dos mensajes de cola con valores codificados de forma rígida para cada una de las solicitudes HTTP recibidas.

```javascript
module.exports = function(context) {
    context.bindings.myQueueItem = ["message 1","message 2"];
    context.done();
};
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

En los siguientes ejemplos de código se muestra cómo generar un mensaje de la cola desde una función desencadenada por HTTP. La sección de configuración con el valor de `type` de `queue` define el enlace de salida.

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "Request",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "Response"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "Msg",
      "queueName": "outqueue",
      "connection": "MyStorageConnectionAppSetting"
    }
  ]
}
```

Con esta configuración de enlace, una función de PowerShell puede crear un mensaje de cola mediante `Push-OutputBinding`. En este ejemplo, se crea un mensaje a partir de una cadena de consulta o un parámetro de cuerpo.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = $Request.Query.Message
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

Para enviar varios mensajes a la vez, defina una matriz de mensajes y utilice `Push-OutputBinding` para enviar mensajes al enlace de salida de la cola.

```powershell
using namespace System.Net

# Input bindings are passed in via param block.
param($Request, $TriggerMetadata)

# Write to the Azure Functions log stream.
Write-Host "PowerShell HTTP trigger function processed a request."

# Interact with query parameters or the body of the request.
$message = @("message1", "message2")
Push-OutputBinding -Name Msg -Value $message
Push-OutputBinding -Name Response -Value ([HttpResponseContext]@{
    StatusCode = 200
    Body = "OK"
})
```

# <a name="python"></a>[Python](#tab/python)

En el ejemplo siguiente se muestra cómo generar valores únicos y múltiples en las colas de almacenamiento. La configuración necesaria para *function.json* es la misma en cualquier caso.

Un enlace de cola de almacenamiento se define en *function.json*, donde *type* está establecido en `queue`.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureStorageQueuesConnectionString"
    }
  ]
}
```

Para establecer un mensaje individual en la cola, se pasa un valor único al método `set`.

```python
import azure.functions as func

def main(req: func.HttpRequest, msg: func.Out[str]) -> func.HttpResponse:

    input_msg = req.params.get('message')

    msg.set(input_msg)

    return 'OK'
```

Para crear varios mensajes en la cola, declare un parámetro como el tipo de lista adecuado y pase una matriz de valores (que coincidan con el tipo de lista) al método `set`.

```python
import azure.functions as func
import typing

def main(req: func.HttpRequest, msg: func.Out[typing.List[str]]) -> func.HttpResponse:

    msg.set(['one', 'two'])

    return 'OK'
```

---

## <a name="attributes-and-annotations"></a>Atributos y anotaciones

# <a name="c"></a>[C#](#tab/csharp)

En las [bibliotecas de clase C#](functions-dotnet-class-library.md), use [QueueAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Queues/QueueAttribute.cs).

El atributo se aplica a un parámetro `out` o al valor de retorno de la función. El constructor del atributo toma el nombre de la cola, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Puede establecer la propiedad `Connection` para especificar la cuenta de almacenamiento que se usará, tal como se muestra en el ejemplo siguiente:

```csharp
[FunctionName("QueueOutput")]
[return: Queue("myqueue-items", Connection = "StorageConnectionAppSetting")]
public static string Run([HttpTrigger] dynamic input,  ILogger log)
{
    ...
}
```

Para obtener un ejemplo completo, vea el [ejemplo de salida](#example).

Puede usar el atributo `StorageAccount` para especificar la cuenta de almacenamiento en el nivel de clase, método o parámetro. Para obtener más información, consulte Desencadenador: atributos.

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

El script de C# no admite atributos.

# <a name="java"></a>[Java](#tab/java)

La anotación `QueueOutput` permite escribir un mensaje como la salida de una función. En el ejemplo siguiente se muestra una función desencadenada mediante HTTP que crea un mensaje de cola.

```java
package com.function;
import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerQueueOutput {
    @FunctionName("HttpTriggerQueueOutput")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "message", queueName = "messages", connection = "MyStorageConnectionAppSetting") OutputBinding<String> message,
            final ExecutionContext context) {

        message.setValue(request.getQueryParameters().get("name"));
        return request.createResponseBuilder(HttpStatus.OK).body("Done").build();
    }
}
```

| Propiedad    | Descripción |
|-------------|-----------------------------|
|`name`       | Declara el nombre del parámetro en la firma de la función. Cuando se desencadena la función, el valor de este parámetro tiene el contenido del mensaje de la cola. |
|`queueName`  | Declara el nombre de la cola en la cuenta de almacenamiento. |
|`connection` | Apunta a la cadena de conexión de la cuenta de almacenamiento. |

El parámetro asociado a la anotación `QueueOutput` tiene como tipo una instancia de [OutputBinding\<T\>](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/OutputBinding.java).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript no admite atributos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell no admite atributos.

# <a name="python"></a>[Python](#tab/python)

Python no admite atributos.

---

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `Queue`.

|Propiedad de function.json | Propiedad de atributo |Descripción|
|---------|---------|----------------------|
|**type** | N/D | Se debe establecer en `queue`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal.|
|**direction** | N/D | Se debe establecer en `out`. Esta propiedad se establece automáticamente cuando se crea el desencadenador en Azure Portal. |
|**name** | N/D | Nombre de la variable que representa la cola en el código de la función. Se establece en `$return` para hacer referencia al valor devuelto de la función.|
|**queueName** |**QueueName** | Nombre de la cola. |
|**connection** | **Connection** |Nombre de una configuración de aplicación o colección de configuraciones que especifica cómo conectarse a las colas de Azure. Consulte [Conexiones](#connections).|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

[!INCLUDE [functions-storage-queue-connections](../../includes/functions-storage-queue-connections.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

### <a name="default"></a>Valor predeterminado

Escriba un mensaje de cola único mediante un parámetro de método como `out T paramName`. Puede usar el tipo de valor devuelto del método en lugar de un parámetro `out`, y `T` puede ser cualquiera de los siguientes tipos:

* Un objeto serializable como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Si intenta enlazar a `CloudQueueMessage` y obtiene un mensaje de error, asegúrese de que tiene una referencia a [la versión correcta del SDK de Storage](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

En C# y script de C#, escriba varios mensajes de cola mediante uno de los siguientes tipos: 

* `ICollector<T>` o `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Tipos adicionales

Las aplicaciones que usan la [versión 5.0.0 o posterior de la extensión de Azure Storage](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) también pueden usar tipos de [Azure SDK para .NET](/dotnet/api/overview/azure/storage.queues-readme). En esta versión se elimina la compatibilidad con los tipos `CloudQueue` y `CloudQueueMessage` heredados en favor de los siguientes tipos:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) para escribir varios mensajes en la cola

Para obtener ejemplos de uso de estos tipos, consulte el [repositorio de GitHub de la extensión](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

### <a name="default"></a>Valor predeterminado

Escriba un mensaje de cola único mediante un parámetro de método como `out T paramName`. `paramName` es el valor especificado en la propiedad `name` de *function.json*. Puede usar el tipo de valor devuelto del método en lugar de un parámetro `out`, y `T` puede ser cualquiera de los siguientes tipos:

* Un objeto serializable como JSON
* `string`
* `byte[]`
* [CloudQueueMessage] 

Si intenta enlazar a `CloudQueueMessage` y obtiene un mensaje de error, asegúrese de que tiene una referencia a [la versión correcta del SDK de Storage](functions-bindings-storage-queue.md#azure-storage-sdk-version-in-functions-1x).

En C# y script de C#, escriba varios mensajes de cola mediante uno de los siguientes tipos: 

* `ICollector<T>` o `IAsyncCollector<T>`
* [CloudQueue](/dotnet/api/microsoft.azure.storage.queue.cloudqueue)

### <a name="additional-types"></a>Tipos adicionales

Las aplicaciones que usan la [versión 5.0.0 o posterior de la extensión de Azure Storage](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) también pueden usar tipos de [Azure SDK para .NET](/dotnet/api/overview/azure/storage.queues-readme). En esta versión se elimina la compatibilidad con los tipos `CloudQueue` y `CloudQueueMessage` heredados en favor de los siguientes tipos:

- [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage)
- [QueueClient](/dotnet/api/azure.storage.queues.queueclient) para escribir varios mensajes en la cola

Para obtener ejemplos de uso de estos tipos, consulte el [repositorio de GitHub de la extensión](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Microsoft.Azure.WebJobs.Extensions.Storage.Queues#examples).

# <a name="java"></a>[Java](#tab/java)

Hay dos opciones para generar un mensaje de cola desde una función mediante la anotación [QueueOutput](/java/api/com.microsoft.azure.functions.annotation.queueoutput):

- **Valor devuelto**: Al aplicar la anotación a la propia función, el valor devuelto de la función se conserva como un mensaje de cola.

- **Imperativa**: Para establecer explícitamente el valor del mensaje, aplique la anotación a un parámetro específico del tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), donde `T` es un POJO o cualquier tipo de Java nativo. Con esta configuración, al pasar un valor al método `setValue` se conserva el valor como un mensaje de cola.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

El elemento de la cola de salida está disponible mediante `context.bindings.<NAME>`, donde `<NAME>` coincide con el nombre definido en *function.json*. Puede usar una cadena o un objeto JSON serializable para la carga del elemento de cola.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

La salida al mensaje de la cola está disponible a través de `Push-OutputBinding`, donde se pasan argumentos que coinciden con el nombre designado por el parámetro `name` del enlace en el archivo *function.json*.

# <a name="python"></a>[Python](#tab/python)

Hay dos opciones para la generación de un mensaje de cola desde una función:

- **Valor devuelto**: Establezca la propiedad `name` de *function.json* en `$return`. Con esta configuración, el valor devuelto de la función se conserva como un mensaje de Queue Storage.

- **Imperativa**: Pase un valor al método [set](/python/api/azure-functions/azure.functions.out#set-val--t-----none) del parámetro declarado como tipo [Out](/python/api/azure-functions/azure.functions.out). El valor pasado a `set` se conserva como un mensaje de Queue Storage.

---

## <a name="exceptions-and-return-codes"></a>Excepciones y códigos de retorno

| Enlace |  Referencia |
|---|---|
| Cola | [Códigos de error de cola](/rest/api/storageservices/queue-service-error-codes) |
| Blob, tabla, cola | [Códigos de error de almacenamiento](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, tabla, cola |  [Solución de problemas](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>Pasos siguientes

- [Ejecución de una función cuando cambian los datos de Queue Storage (desencadenador)](./functions-bindings-storage-queue-trigger.md)

<!-- LINKS -->

[CloudQueueMessage]: /dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage
