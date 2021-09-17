---
title: 'Referencia: desencadenador y enlaces de Azure Web PubSub para Azure Functions'
description: En la referencia se describen los desencadenadores y los enlaces de Azure Web PubSub para Azure Functions.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 68c3ba369af177d2b673dc06d5f7ccafe09c4cf6
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598314"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>Desencadenador y enlaces de Azure Web PubSub para Azure Functions

En esta referencia se explica cómo controlar eventos de Web PubSub en Azure Functions.

Web PubSub es un servicio administrado de Azure que ayuda a los desarrolladores a compilar fácilmente aplicaciones web con características en tiempo real y con el patrón de publicación-suscripción.

| Acción | Tipo |
|---------|---------|
| Ejecutar una función cuando los mensajes proceden del servicio | [Enlace del desencadenador](#trigger-binding) |
| Devolver la dirección URL del punto de conexión de servicio y el token de acceso | [Enlace de entrada](#input-binding)
| Enviar mensajes de Web PubSub |[Enlace de salida](#output-binding) |

[Código fuente](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/) |
[Paquete](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub) |
[Documentación de referencia de API](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Microsoft.Azure.WebJobs.Extensions.WebPubSub/api/Microsoft.Azure.WebJobs.Extensions.WebPubSub.netstandard2.0.cs) |
[Documentación del producto](https://aka.ms/awps/doc) |
[Ejemplos][samples_ref]

## <a name="add-to-your-functions-app"></a>Adición a la aplicación de Functions

Para trabajar con el desencadenador y los enlaces, es necesario hacer referencia al paquete adecuado. En las bibliotecas de clases de .NET se usa el paquete NuGet, mientras que en los demás tipos de aplicaciones se emplea el conjunto de extensiones.

| Idioma                                        | Agregar mediante...                                   | Observaciones 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalación del [paquete NuGet], versión preliminar | |
| Script de C#, JavaScript, Python, PowerShell       | [Instalación explícita de extensiones]                    | Se recomienda usar la [extensión Azure Tools] con Visual Studio Code. |
| Script de C# (solo en línea en Azure Portal)         | Adición de un enlace                                   | Para actualizar extensiones de enlace existentes sin tener que volver a publicar la aplicación de funciones, vea [Actualización de las extensiones]. |

Instale la biblioteca cliente desde [NuGet](https://www.nuget.org/) con el paquete y la versión especificados.

```bash
func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0-beta.3
```

[Paquete NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[Instalación explícita de extensiones]: /azure/azure-functions/functions-bindings-register#explicitly-install-extensions 
[Extensión Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[Actualización de las extensiones]: /azure/azure-functions/functions-bindings-register

## <a name="key-concepts"></a>Conceptos clave

![Diagrama que muestra el flujo de trabajo del servicio Azure Web PubSub que funciona con aplicaciones de funciones.](./media/reference-functions-bindings/functions-workflow.png)

(1)-(2) Enlace de entrada `WebPubSubConnection` con HttpTrigger para generar la conexión de cliente.

(3)-(4) Enlace de desencadenador `WebPubSubTrigger` o enlace de entrada `WebPubSubRequest` con HttpTrigger para controlar la solicitud de servicio.

(5)-(6) Enlace de salida `WebPubSub` para solicitar que el servicio realice alguna acción.

## <a name="trigger-binding"></a>Enlace del desencadenador

Use el desencadenador de función para controlar las solicitudes del servicio Azure Web PubSub. 

`WebPubSubTrigger` se usa cuando necesita controlar las solicitudes del lado del servicio. El patrón de punto de conexión del desencadenador sería similar al siguiente, que debe establecerse en el lado del servicio Web PubSub (portal: configuración -> controlador de eventos -> plantilla de URL). En el patrón de punto de conexión, el elemento de consulta `code=<API_KEY>` es **OBLIGATORIO** cuando se usa la aplicación de funciones de Azure por motivos de [seguridad](/azure/azure-functions/security-concepts#system-key). La clave se puede encontrar en **Azure Portal**. Busque el recurso de la aplicación de funciones y vaya **Functions** -> **Claves de la aplicación** -> **Claves del sistema** -> **webpubsub_extension** después de implementar la aplicación de funciones en Azure. Sin embargo, esta clave no es necesaria para trabajar con funciones locales.

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

### <a name="example"></a>Ejemplo


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message: {message}");
    Console.WriteLine($"Request message DataType: {dataType}");
}
```

El enlace `WebPubSubTrigger` también admite el valor devuelto en algunos escenarios, como, por ejemplo, eventos `Connect`, `Message`, cuando el servidor puede comprobar y denegar la solicitud de cliente o enviar mensajes al cliente de la solicitud directamente. El evento `Connect` respeta `ConnectResponse` y `ErrorResponse`, y el evento `Message` respeta `MessageResponse` y `ErrorResponse`; los tipos rest que no coincidan con el escenario actual se ignorarán. Y si se devuelve `ErrorResponse`, el servicio quitará la conexión de cliente.

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)] 
    ConnectionContext context,
    string message,
    MessageDataType dataType)
{
    return new MessageResponse
    {
        Message = BinaryData.FromString("ack"),
        DataType = MessageDataType.Text
    };
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Defina el enlace de desencadenador en `function.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSubTrigger",
      "direction": "in",
      "name": "message",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

Defina la función en `index.js`.

```js
module.exports = function (context, message) {
  console.log('Request from: ', context.userId);
  console.log('Request message: ', message);
  console.log('Request message dataType: ', context.bindingData.dataType);
}
```

El enlace `WebPubSubTrigger` también admite el valor devuelto en algunos escenarios, como, por ejemplo, eventos `Connect`, `Message`. Cuando el servidor puede comprobar y denegar la solicitud de cliente o enviar un mensaje al cliente de la solicitud directamente. En el lenguaje sin tipos de JavaScript, se deserializará con respecto a las claves de objeto. `ErrorResponse` tendrá la prioridad más alta en comparación con los objetos rest, que si `code` se encuentra en la devolución; a continuación, se analizará en `ErrorResponse` y se descartará la conexión de cliente.

```js
module.exports = async function (context) {
  return { 
    "message": "ack",
    "dataType" : "text"
  };
}
```

---


### <a name="attributes-and-annotations"></a>Atributos y anotaciones

En las [bibliotecas de clases C#](/azure/azure-functions/functions-dotnet-class-library), use el atributo `WebPubSubTrigger`.

A continuación, se muestra un atributo `WebPubSubTrigger` en una signatura de método:

```csharp
[FunctionName("WebPubSubTrigger")]
public static void Run([WebPubSubTrigger("<hub>", "<eventName>", <eventType>)] 
ConnectionContext context, ILogger log)
{
    ...
}
```

Para un ejemplo completo, consulte el ejemplo de C#.

### <a name="configuration"></a>Configuración

En la siguiente tabla se explican las propiedades de configuración de enlace que se establecen en el archivo *function.json*.

| Propiedad de function.json | Propiedad de atributo | Descripción |
|---------|---------|---------|
| **type** | N/D |Requerida: se debe establecer en `webPubSubTrigger`. |
| **direction** | N/D | Requerida: se debe establecer en `in`. |
| **name** | N/D | Requerido: el nombre de la variable que se utiliza en el código de función para el parámetro que recibe los datos del evento. |
| **hub** | Concentrador | Requerido: el valor debe establecerse en el nombre del centro de Web PubSub para la función que se va a desencadenar. Se permite establecer el valor en el atributo como prioridad más alta o se puede establecer en la configuración de la aplicación como un valor global. |
| **eventType** | EventType | Requerido: el valor debe establecerse en el tipo de evento de los mensajes para la función que se va a desencadenar. El valor debe ser `user` o `system`. |
| **eventName** | EventName | Requerido: el valor debe establecerse en el evento de los mensajes para la función que se va a desencadenar. </br> Para el tipo de evento `system`, el nombre del evento debería estar en `connect`, `connected`, `disconnect`. </br> En el caso del subprotocolo compatible con el sistema `json.webpubsub.azure.v1.`, el nombre del evento es el nombre del evento definido por el usuario. </br> Para los subprotocolos definidos por el usuario, el nombre del evento es `message`. |

### <a name="usages"></a>Usos

En C#, `ConnectionContext` es el parámetro de enlace reconocido de tipo, los parámetros rest están enlazados por el nombre del parámetro. Consulte a continuación la tabla de los parámetros y tipos disponibles.

En un lenguaje sin tipo como JavaScript, `name` en `function.json` se usará en para enlazar el objeto desencadenador con respecto a la tabla de asignación siguiente. Asimismo, respetará `dataType` en `function.json` para convertir el mensaje en consecuencia cuando `name` esté establecido en `message` como el objeto de enlace para la entrada del desencadenador. Todos los parámetros se pueden leer desde `context.bindingData.<BindingName>`y se convertirán a `JObject`. 

| Nombre del enlace | Tipo de enlace | Descripción | Propiedades |
|---------|---------|---------|---------|
|connectionContext|`ConnectionContext`|Información de solicitud común| EventType, EventName, Hub, ConnectionId, UserId, Headers, Signature |
|message|`BinaryData`,`string`,`Stream`,`byte[]`| Mensaje de solicitud del cliente | -|
|dataType|`MessageDataType`| Objeto dataType del mensaje de solicitud; admite `binary`, `text` y `json`. | -|
|claims|`IDictionary<string, string[]>`|Notificaciones del usuario en la solicitud `connect` | -|
|Query|`IDictionary<string, string[]>`|Consulta del usuario en la solicitud `connect` | -|
|subprotocols|`string[]`|Subprotocolos disponibles en la solicitud `connect` | -|
|clientCertificates|`ClientCertificate[]`|Lista de huellas digitales de certificado de clientes en la solicitud `connect`|-|
|reason|`string`|Motivo de la solicitud de desconexión|-|

### <a name="return-response"></a> Devolución de respuesta

`WebPubSubTrigger` respetará la respuesta devuelta por el cliente para los eventos sincrónicos de `connect` y el evento de usuario `message`. Solo la respuesta coincidente se devolverá al servicio; de lo contrario, se omitirá. 

| Tipo de valor devuelto | Descripción | Propiedades |
|---------|---------|---------|
|`ConnectResponse`| Respuesta para el evento `connect` | Groups, Roles, UserId, Subprotocol |
|`MessageResponse`| Respuesta para el evento de usuario | DataType, Message |
|`ErrorResponse`| Respuesta de error para el evento de sincronización | Code, ErrorMessage |
|`ServiceResponse`| Tipo de respuesta base de los admitidos que se usan para casos de devolución inciertos. | - |

## <a name="input-binding"></a>Enlace de entrada

Nuestra extensión proporciona dos enlaces de entrada destinados a necesidades diferentes.

- `WebPubSubConnection`

  Para que un cliente pueda conectarse al servicio Azure Web PubSub, debe conocer la dirección URL del punto de conexión del servicio y tener un token de acceso válido. El enlace de entrada `WebPubSubConnection` genera la información necesaria, por lo que el cliente no necesita controlar la generación de tokens. Dado que el token tiene una limitación temporal y se puede usar para autenticar un usuario concreto en una conexión, no lo almacene en la caché ni lo comparta con clientes. Se puede usar un desencadenador HTTP que funcione con este enlace de entrada para que los clientes recuperen la información de conexión.

- `WebPubSubRequest`

  Cuando se usa Static Web Apps, `HttpTrigger` es el único desencadenador admitido y, en el escenario de Web PubSub, proporcionamos el enlace de entrada `WebPubSubRequest`, que ayuda a los usuarios a deserializar la solicitud HTTP ascendente desde el lado del servicio en protocolos de Web PubSub. Por lo tanto, los clientes pueden obtener resultados similares en comparación con `WebPubSubTrigger` para controlar fácilmente las funciones. Vea [ejemplos](#example---webpubsubrequest) a continuación.
  Cuando se usa con `HttpTrigger`, el cliente debe configurar la dirección URL expuesta de HttpTrigger en el nivel superior según corresponda.

### <a name="example---webpubsubconnection"></a>Ejemplo: `WebPubSubConnection`

En el siguiente ejemplo se muestra una función de C# que adquiere la información de la conexión de Web PubSub mediante el enlace de entrada y la devuelve a través de HTTP.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{query.userid}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Defina los enlaces de entrada en `function.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "webPubSubConnection",
      "name": "connection",
      "userId": "{query.userid}",
      "hub": "<hub>",
      "direction": "in"
    }
  ]
}
```

Defina la función en `index.js`.

```js
module.exports = function (context, req, connection) {
  context.res = { body: connection };
  context.done();
};
```

---

### <a name="authenticated-tokens"></a>**Tokens** autenticados

Si la función la desencadena un cliente autenticado, puede agregar una notificación del identificador de usuario al token generado. Puede agregar fácilmente la autenticación a una aplicación de función mediante Autenticación de App Service.

Autenticación de App Service establece encabezados HTTP denominados `x-ms-client-principal-id` y `x-ms-client-principal-name` que contienen el identificador y el nombre de la entidad de seguridad de cliente del usuario autenticado, respectivamente.

Puede establecer la propiedad UserId del enlace en el valor de cualquier encabezado mediante una expresión de enlace: `{headers.x-ms-client-principal-id}` o `{headers.x-ms-client-principal-name}`.

```cs
[FunctionName("WebPubSubConnectionInputBinding")]
public static WebPubSubConnection Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubConnection(Hub = "<hub>", UserId = "{headers.x-ms-client-principal-name}")] WebPubSubConnection connection)
{
    Console.WriteLine("login");
    return connection;
}
```

### <a name="example---webpubsubrequest"></a>Ejemplo: `WebPubSubRequest`

En el siguiente ejemplo se muestra una función de C# que adquiere la información de la solicitud de Web PubSub mediante el enlace de entrada en el tipo de evento de conexión y la devuelve a través de HTTP.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubRequestInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubRequest] WebPubSubRequest wpsReq)
{
    if (wpsReq.Request.IsValidationRequest || !wpsReq.Request.Valid)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectResponse
    {
        UserId = wpsReq.ConnectionContext.UserId
    };
    return response;
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Defina los enlaces de entrada en `function.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "webPubSubRequest",
      "name": "wpsReq",
      "direction": "in"
    }
  ]
}
```

Defina la función en `index.js`.

```js
module.exports = async function (context, req, wpsReq) {
  if (!wpsReq.request.valid || wpsReq.request.isValidationRequest)
  {
    console.log(`invalid request: ${wpsReq.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${context.bindings.wpsReq.connectionContext.userId} is connecting.`);
  return { body: {"userId": context.bindings.wpsReq.connectionContext.userId} };
};
```

---

### <a name="configuration"></a>Configuración

#### <a name="webpubsubconnection"></a>WebPubSubConnection

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo function.json y el atributo `WebPubSubConnection`.

| Propiedad de function.json | Propiedad de atributo | Descripción |
|---------|---------|---------|
| **type** | N/D | Se debe establecer en `webPubSubConnection` |
| **direction** | N/D | Se debe establecer en `in` |
| **name** | N/D | Nombre de variable utilizado en el código de función para el objeto de enlace de la conexión de entrada. |
| **hub** | Concentrador | El valor debe establecerse en el nombre del centro de Web PubSub para la función que se va a desencadenar. Se permite establecer el valor en el atributo como prioridad más alta o se puede establecer en la configuración de la aplicación como un valor global. |
| **userId** | UserId | Opcional: valor de la notificación del identificador de usuario que se debe establecer en el token de la clave de acceso. |
| **connectionStringSetting** | ConnectionStringSetting | Nombre de la configuración de la aplicación que contiene la cadena de conexión del servicio Web PubSub (el valor predeterminado es "AzureSignalRConnectionString"). |

#### <a name="webpubsubrequest"></a>WebPubSubRequest

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo functions.json y el atributo `WebPubSubRequest`.

| Propiedad de function.json | Propiedad de atributo | Descripción |
|---------|---------|---------|
| **type** | N/D | Se debe establecer en `webPubSubRequest` |
| **direction** | N/D | Se debe establecer en `in` |
| **name** | N/D | Nombre de variable usado en el código de función para la solicitud de Web PubSub de entrada. |

### <a name="usage"></a>Uso

#### <a name="webpubsubconnection"></a>WebPubSubConnection

`WebPubSubConnection` proporciona las propiedades siguientes.

Nombre del enlace | Tipo de enlace | Descripción
---------|---------|---------
BaseUrl | string | Dirección URL de conexión de cliente de Web PubSub
Url | string | URI absoluto de la conexión de Web PubSub; contiene la base generada por `AccessToken` en la solicitud.
AccessToken | string | `AccessToken` generado basado en la información de servicio y UserId de la solicitud.

#### <a name="webpubsubrequest"></a>WebPubSubRequest

`WebPubSubRequest` proporciona las propiedades siguientes.

Nombre del enlace | Tipo de enlace | Descripción | Propiedades
---------|---------|---------|---------
connectionContext | `ConnectionContext` | Información de solicitud común| EventType, EventName, Hub, ConnectionId, UserId, Headers, Signature
request | `ServiceRequest` | Solicitud del cliente; consulte la tabla siguiente para obtener más información. | IsValidationRequest, Valid, Unauthorized, BadRequest, ErrorMessage, Name, etc.
respuesta | `HttpResponseMessage` | La extensión genera la respuesta principalmente para `AbuseProtection` y los casos de error. | -

Para `ServiceRequest`, se deserializa en diferentes clases que proporcionan información diferente sobre el escenario de solicitud. Para `ValidationRequest` o `InvalidRequest`, se recomienda devolver la respuesta de compilación del sistema `WebPubSubRequest.Response` directamente, o bien el cliente puede registrar los errores necesarios. En escenarios diferentes, el cliente puede leer las propiedades de la solicitud como se indica a continuación.

Clase derivada | Descripción | Propiedades
--|--|--
`ValidationRequest` | Se usa en `AbuseProtection` cuando `IsValidationRequest` es **true**. | -
`ConnectEventRequest` | Se usa en el tipo de evento `Connect`. | Claims, Query, Subprotocols, ClientCertificates
`ConnectedEventRequest` | Se usa en el tipo de evento `Connected`. | -
`MessageEventRequest` | Se usa en el tipo de evento de usuario. | Message, DataType
`DisconnectedEventRequest` | Se usa en el tipo de evento `Disconnected`. | Motivo
`InvalidRequest` | Se usa cuando la solicitud no es válida. | -

## <a name="output-binding"></a>Enlace de salida

Use el enlace de salida de Web PubSub para enviar uno o varios mensajes mediante el servicio Azure Web PubSub. Puede difundir un mensaje a:

* Todos los clientes conectados
* Clientes conectados autenticados para un usuario específico
* Clientes conectados unidos a un grupo específico

El enlace de salida también permite administrar grupos y conceder o revocar permisos destinados al identificador de conexión con el grupo específico.

Para obtener información sobre los detalles de instalación y configuración, consulte la información general.

### <a name="example"></a>Ejemplo

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubOperation> operations)
{
    await operations.AddAsync(new SendToAll
    {
        Message = BinaryData.FromString("Hello Web PubSub"),
        DataType = MessageDataType.Text
    });
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Defina los enlaces en `functions.json`.

```json
{
  "disabled": false,
  "bindings": [
    {
      "type": "webPubSub",
      "name": "webPubSubOperation",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

Defina la función en `index.js`.

```js
module.exports = async function (context) {
  context.bindings.webPubSubOperation = {
    "operationKind": "sendToAll",
    "message": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsuboperation"></a>WebPubSubOperation 

`WebPubSubOperation` es el tipo abstracto base de los enlaces de salida. Los tipos derivados representan el servidor de operaciones que quiere que se invoquen los servicios. En un lenguaje sin tipo como `javascript`, `OperationKind` es el parámetro clave para resolver el tipo. En un lenguaje de tipo seguro como `csharp`, el usuario podría crear el tipo de operación de destino directamente y se omitiría el valor `OperationKind` asignado por el cliente.

Clase derivada|Propiedades
--|--
`SendToAll`|Message, DataType, Excluded
`SendToGroup`|Group, Message, DataType, Excluded
`SendToUser`|UserId, Message, DataType
`SendToConnection`|ConnectionId, Message, DataType
`AddUserToGroup`|UserId, Group
`RemoveUserFromGroup`|UserId, Group
`RemoveUserFromAllGroups`|UserId
`AddConnectionToGroup`|ConnectionId, Group
`RemoveConnectionFromGroup`|ConnectionId, Group
`CloseClientConnection`|ConnectionId, Reason
`GrantGroupPermission`|ConnectionId, Group, Permission, TargetName
`RevokeGroupPermission`|ConnectionId, Group, Permission, TargetName

### <a name="configuration"></a>Configuración

#### <a name="webpubsub"></a>WebPubSub

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo function.json y el atributo `WebPubSub`.

| Propiedad de function.json | Propiedad de atributo | Descripción |
|---------|---------|---------|
| **type** | N/D | Se debe establecer en `webPubSub` |
| **direction** | N/D | Se debe establecer en `out` |
| **name** | N/D | Nombre de variable utilizado en el código de función para el objeto de enlace de la tabla. |
| **hub** | Concentrador | El valor debe establecerse en el nombre del centro de Web PubSub para la función que se va a desencadenar. Se permite establecer el valor en el atributo como prioridad más alta o se puede establecer en la configuración de la aplicación como un valor global. |
| **connectionStringSetting** | ConnectionStringSetting | Nombre de la configuración de la aplicación que contiene la cadena de conexión del servicio Web PubSub (el valor predeterminado es "AzureSignalRConnectionString"). |

## <a name="troubleshooting"></a>Solución de problemas

### <a name="setting-up-console-logging"></a>Configuración del registro de la consola
También puede [habilitar el registro de la consola](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging) fácilmente si desea profundizar más en las solicitudes que realiza en el servicio.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
