---
title: Enlaces de salida de RabbitMQ para Azure Functions
description: Aprenda a enviar mensajes RabbitMQ desde Azure Functions.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: de6c5528bb62cecb130667b2e80a8884c7608a58
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124807092"
---
# <a name="rabbitmq-output-binding-for-azure-functions-overview"></a>Información general sobre los enlaces de salid de RabbitMQ para Azure Functions

> [!NOTE]
> Los enlaces de RabbitMQ solo se admiten por completo en planes **Premium y Dedicado**. El consumo no se admite.

Use el enlace de salida de RabbitMQ para enviar mensajes a una cola de RabbitMQ.

Para obtener información sobre los detalles de instalación y configuración, vea la [información general](functions-bindings-rabbitmq-output.md).

## <a name="example"></a>Ejemplo

# <a name="c"></a>[C#](#tab/csharp)

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que envía un mensaje RabbitMQ cuando lo desencadena un desencadenador TimerTrigger cada 5 minutos usando el valor devuelto del método como salida:

```cs
[FunctionName("RabbitMQOutput")]
[return: RabbitMQ(QueueName = "outputQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

En el ejemplo siguiente se muestra cómo utilizar la interfaz IAsyncCollector para enviar mensajes.

```cs
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] string rabbitMQEvent,
[RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<string> outputEvents,
ILogger log)
{
     // send the message
    await outputEvents.AddAsync(JsonConvert.SerializeObject(rabbitMQEvent));
}
```

En el ejemplo siguiente se muestra cómo enviar los mensajes como POCO.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }
    public static class RabbitMQOutput{
        [FunctionName("RabbitMQOutput")]
        public static async Task Run(
        [RabbitMQTrigger("sourceQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass rabbitMQEvent,
        [RabbitMQ(QueueName = "destinationQueue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")]IAsyncCollector<TestClass> outputPocObj,
        ILogger log)
        {
            // send the message
            await outputPocObj.AddAsync(rabbitMQEvent);
        }
    }
}
```

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

En el ejemplo siguiente se muestra un enlace de salida de RabbitMQ en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función lee el mensaje desde un desencadenador HTTP y lo envía a la cola de RabbitMQ.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Este es el código de script de C#:

```C#
using System;
using Microsoft.Extensions.Logging;

public static void Run(string input, out string outputMessage, ILogger log)
{
    log.LogInformation(input);
    outputMessage = input;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

En el ejemplo siguiente se muestra un enlace de salida de RabbitMQ en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función lee el mensaje desde un desencadenador HTTP y lo envía a la cola de RabbitMQ.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "bindings": [
        {
            "type": "httpTrigger",
            "direction": "in",
            "authLevel": "function",
            "name": "input",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

Este es el código de JavaScript:

```javascript
module.exports = function (context, input) {
    context.bindings.myQueueItem = input.body;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

En el ejemplo siguiente se muestra un enlace de entrada de RabbitMQ en un archivo *function.json* y una función de Python que usa el enlace. La función lee el mensaje desde un desencadenador HTTP y lo envía a la cola de RabbitMQ.

Estos son los datos de enlace del archivo *function.json*:

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
            "type": "rabbitMQ",
            "name": "outputMessage",
            "queueName": "outputQueue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting",
            "direction": "out"
        }
    ]
}
```

En *_\_init_\_.py*:

```python
import azure.functions as func

def main(req: func.HttpRequest, outputMessage: func.Out[str]) -> func.HttpResponse:
    input_msg = req.params.get('message')
    outputMessage.set(input_msg)
    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

La siguiente función de Java usa la anotación `@RabbitMQOutput` de los [tipos de RabbitMQ para Java](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) para describir la configuración de un enlace de salida de la cola de RabbitMQ. La función envía un mensaje a la cola de RabbitMQ cuando se desencadena mediante TimerTrigger cada cinco minutos.

```java
@FunctionName("RabbitMQOutputExample")
public void run(
@TimerTrigger(name = "keepAliveTrigger", schedule = "0 */5 * * * *") String timerInfo,
@RabbitMQOutput(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "hello") OutputBinding<String> output,
final ExecutionContext context) {
    output.setValue("Some string");
}
```

---

## <a name="attributes-and-annotations"></a>Atributos y anotaciones

# <a name="c"></a>[C#](#tab/csharp)

En [Bibliotecas de clase C#](functions-dotnet-class-library.md), use [RabbitMQAttribute](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/RabbitMQAttribute.cs).

A continuación, se muestra un atributo `RabbitMQAttribute` en una signatura de método:

```csharp
[FunctionName("RabbitMQOutput")]
public static async Task Run(
[RabbitMQTrigger("SourceQueue", ConnectionStringSetting = "TriggerConnectionString")] string rabbitMQEvent,
[RabbitMQ("DestinationQueue", ConnectionStringSetting = "OutputConnectionString")]IAsyncCollector<string> outputEvents,
ILogger log)
{
    ...
}
```

Para ver un ejemplo completo, consulte el [ejemplo](#example) de C#.

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

El script de C# no admite atributos.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript no admite atributos.

# <a name="python"></a>[Python](#tab/python)

Python no admite atributos.

# <a name="java"></a>[Java](#tab/java)

La anotación `RabbitMQOutput` permite crear una función que se ejecuta al enviar un mensaje RabbitMQ. Las opciones de configuración disponibles incluyen el nombre de la cola y el nombre de la cadena de conexión. Para obtener más detalles sobre los parámetros, visite [Anotaciones Java RabbitMQOutput](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQOutput.java).

Vea el [ejemplo](#example) de enlace de salida para obtener más detalles.

---

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `RabbitMQ`.

|Propiedad de function.json | Propiedad de atributo |Descripción|
|---------|---------|----------------------|
|**type** | N/D | Debe establecerse en "RabbitMQ".|
|**direction** | N/D | Debe establecerse en "out". |
|**name** | N/D | Nombre de la variable que representa la cola en el código de la función. |
|**queueName**|**QueueName**| Nombre de la cola a la que se van a enviar mensajes. |
|**hostName**|**HostName**|(se omite si se usa ConnectStringSetting) <br>Nombre de host de la cola (por ejemplo: 10.26.45.210)|
|**userName**|**UserName**|(se omite si se usa ConnectionStringSetting) <br>Nombre de la configuración de la aplicación que contiene el nombre de usuario para acceder a la cola. Ejemplo: UserNameSetting: "< UserNameFromSettings >"|
|**password**|**Contraseña**|(se omite si se usa ConnectionStringSetting) <br>Nombre de la configuración de la aplicación que contiene la contraseña para acceder a la cola. Ejemplo: UserNameSetting: "< UserNameFromSettings >"|
|**connectionStringSetting**|**ConnectionStringSetting**|El nombre de la configuración de la aplicación que contiene la cadena de conexión de la cola de mensajes RabbitMQ. Tenga en cuenta que si especifica la cadena de conexión directamente y no a través de una configuración de la aplicación en local.settings.json, el desencadenador no funcionará. (Por ejemplo: En *function.json*: connectionStringSetting: "rabbitMQConnection" <br> En *local.settings.json*: "rabbitMQConnection" : "< ActualConnectionstring >")|
|**port**|**Puerto**|(se omite si se usa ConnectionStringSetting) Obtiene o establece el puerto utilizado. El valor predeterminado es 0, que apunta a la configuración de puerto predeterminado del cliente de rabbitmq: 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

Puede usar los siguientes tipos de parámetro para el enlace de salida:

* `byte[]`: si el valor del parámetro es null cuando finaliza la función, Functions no crea ningún mensaje.
* `string`: si el valor del parámetro es null cuando finaliza la función, Functions no crea ningún mensaje.
* `POCO`: si el valor del parámetro no tiene el formato de un objeto de C#, se recibirá un error. Para ver un ejemplo completo, consulte el [ejemplo](#example) de C#.

Cuando trabaje con funciones de C#:

* Las funciones asincrónicas necesitan un valor devuelto o `IAsyncCollector`, en lugar de un parámetro `out`.

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

Puede usar los siguientes tipos de parámetro para el enlace de salida:

* `byte[]`: si el valor del parámetro es null cuando finaliza la función, Functions no crea ningún mensaje.
* `string`: si el valor del parámetro es null cuando finaliza la función, Functions no crea ningún mensaje.
* `POCO`: si el valor del parámetro no tiene el formato de un objeto de C#, se recibirá un error. Para ver un ejemplo completo, consulte el [ejemplo](#example) de script de C#.

Al trabajar con funciones de script de C#:

* Las funciones asincrónicas necesitan un valor devuelto o `IAsyncCollector`, en lugar de un parámetro `out`.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

El mensaje de la cola está disponible a través de context.bindings.\<NAME\> donde \<NAME\> coincide con el nombre definido en function.json. Si la carga es JSON, el valor se deserializa en un objeto.

# <a name="python"></a>[Python](#tab/python)

Consulte el [ejemplo](#example) de Python.

# <a name="java"></a>[Java](#tab/java)

Puede usar los siguientes tipos de parámetro para el enlace de salida:

* `byte[]`: si el valor del parámetro es null cuando finaliza la función, Functions no crea ningún mensaje.
* `string`: si el valor del parámetro es null cuando finaliza la función, Functions no crea ningún mensaje.
* `POJO`: si el valor del parámetro no tiene el formato de objeto Java, se recibirá un error.

---

## <a name="next-steps"></a>Pasos siguientes

- [Ejecución de una función cuando se crea un mensaje RabbitMQ (desencadenador)](./functions-bindings-rabbitmq-trigger.md)
