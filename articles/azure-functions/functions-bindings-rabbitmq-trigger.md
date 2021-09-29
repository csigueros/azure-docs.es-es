---
title: Desencadenador de RabbitMQ para Azure Functions
description: Aprenda a ejecutar una instancia de Azure Functions al crear un mensaje de RabbitMQ.
author: cachai2
ms.assetid: ''
ms.topic: reference
ms.date: 12/17/2020
ms.author: cachai
ms.custom: ''
ms.openlocfilehash: f0cac775870345ae298672c2af5dad8277c592e6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639793"
---
# <a name="rabbitmq-trigger-for-azure-functions-overview"></a>Introducción al desencadenador de RabbitMQ para Azure Functions

> [!NOTE]
> Los enlaces de RabbitMQ solo se admiten por completo en planes **Premium y Dedicado**. El consumo no se admite.

Use el desencadenador de RabbitMQ para responder a los mensajes de una cola de RabbitMQ.

Para obtener información sobre los detalles de instalación y configuración, vea la [información general](functions-bindings-rabbitmq.md).

## <a name="example"></a>Ejemplo

# <a name="c"></a>[C#](#tab/csharp)

En el ejemplo siguiente se muestra una [función de C#](functions-dotnet-class-library.md) que lee y registra el mensaje de RabbitMQ como un [evento de RabbitMQ](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html):

```cs
[FunctionName("RabbitMQTriggerCSharp")]
public static void RabbitMQTrigger_BasicDeliverEventArgs(
    [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] BasicDeliverEventArgs args,
    ILogger logger
    )
{
    logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {Encoding.UTF8.GetString(args.Body)}");
}
```

En el ejemplo siguiente se muestra cómo leer el mensaje como POCO.

```cs
namespace Company.Function
{
    public class TestClass
    {
        public string x { get; set; }
    }

    public class RabbitMQTriggerCSharp{
        [FunctionName("RabbitMQTriggerCSharp")]
        public static void RabbitMQTrigger_BasicDeliverEventArgs(
            [RabbitMQTrigger("queue", ConnectionStringSetting = "rabbitMQConnectionAppSetting")] TestClass pocObj,
            ILogger logger
            )
        {
            logger.LogInformation($"C# RabbitMQ queue trigger function processed message: {pocObj}");
        }
    }
}
```

Al igual que con los objetos JSON, se producirá un error si el mensaje no tiene el formato correcto como objeto de C#. Si lo tiene, se enlaza a la variable pocObj, la cual se puede usar para lo que sea necesario.

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

En el ejemplo siguiente se muestra un enlace de desencadenador de RabbitMQ en un archivo *function.json* y una [función de script de C#](functions-reference-csharp.md) que usa el enlace. La función Lee y registra el mensaje de RabbitMQ.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

Este es el código de script de C#:

```C#
using System;

public static void Run(string myQueueItem, ILogger log)
{
    log.LogInformation($"C# Script RabbitMQ trigger function processed: {myQueueItem}");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

En el ejemplo siguiente se muestra un enlace de desencadenador de RabbitMQ en un archivo *function.json* y una [función de JavaScript](functions-reference-node.md) que usa el enlace. La función lee y registra un mensaje de RabbitMQ.

Estos son los datos de enlace del archivo *function.json*:

```json
{
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

Este es el código del script de JavaScript:

```javascript
module.exports = async function (context, myQueueItem) {
    context.log('JavaScript RabbitMQ trigger function processed work item', myQueueItem);
};
```

# <a name="python"></a>[Python](#tab/python)

En el ejemplo siguiente se muestra cómo leer un mensaje de cola de RabbitMQ a través de un desencadenador.

Un enlace de RabbitMQ se define en *function.json*, donde *type* se establece en `RabbitMQTrigger`.

```json
{
    "scriptFile": "__init__.py",
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "rabbitMQTrigger",
            "direction": "in",
            "queueName": "queue",
            "connectionStringSetting": "rabbitMQConnectionAppSetting"
        }
    ]
}
```

```python
import logging
import azure.functions as func

def main(myQueueItem) -> None:
    logging.info('Python RabbitMQ trigger function processed a queue item: %s', myQueueItem)
```

# <a name="java"></a>[Java](#tab/java)

La siguiente función de Java usa la anotación `@RabbitMQTrigger` de los [tipos de RabbitMQ para Java](https://mvnrepository.com/artifact/com.microsoft.azure.functions/azure-functions-java-library-rabbitmq) para describir la configuración de un desencadenador de cola RabbitMQ. La función toma el mensaje colocado en la cola y lo agrega a los registros.

```java
@FunctionName("RabbitMQTriggerExample")
public void run(
    @RabbitMQTrigger(connectionStringSetting = "rabbitMQConnectionAppSetting", queueName = "queue") String input,
    final ExecutionContext context)
{
    context.getLogger().info("Java HTTP trigger processed a request." + input);
}
```

---

## <a name="attributes-and-annotations"></a>Atributos y anotaciones

# <a name="c"></a>[C#](#tab/csharp)

En las [bibliotecas de clases de C#](functions-dotnet-class-library.md), use el atributo [RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/src/Trigger/RabbitMQTriggerAttribute.cs).

A continuación, se muestra un atributo `RabbitMQTrigger` en una signatura de método:

```csharp
[FunctionName("RabbitMQTest")]
public static void RabbitMQTest([RabbitMQTrigger("queue")] string message, ILogger log)
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

La anotación `RabbitMQTrigger` permite crear una función que se ejecuta al crear un mensaje de RabbitMQ. Las opciones de configuración disponibles incluyen el nombre de la cola y el nombre de la cadena de conexión. Para obtener más detalles sobre los parámetros, visite [Anotaciones Java RabbitMQTrigger](https://github.com/Azure/azure-functions-rabbitmq-extension/blob/dev/binding-library/java/src/main/java/com/microsoft/azure/functions/rabbitmq/annotation/RabbitMQTrigger.java).

Consulte el [ejemplo](#example) de desencadenador para más detalles.

---

## <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo *function.json* y el atributo `RabbitMQTrigger`.

|Propiedad de function.json | Propiedad de atributo |Descripción|
|---------|---------|----------------------|
|**type** | N/D | Debe establecerse en "RabbitMQTrigger".|
|**direction** | N/D | Debe establecerse en "in".|
|**name** | N/D | Nombre de la variable que representa la cola en el código de la función. |
|**queueName**|**QueueName**| Nombre de la cola desde la que se reciben los mensajes. |
|**hostName**|**HostName**|(se omite si se usa ConnectStringSetting) <br>Nombre de host de la cola (por ejemplo: 10.26.45.210)|
|**userNameSetting**|**UserNameSetting**|(se omite si se usa ConnectionStringSetting) <br>Nombre de la configuración de la aplicación que contiene el nombre de usuario para acceder a la cola. Ejemplo: UserNameSetting: "%< UserNameFromSettings >%"|
|**passwordSetting**|**PasswordSetting**|(se omite si se usa ConnectionStringSetting) <br>Nombre de la configuración de la aplicación que contiene la contraseña para acceder a la cola. Ejemplo: PasswordSetting: "%< PasswordFromSettings >%"|
|**connectionStringSetting**|**ConnectionStringSetting**|El nombre de la configuración de la aplicación que contiene la cadena de conexión de la cola de mensajes RabbitMQ. Tenga en cuenta que si especifica la cadena de conexión directamente y no a través de una configuración de la aplicación en local.settings.json, el desencadenador no funcionará. (Por ejemplo: En *function.json*: connectionStringSetting: "rabbitMQConnection" <br> En *local.settings.json*: "rabbitMQConnection" : "< ActualConnectionstring >")|
|**port**|**Puerto**|(se omite si se usa ConnectionStringSetting) Obtiene o establece el puerto utilizado. El valor predeterminado es 0, que apunta a la configuración de puerto predeterminado del cliente de rabbitmq: 5672.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>Uso

# <a name="c"></a>[C#](#tab/csharp)

El tipo de mensaje predeterminado es [evento RabbitMQ](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)y la propiedad `Body` del evento RabbitMQ se puede leer como los tipos que se enumeran a continuación:

* `An object serializable as JSON`: el mensaje se entrega como una cadena JSON válida.
* `string`
* `byte[]`
* `POCO`: el mensaje tiene el formato de un objeto de C#. Para ver un ejemplo completo, consulte el [ejemplo](#example) de C#.

# <a name="c-script"></a>[Script de C#](#tab/csharp-script)

El tipo de mensaje predeterminado es [evento RabbitMQ](https://rabbitmq.github.io/rabbitmq-dotnet-client/api/RabbitMQ.Client.Events.BasicDeliverEventArgs.html)y la propiedad `Body` del evento RabbitMQ se puede leer como los tipos que se enumeran a continuación:

* `An object serializable as JSON`: el mensaje se entrega como una cadena JSON válida.
* `string`
* `byte[]`
* `POCO`: el mensaje tiene el formato de un objeto de C#. Para ver un ejemplo completo, consulte el [ejemplo](#example) de script de C#.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

El mensaje de la cola está disponible a través de context.bindings.\<NAME\> donde \<NAME\> coincide con el nombre definido en function.json. Si la carga es JSON, el valor se deserializa en un objeto.

# <a name="python"></a>[Python](#tab/python)

Consulte el [ejemplo](#example) de Python.

# <a name="java"></a>[Java](#tab/java)

Consulte los [atributos y anotaciones](#attributes-and-annotations) de Java.

---

## <a name="dead-letter-queues"></a>Colas de mensajes fallidos
Las colas de mensajes fallidos y los intercambios no se pueden controlar ni configurar desde el desencadenador de RabbitMQ.  Para usar colas de mensajes fallidos, configure previamente la cola utilizada por el desencadenador en RabbitMQ. Consulte la [documentación de RabbitMQ](https://www.rabbitmq.com/dlx.html).

## <a name="hostjson-settings"></a>configuración de host.json

En esta sección se describen las opciones de configuración globales disponibles para este enlace en las versiones 2.x y posteriores. El siguiente archivo *host.json* de ejemplo contiene solo la configuración para este enlace. Para más información acerca de las opciones de configuración globales, consulte [Referencia de host.json para Azure Functions](functions-host-json.md).

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            "prefetchCount": 100,
            "queueName": "queue",
            "connectionString": "amqp://user:password@url:port",
            "port": 10
        }
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------|
|prefetchCount|30|Obtiene o establece el número de mensajes que el destinatario del mensaje puede solicitar simultáneamente y lo almacena en caché.|
|queueName|N/D| Nombre de la cola desde la que se reciben los mensajes.|
|connectionString|N/D|La cadena de conexión de la cola de mensajes de RabbitMQ. Tenga en cuenta que la cadena de conexión se especifica directamente aquí y no a través de una configuración de aplicación.|
|port|0|(se omite si se usa connectionString) Obtiene o establece el puerto utilizado. El valor predeterminado es 0, que apunta a la configuración de puerto predeterminado del cliente de rabbitmq: 5672.|

## <a name="local-testing"></a>Pruebas locales

> [!NOTE]
> connectionString tiene prioridad sobre "hostName", "userName" y "password". Si todos están establecidos, connectionString invalidará a los otros dos.

Si está realizando una prueba de forma local sin una cadena de conexión, debería configurar los valores "hostName", "userName" y "password" si es aplicable en la sección "rabbitMQ" de *host.json*:

```json
{
    "version": "2.0",
    "extensions": {
        "rabbitMQ": {
            ...
            "hostName": "localhost",
            "username": "userNameSetting",
            "password": "passwordSetting"
        }
    }
}
```

|Propiedad  |Valor predeterminado | Descripción |
|---------|---------|---------|
|hostName|N/D|(se omite si se usa connectionString) <br>Nombre de host de la cola (por ejemplo: 10.26.45.210)|
|userName|N/D|(se omite si se usa connectionString) <br>Nombre para acceder a la cola |
|password|N/D|(se omite si se usa connectionString) <br>Contraseña para acceder la cola|


## <a name="enable-runtime-scaling"></a>Habilitación del escalado de tiempo de ejecución

Para que el desencadenador de RabbitMQ se escale horizontalmente a varias instancias, debe estar habilitada la configuración **Runtime Scale Monitoring** (Supervisión de la escala de tiempo de ejecución). 

En el portal, esta configuración se puede encontrar en **Configuración**  > **Configuración del tiempo de ejecución de la función** para la aplicación de funciones.

:::image type="content" source="media/functions-networking-options/virtual-network-trigger-toggle.png" alt-text="VNETToggle":::

En la CLI, puede habilitar **Runtime Scale Monitoring** (Supervisión de la escala de tiempo de ejecución) mediante el comando siguiente:

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.functionsRuntimeScaleMonitoringEnabled=1 --resource-type Microsoft.Web/sites
```

## <a name="monitoring-rabbitmq-endpoint"></a>Supervisión del punto de conexión de RabbitMQ
Para supervisar las colas e intercambios de un determinado punto de conexión de RabbitMQ:

* Habilite el [complemento de administración de RabbitMQ](https://www.rabbitmq.com/management.html)
* Vaya a http://{node-hostname}:15672 e inicie sesión con su nombre de usuario y contraseña.

## <a name="next-steps"></a>Pasos siguientes

- [Envío de mensajes de RabbitMQ desde Azure Functions (enlace de salida)](./functions-bindings-rabbitmq-output.md)
