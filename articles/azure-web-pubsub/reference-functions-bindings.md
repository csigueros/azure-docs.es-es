---
title: 'Referencia: desencadenador y enlaces de Azure Web PubSub para Azure Functions'
description: En la referencia se describen los desencadenadores y los enlaces de Azure Web PubSub para Azure Functions.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 40904b087d58ee6a07ca7acecdd2b2927348799e
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493255"
---
#  <a name="azure-web-pubsub-trigger-and-bindings-for-azure-functions"></a>Desencadenador y enlaces de Azure Web PubSub para Azure Functions

En esta referencia se explica cómo controlar eventos de Web PubSub en Azure Functions.

Web PubSub es un servicio administrado de Azure que ayuda a los desarrolladores a compilar fácilmente aplicaciones web con características en tiempo real y con el patrón de publicación-suscripción.

| Acción | Tipo |
|---------|---------|
| Ejecutar una función cuando los mensajes proceden del servicio | [Enlace del desencadenador](#trigger-binding) |
| Enlace de la solicitud al objeto de destino en el desencadenador HTTP para la negociación y las solicitudes ascendentes | [Enlace de entrada](#input-binding)
| Invocación de acciones Do del servicio | [Enlace de salida](#output-binding) |

[Código fuente](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/webpubsub/) |
[Paquete](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub) |
[Documentación de referencia de API](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/webpubsub/Microsoft.Azure.WebJobs.Extensions.WebPubSub/api/Microsoft.Azure.WebJobs.Extensions.WebPubSub.netstandard2.0.cs) |
[Documentación del producto](./index.yml) |
[Ejemplos][samples_ref]

## <a name="add-to-your-functions-app"></a>Adición a la aplicación de Functions

Para trabajar con el desencadenador y los enlaces, es necesario hacer referencia al paquete adecuado. En las bibliotecas de clases de .NET se usa el paquete NuGet, mientras que en los demás tipos de aplicaciones se emplea el conjunto de extensiones.

| Idioma                                        | Agregar mediante...                                   | Observaciones 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Instalación del [paquete NuGet], versión preliminar | |
| Script de C#, JavaScript, Python, PowerShell       | [Instalación explícita de extensiones], [Uso de conjuntos de extensiones] | Se recomienda usar la [extensión Azure Tools] con Visual Studio Code. |
| Script de C# (solo en línea en Azure Portal)         | Adición de un enlace                                   | Para actualizar extensiones de enlace existentes sin tener que volver a publicar la aplicación de funciones, vea [Actualización de las extensiones]. |

> [!NOTE]
> Instale la biblioteca cliente desde [NuGet](https://www.nuget.org/) con el paquete y la versión especificados.
> 
> ```bash
> func extensions install --package Microsoft.Azure.WebJobs.Extensions.WebPubSub --version 1.0.0
> ```

[Paquete NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.WebPubSub
[Uso de conjuntos de extensiones]: ../azure-functions/functions-bindings-register.md#extension-bundles
[Instalación explícita de extensiones]: ../azure-functions/functions-bindings-register.md#explicitly-install-extensions 
[Extensión Azure Tools]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
[Actualización de las extensiones]: ../azure-functions/functions-bindings-register.md

## <a name="key-concepts"></a>Conceptos clave

![Diagrama que muestra el flujo de trabajo del servicio Azure Web PubSub que funciona con aplicaciones de funciones.](./media/reference-functions-bindings/functions-workflow.png)

(1)-(2) Enlace de entrada `WebPubSubConnection` con HttpTrigger para generar la conexión de cliente.

(3)-(4) Enlace de desencadenador `WebPubSubTrigger` o enlace de entrada `WebPubSubContext` con HttpTrigger para controlar la solicitud de servicio.

(5)-(6) Enlace de salida `WebPubSub` para solicitar que el servicio realice alguna acción.

## <a name="trigger-binding"></a>Enlace del desencadenador

Use el desencadenador de función para controlar las solicitudes del servicio Azure Web PubSub. 

`WebPubSubTrigger` se usa cuando necesita controlar las solicitudes del lado del servicio. El patrón de punto de conexión del desencadenador sería similar al siguiente, que debe establecerse en el lado del servicio Web PubSub (portal: configuración -> controlador de eventos -> plantilla de URL). En el patrón de punto de conexión, el elemento de consulta `code=<API_KEY>` es **OBLIGATORIO** cuando se usa la aplicación de funciones de Azure por motivos de [seguridad](../azure-functions/security-concepts.md#system-key). La clave se puede encontrar en **Azure Portal**. Busque el recurso de la aplicación de funciones y vaya **Functions** -> **Claves de la aplicación** -> **Claves del sistema** -> **webpubsub_extension** después de implementar la aplicación de funciones en Azure. Sin embargo, esta clave no es necesaria para trabajar con funciones locales.

```
<Function_App_Url>/runtime/webhooks/webpubsub?code=<API_KEY>
```

:::image type="content" source="media/quickstart-serverless/func-keys.png" alt-text="Captura de pantalla de Obtener las claves del sistema de la función.":::

### <a name="example"></a>Ejemplo


# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubTrigger")]
public static void Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)]
    UserEventRequest request,
    WebPubSubConnectionContext context,
    string data,
    WebPubSubDataType dataType)
{
    Console.WriteLine($"Request from: {context.userId}");
    Console.WriteLine($"Request message data: {data}");
    Console.WriteLine($"Request message dataType: {dataType}");
}
```

El enlace `WebPubSubTrigger` también admite el valor devuelto en escenarios de sincronización, como, por ejemplo, `Connect`del sistema y los eventos de usuario, cuando el servidor puede comprobar y denegar la solicitud de cliente o enviar mensajes directamente al autor de la llamada. El evento `Connect` respeta `ConnectEventResponse` y `EventErrorResponse`, y el evento de usuario respeta `UserEventResponse` y `EventErrorResponse`; los tipos rest que no coincidan con el escenario actual se ignorarán. Y si se devuelve `EventErrorResponse`, el servicio quitará la conexión de cliente.

```cs
[FunctionName("WebPubSubTriggerReturnValue")]
public static MessageResponse Run(
    [WebPubSubTrigger("<hub>", "message", EventType.User)]
    UserEventRequest request,
    ConnectionContext context,
    string data,
    WebPubSubDataType dataType)
{
    return new UserEventResponse
    {
        Data = BinaryData.FromString("ack"),
        DataType = WebPubSubDataType.Text
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
      "name": "data",
      "hub": "<hub>",
      "eventName": "message",
      "eventType": "user"
    }
  ]
}
```

Defina la función en `index.js`.

```js
module.exports = function (context, data) {
  console.log('Request from: ', context.bindingData.request.connectionContext.userId);
  console.log('Request message data: ', data);
  console.log('Request message dataType: ', context.bindingData.request.dataType);
}
```

El enlace `WebPubSubTrigger` también admite el valor devuelto en escenarios de sincronización, como, por ejemplo, `Connect`del sistema y los eventos de usuario, cuando el servidor puede comprobar y denegar la solicitud de cliente o enviar mensajes directamente al cliente de la solicitud. En el lenguaje débilmente tipado de JavaScript, se deserializará en relación con las claves de objeto. `EventErrorResponse` tendrá la prioridad más alta en comparación con los objetos rest, que si `code` se encuentra en la devolución; a continuación, se analizará en `EventErrorResponse` y se descartará la conexión de cliente.

```js
module.exports = async function (context) {
  return { 
    "data": "ack",
    "dataType" : "text"
  };
}
```

---


### <a name="attributes-and-annotations"></a>Atributos y anotaciones

En las [bibliotecas de clases C#](../azure-functions/functions-dotnet-class-library.md), use el atributo `WebPubSubTrigger`.

A continuación, se muestra un atributo `WebPubSubTrigger` en una signatura de método:

```csharp
[FunctionName("WebPubSubTrigger")]
public static void Run([WebPubSubTrigger("<hub>", "<event-Name>", <WebPubSubEventType>)] 
WebPubSubConnectionContext context, ILogger log)
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
| **eventName** | EventName | Requerido: el valor debe establecerse en el evento de los mensajes para la función que se va a desencadenar. </br> Para el tipo de evento `system`, el nombre del evento debería estar en `connect`, `connected`, `disconnected`. </br> Para los subprotocolos definidos por el usuario, el nombre del evento es `message`. </br> En el caso del subprotocolo compatible con el sistema `json.webpubsub.azure.v1.`, el nombre del evento es el nombre del evento definido por el usuario. |
| **connection** | Conexión | Opcional: el nombre de una configuración de aplicación o una colección de configuraciones que especifica el servicio Azure Web PubSub ascendente. El valor se usará para la validación de firmas. Y el valor se resolverá automáticamente con la configuración de la aplicación "WebPubSubConnectionString" de forma predeterminada. Y `null` significa que la validación no es necesaria y siempre se realizará correctamente. |

### <a name="usages"></a>Usos

En C#, `WebPubSubEventRequest` es el parámetro de enlace reconocido de tipo, los parámetros rest están enlazados por el nombre del parámetro. Consulte a continuación la tabla de los parámetros y tipos disponibles.

En un lenguaje débilmente tipado como JavaScript, `name` en `function.json` se usará en para enlazar el objeto desencadenador con respecto a la tabla de asignación siguiente. Asimismo, respetará `dataType` en `function.json` para convertir el mensaje en consecuencia cuando `name` esté establecido en `data` como el objeto de enlace para la entrada del desencadenador. Todos los parámetros se pueden leer desde `context.bindingData.<BindingName>`y se convertirán a `JObject`. 

| Nombre del enlace | Tipo de enlace | Descripción | Propiedades |
|---------|---------|---------|---------|
|request|`WebPubSubEventRequest`|Describe la solicitud ascendente|La propiedad difiere según distintos tipos de eventos, incluidas las clases derivadas `ConnectEventRequest`, `ConnectedEventRequest`, `UserEventRequest` y `DisconnectedEventRequest`. |
|connectionContext|`WebPubSubConnectionContext`|Información de solicitud común| EventType, EventName, Hub, ConnectionId, UserId, Headers, Origin, Signature, States |
|datos|`BinaryData`,`string`,`Stream`,`byte[]`| Solicitud de datos de mensaje del cliente en el evento `message` de usuario | -|
|dataType|`WebPubSubDataType`| Objeto dataType del mensaje de solicitud; admite `binary`, `text` y `json`. | -|
|claims|`IDictionary<string, string[]>`|Notificaciones del usuario en la solicitud `connect` del sistema | -|
|Query|`IDictionary<string, string[]>`|Consulta del usuario en la solicitud `connect` del sistema | -|
|subprotocols|`IList<string>`|Subprotocolos disponibles en la solicitud `connect` del sistema | -|
|clientCertificates|`IList<ClientCertificate>`|Lista de huellas digitales de certificado de clientes en la solicitud `connect` del sistema|-|
|reason|`string`|Motivo de la solicitud `disconnected` del sistema|-|

### <a name="return-response"></a> Devolución de respuesta

`WebPubSubTrigger` respetará la respuesta devuelta por el cliente para los eventos sincrónicos de `connect` y el evento de usuario. Solo la respuesta coincidente se devolverá al servicio; de lo contrario, se omitirá. Además, el objeto devuelto `WebPubSubTrigger` admite a los usuarios que realicen `SetState()` y `ClearStates()` para administrar los metadatos de la conexión. Y la extensión combinará los resultados del valor devuelto con los originales de la solicitud `WebPubSubConnectionContext.States`. El valor de la clave existente se sobrescribirá y se agregará el valor de la nueva clave.

| Tipo de valor devuelto | Descripción | Propiedades |
|---------|---------|---------|
|`ConnectEventResponse`| Respuesta para el evento `connect` | Groups, Roles, UserId, Subprotocol |
|`UserEventResponse`| Respuesta para el evento de usuario | DataType, Data |
|`EventErrorResponse`| Respuesta de error para el evento de sincronización | Code, ErrorMessage |
|`*WebPubSubEventResponse`| Tipo de respuesta base de los admitidos que se usan para casos de devolución inciertos. | - |

## <a name="input-binding"></a>Enlace de entrada

Nuestra extensión proporciona dos enlaces de entrada destinados a necesidades diferentes.

- `WebPubSubConnection`

  Para que un cliente pueda conectarse al servicio Azure Web PubSub, debe conocer la dirección URL del punto de conexión del servicio y tener un token de acceso válido. El enlace de entrada `WebPubSubConnection` genera la información necesaria, por lo que el cliente no necesita controlar la generación de tokens. Dado que el token tiene una limitación temporal y se puede usar para autenticar un usuario concreto en una conexión, no lo almacene en la caché ni lo comparta con clientes. Se puede usar un desencadenador HTTP que funcione con este enlace de entrada para que los clientes recuperen la información de conexión.

- `WebPubSubContext`

  Cuando se usa Static Web Apps, `HttpTrigger` es el único desencadenador admitido y, en el escenario de Web PubSub, proporcionamos el enlace de entrada `WebPubSubContext`, que ayuda a los usuarios a deserializar la solicitud HTTP ascendente desde el lado del servicio en protocolos de Web PubSub. Por lo tanto, los clientes pueden obtener resultados similares en comparación con `WebPubSubTrigger` para controlar fácilmente las funciones. Vea [ejemplos](#example---webpubsubcontext) a continuación.
  Cuando se usa con `HttpTrigger`, el cliente debe configurar la dirección URL expuesta de HttpTrigger en el controlador de eventos según corresponda.

### <a name="example---webpubsubconnection"></a>Ejemplo: `WebPubSubConnection`

En el siguiente ejemplo se muestra una función de C# que adquiere la información de la conexión de Web PubSub mediante el enlace de entrada y la devuelve a través de HTTP. En el ejemplo siguiente, se pasa `UserId` a través del elemento de consulta de la solicitud de cliente como `?userid={User-A}`.

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

### <a name="example---webpubsubcontext"></a>Ejemplo: `WebPubSubContext`

En el siguiente ejemplo se muestra una función de C# que adquiere la información de la solicitud de nivel superior de Web PubSub mediante el enlace de entrada en el tipo de evento `connect` y la devuelve a través de HTTP.

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubContextInputBinding")]
public static object Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSubContext] WebPubSubContext wpsContext)
{
    if (wpsContext.IsPreflight || !wpsContext.HasError)
    {
        return wpsReq.Response;
    }
    var request = wpsReq.Request as ConnectEventRequest;
    var response = new ConnectEventResponse
    {
        UserId = wpsContext.Request.ConnectionContext.UserId
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
      "type": "webPubSubContext",
      "name": "wpsContext",
      "direction": "in"
    }
  ]
}
```

Defina la función en `index.js`.

```js
module.exports = async function (context, req, wpsContext) {
  if (!wpsContext.hasError || wpsContext.isPreflight)
  {
    console.log(`invalid request: ${wpsContext.response.message}.`);
    return wpsReq.response;
  }
  console.log(`user: ${wpsContext.connectionContext.userId} is connecting.`);
  return { body: {"userId": wpsContext.connectionContext.userId} };
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
| **hub** | Concentrador | Requerido: el valor debe establecerse en el nombre del centro de Web PubSub para la función que se va a desencadenar. Se permite establecer el valor en el atributo como prioridad más alta o se puede establecer en la configuración de la aplicación como un valor global. |
| **userId** | UserId | Opcional: valor de la notificación del identificador de usuario que se debe establecer en el token de la clave de acceso. |
| **connection** | Conexión | Obligatorio: el nombre de la configuración de la aplicación que contiene la cadena de conexión del servicio Web PubSub (el valor predeterminado es "AzureSignalRConnectionString"). |

#### <a name="webpubsubcontext"></a>WebPubSubContext

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo functions.json y el atributo `WebPubSubContext`.

| Propiedad de function.json | Propiedad de atributo | Descripción |
|---------|---------|---------|
| **type** | N/D | Se debe establecer en `webPubSubContext`. |
| **direction** | N/D | Se debe establecer en `in`. |
| **name** | N/D | Nombre de variable usado en el código de función para la solicitud de Web PubSub de entrada. |
| **connection** | Conexión | Opcional: el nombre de una configuración de aplicación o una colección de configuraciones que especifica el servicio Azure Web PubSub ascendente. El valor se usará para la [protección contra abusos](https://github.com/cloudevents/spec/blob/v1.0.1/http-webhook.md#4-abuse-protection) y la validación de firmas. Y el valor se resolverá automáticamente con "WebPubSubConnectionString" de forma predeterminada. Y `null` significa que la validación no es necesaria y siempre se realizará correctamente. |

### <a name="usage"></a>Uso

#### <a name="webpubsubconnection"></a>WebPubSubConnection

# <a name="c"></a>[C#](#tab/csharp)

`WebPubSubConnection` proporciona las propiedades siguientes.

| Nombre del enlace | Tipo de enlace | Descripción |
|---------|---------|---------|
| BaseUri | Identificador URI | Identificador URI de conexión de cliente de Web PubSub. |
| Identificador URI | Identificador URI | Identificador URI absoluto de la conexión de Web PubSub; contiene la base generada por `AccessToken` en la solicitud. |
| AccessToken | string | `AccessToken` generado basado en la información de servicio y UserId de la solicitud. |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`WebPubSubConnection` proporciona las propiedades siguientes.

| Nombre del enlace | Description |
|---------|---------|
| baseUrl | Identificador URI de conexión de cliente de Web PubSub. |
| url | Identificador URI absoluto de la conexión de Web PubSub; contiene la base generada por `AccessToken` en la solicitud. |
| accessToken | `AccessToken` generado basado en la información de servicio y UserId de la solicitud. |

---

#### <a name="webpubsubcontext"></a>WebPubSubContext

`WebPubSubContext` proporciona las propiedades siguientes.

| Nombre del enlace | Tipo de enlace | Descripción | Propiedades |
|---------|---------|---------|---------|
| request | `WebPubSubEventRequest` | Solicitud del cliente; consulte la tabla siguiente para obtener más información. | `WebPubSubConnectionContext` desde el encabezado de solicitud y otras propiedades deserializadas del cuerpo de la solicitud describen a esta, por ejemplo, `Reason` para `DisconnectedEventRequest`. |
| respuesta | `HttpResponseMessage` | La extensión genera la respuesta principalmente para `AbuseProtection` y los casos de error. | - |
| errorMessage | string | Describa los detalles del error al procesar la solicitud ascendente. | - |
| hasError | bool | Marca para indicar si se trata de una solicitud ascendente de Web PubSub válida. | - |
| isPreflight | bool | Marca para indicar si se trata de una solicitud preparatoria de `AbuseProtection`. | - |

Para `WebPubSubEventRequest`, se deserializa en diferentes clases que proporcionan información diferente sobre el escenario de solicitud. Para `PreflightRequest` o para casos no válidos, el usuario puede comprobar las marcas `IsPreflight` y `HasError` y averiguarlo. Se recomienda devolver la respuesta `WebPubSubContext.Response` de compilación del sistema directamente, o bien el cliente puede registrar los errores a petición. En escenarios diferentes, el cliente puede leer las propiedades de la solicitud como se indica a continuación.

| Clase derivada | Descripción | Propiedades |
| -- | -- | -- |
| `PreflightRequest` | Se usa en `AbuseProtection` cuando `IsPreflight` es **true**. | - |
| `ConnectEventRequest` | Se usa en el tipo de evento `Connect` del sistema. | Claims, Query, Subprotocols, ClientCertificates |
| `ConnectedEventRequest` | Se usa en el tipo de evento `Connected` del sistema. | - |
| `UserEventRequest` | Se usa en el tipo de evento de usuario. | Data, DataType |
| `DisconnectedEventRequest` | Se usa en el tipo de evento `Disconnected` del sistema. | Motivo |

> [!NOTE]
> Aunque `WebPubSubContext` es un enlace de entrada que proporciona una manera similar de deserialización de solicitudes en `HttpTrigger` en comparación con `WebPubSubTrigger`, hay limitaciones como, por ejemplo, que no se admite un estado de conexión posterior a la combinación. El lado del servicio seguirá respetando la respuesta de devolución, pero los usuarios deberán crear la respuesta ellos mismos. Si los usuarios tienen que establecer la respuesta del evento, debe devolver una respuesta `HttpResponseMessage` que contenga `ConnectEventResponse` o mensajes del evento del usuario como **cuerpo de la respuesta** y poner el estado de conexión con la clave `ce-connectionstate` en el **encabezado de respuesta**.

## <a name="output-binding"></a>Enlace de salida

Use el enlace de salida de Web PubSub para invocar al servicio Azure Web PubSub para hacer algo. Puede difundir un mensaje a:

* Todos los clientes conectados
* Clientes conectados autenticados para un usuario específico
* Clientes conectados unidos a un grupo específico
* Una conexión de cliente específica

El enlace de salida también le permite administrar clientes y grupos, y conceder o revocar permisos destinados al identificador de conexión con el grupo específico.

* Agregar conexión al grupo
* Agregar usuario a un grupo
* Eliminación de una conexión de un grupo
* Eliminación de un usuario de un grupo
* Eliminación de un usuario de todos los grupos
* Cierre de todas las conexiones de cliente
* Cierre de una conexión de cliente específica
* Cierre de conexiones de un grupo
* Concesión del permiso de una conexión
* Revocación del permiso de una conexión

Para obtener información sobre los detalles de instalación y configuración, consulte la información general.

### <a name="example"></a>Ejemplo

# <a name="c"></a>[C#](#tab/csharp)

```cs
[FunctionName("WebPubSubOutputBinding")]
public static async Task RunAsync(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post")] HttpRequest req,
    [WebPubSub(Hub = "<hub>")] IAsyncCollector<WebPubSubAction> actions)
{
    await actions.AddAsync(WebPubSubAction.CreateSendToAllAction("Hello Web PubSub!", WebPubSubDataType.Text));
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
      "name": "actions",
      "hub": "<hub>",
      "direction": "out"
    }
  ]
}
```

Defina la función en `index.js`.

```js
module.exports = async function (context) {
  context.bindings.actions = {
    "actionName": "sendToAll",
    "data": "hello",
    "dataType": "text"
  };
  context.done();
}
```

---

### <a name="webpubsubaction"></a>WebPubSubAction 

`WebPubSubAction` es el tipo abstracto base de los enlaces de salida. Los tipos derivados representan el servidor de acción que quiere que se invoque el servicio. 

# <a name="c"></a>[C#](#tab/csharp)

En el lenguaje C#, se proporcionan algunos métodos estáticos en `WebPubSubAction` para ayudar a detectar las acciones disponibles. Por ejemplo, el usuario puede crear `SendToAllAction` mediante una llamada a `WebPubSubAction.CreateSendToAllAction()`.

| Clase derivada | Propiedades |
| -- | -- |
| `SendToAllAction`|Data, DataType, Excluded |
| `SendToGroupAction`|Group, Data, DataType, Excluded |
| `SendToUserAction`|UserId, Data, DataType |
| `SendToConnectionAction`|ConnectionId, Data, DataType |
| `AddUserToGroupAction`|UserId, Group |
| `RemoveUserFromGroupAction`|UserId, Group |
| `RemoveUserFromAllGroupsAction`|UserId |
| `AddConnectionToGroupAction`|ConnectionId, Group |
| `RemoveConnectionFromGroupAction`|ConnectionId, Group |
| `CloseAllConnectionsAction`|Excluded, Reason |
| `CloseClientConnectionAction`|ConnectionId, Reason |
| `CloseGroupConnectionsAction`|Group, Excluded, Reason |
| `GrantPermissionAction`|ConnectionId, Permission, TargetName |
| `RevokePermissionAction`|ConnectionId, Permission, TargetName |

# <a name="javascript"></a>[JavaScript](#tab/javascript)

En un lenguaje débilmente tipado como `javascript`, **`actionName`** es el parámetro clave para resolver el tipo, las acciones disponibles se enumeran como se indica a continuación.

| ActionName | Propiedades |
| -- | -- |
| `SendToAll`|Data, DataType, Excluded |
| `SendToGroup`|Group, Data, DataType, Excluded |
| `SendToUser`|UserId, Data, DataType |
| `SendToConnection`|ConnectionId, Data, DataType |
| `AddUserToGroup`|UserId, Group |
| `RemoveUserFromGroup`|UserId, Group |
| `RemoveUserFromAllGroups`|UserId |
| `AddConnectionToGroup`|ConnectionId, Group |
| `RemoveConnectionFromGroup`|ConnectionId, Group |
| `CloseAllConnections`|Excluded, Reason |
| `CloseClientConnection`|ConnectionId, Reason |
| `CloseGroupConnections`|Group, Excluded, Reason |
| `GrantPermission`|ConnectionId, Permission, TargetName |
| `RevokePermission`|ConnectionId, Permission, TargetName |

> [!IMPORTANT]
> La propiedad de datos del mensaje en las acciones relacionadas con el envío de mensajes debe ser `string` si el tipo de datos está establecido en `json` o `text` para evitar la ambigüedad de la conversión de datos. Use `JSON.stringify()` para convertir el objeto json que necesite. Esto se aplica a cualquier lugar mediante la propiedad de mensaje, por ejemplo, `UserEventResponse.Data` trabajando con `WebPubSubTrigger`. 
> 
> Si el tipo de datos está establecido en `binary`, se permite usar el enlace admitido de forma natural `dataType` como `binary` como se configuró en `function.json`. Consulte [Definiciones de desencadenador y enlace](/azure/azure-functions/functions-triggers-bindings?tabs=csharp#trigger-and-binding-definitions) para más información.

---

### <a name="configuration"></a>Configuración

#### <a name="webpubsub"></a>WebPubSub

En la siguiente tabla se explican las propiedades de configuración de enlace que se definen en el archivo function.json y el atributo `WebPubSub`.

| Propiedad de function.json | Propiedad de atributo | Descripción |
|---------|---------|---------|
| **type** | N/D | Se debe establecer en `webPubSub` |
| **direction** | N/D | Se debe establecer en `out` |
| **name** | N/D | Nombre de variable utilizado en el código de función para el objeto de enlace de la tabla. |
| **hub** | Concentrador | El valor debe establecerse en el nombre del centro de Web PubSub para la función que se va a desencadenar. Se permite establecer el valor en el atributo como prioridad más alta o se puede establecer en la configuración de la aplicación como un valor global. |
| **connection** | Conexión | Nombre de la configuración de la aplicación que contiene la cadena de conexión del servicio Web PubSub (el valor predeterminado es "AzureSignalRConnectionString"). |

## <a name="troubleshooting"></a>Solución de problemas

### <a name="setting-up-console-logging"></a>Configuración del registro de la consola
También puede [habilitar el registro de la consola](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#logging) fácilmente si desea profundizar más en las solicitudes que realiza en el servicio.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/functions

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]