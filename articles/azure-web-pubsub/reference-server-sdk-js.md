---
title: 'Referencia: SDK de JavaScript para Azure Web PubSub'
description: En esta referencia se describe el SDK de JavaScript para el servicio Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: 899f9845721412ceef9171c334383e596fd798e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493080"
---
# <a name="javascript-sdk-for-azure-web-pubsub"></a>SDK de JavaScript para Azure Web PubSub

[Azure Web PubSub](https://aka.ms/awps/doc) es un servicio administrado de Azure que ayuda a los desarrolladores a compilar fácilmente aplicaciones web con características en tiempo real y patrón de publicación-suscripción. Cualquier escenario que requiera mensajería de publicación y suscripción en tiempo real entre el servidor y los clientes o entre clientes, puede usar el servicio Azure Web PubSub. Las características tradicionales en tiempo real que a menudo requieren tener que sondear desde el servidor o enviar solicitudes HTTP, también pueden usar el servicio Azure Web PubSub.

Hay dos bibliotecas que se ofrecen para JavaScript: la biblioteca cliente de servicio y el middleware express. Las secciones siguientes contienen más información sobre estas bibliotecas.

- [Biblioteca cliente de servicio](#service-client-library)
- [Middleware express](#express)

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>Biblioteca cliente del servicio Azure Web PubSub para JavaScript

Puede usar esta biblioteca en el lado del servidor de aplicaciones para administrar las conexiones de cliente de WebSocket, como se muestra en el diagrama siguiente:

![El diagrama de desbordamiento muestra el desbordamiento que supone el uso de la biblioteca cliente del servicio.](media/sdk-reference/service-client-overflow.png)

- Enviar mensajes a centros y grupos.
- Enviar mensajes a determinados usuarios y conexiones.
- Organizar usuarios y conexiones en grupos.
- Cerrar conexiones.
- Conceder, revocar y comprobar permisos para una conexión existente.

(Los detalles sobre los términos usados aquí se describen en la sección [Conceptos clave](#key-concepts)):

[Código fuente](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/web-pubsub/web-pubsub) |
[Paquete (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[Documentación de referencia de API](https://aka.ms/awps/sdk/js) |
[Documentación del producto](https://aka.ms/awps/doc) |
[Ejemplos][samples_ref]

### <a name="getting-started"></a>Introducción

#### <a name="currently-supported-environments"></a>Entornos admitidos actualmente

- [Versiones de LTS de Node.js](https://nodejs.org/about/releases/)

#### <a name="prerequisites"></a>Requisitos previos

- Una [suscripción de Azure][azure_sub].
- Una instancia del servicio Azure Web PubSub existente.

#### <a name="1-install-the-azureweb-pubsub-package"></a>1. Instale el paquete `@azure/web-pubsub`.

```bash
npm install @azure/web-pubsub
```

#### <a name="2-create-and-authenticate-a-webpubsubserviceclient"></a>2. Cree y autentique de WebPubSubServiceClient.

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
```

También puede autenticar `WebPubSubServiceClient` mediante un punto de conexión y un `AzureKeyCredential`:

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new AzureKeyCredential("<Key>");
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

O autentique el `WebPubSubServiceClient` mediante [Azure Active Directory] [aad_doc]

1. Instale la dependencia `@azure/identity`

```batch
npm install @azure/identity
```

2. Actualización del código fuente para usar `DefaultAzureCredential`:

```js
const { WebPubSubServiceClient, AzureKeyCredential } = require("@azure/web-pubsub");

const key = new DefaultAzureCredential();
const serviceClient = new WebPubSubServiceClient("<Endpoint>", key, "<hubName>");
```

### <a name="key-concepts"></a>Conceptos clave

#### <a name="connection"></a>Conexión

Una conexión, también conocida como "cliente" o "conexión de cliente", representa una conexión WebSocket individual conectada al servicio Web PubSub. Cuando se conecta correctamente, el servicio Web PubSub asigna un identificador de conexión único a esta conexión.

#### <a name="hub"></a>Hub

El centro de conectividad es un concepto lógico para un conjunto de conexiones de clientes. Normalmente se usa un centro de conectividad para una finalidad, por ejemplo, un centro de conectividad de chats o uno de notificaciones. Cuando se establece una conexión de cliente, esta se realiza con un centro de conectividad y, durante su vigencia, pertenece a dicho centro. Diferentes aplicaciones pueden compartir un servicio de Azure Web PubSub mediante el uso de nombres de centros de conectividad diferentes.

#### <a name="group"></a>Grupo

El grupo es un subconjunto de conexiones al centro de conectividad. Puede agregar una conexión de cliente a un grupo o quitarla de este cuando quiera. Por ejemplo, cuando un cliente se une a una sala de chat o sale de ella, dicha sala puede considerarse un grupo. Un cliente puede unirse a varios grupos, y un grupo puede contener varios clientes.

#### <a name="user"></a>Usuario

Las conexiones a Web PubSub pueden pertenecer a un usuario. Un usuario puede tener varias conexiones, por ejemplo, cuando está conectado a través de varios dispositivos o distintas pestañas del explorador.

#### <a name="message"></a>Message

Cuando el cliente está conectado, puede enviar mensajes a la aplicación de nivel superior o recibir mensajes de esta, mediante la conexión WebSocket.

### <a name="examples"></a>Ejemplos

#### <a name="get-the-access-token-for-a-client-to-start-the-websocket-connection"></a>Obtener el token de acceso para que un cliente inicie la conexión de WebSocket

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Get the access token for the WebSocket client connection to use
let token = await serviceClient.getClientAccessToken();

// Or get the access token and assign the client a userId
token = await serviceClient.getClientAccessToken({ userId: "user1" });

// return the token to the WebSocket client
```

#### <a name="broadcast-messages-to-all-connections-in-a-hub"></a>Difunde mensajes a todas las conexiones en un concentrador

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Send a JSON message
await serviceClient.sendToAll({ message: "Hello world!" });

// Send a plain text message
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

#### <a name="send-messages-to-all-connections-in-a-group"></a>Envía mensajes a todas las conexiones en un grupo

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const groupClient = serviceClient.group("<groupName>");

// Add user to the group
await groupClient.addUser("user1");

// Send a JSON message
await groupClient.sendToAll({ message: "Hello world!" });

// Send a plain text message
await groupClient.sendToAll("Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await groupClient.sendToAll(payload.buffer);
```

#### <a name="send-messages-to-all-connections-for-a-user"></a>Envía mensajes a todas las conexiones para un usuario

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

// Send a JSON message
await serviceClient.sendToUser("user1", { message: "Hello world!" });

// Send a plain text message
await serviceClient.sendToUser("user1", "Hi there!", { contentType: "text/plain" });

// Send a binary message
const payload = new Uint8Array(10);
await serviceClient.sendToUser("user1", payload.buffer);
```

#### <a name="check-if-the-group-has-any-connection"></a>Comprobación de si el grupo tiene alguna conexión

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");
const WebSocket = require("ws");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const groupClient = serviceClient.group("<groupName>");

// close all the connections in the group
await groupClient.closeAllConnections({ reason: "<closeReason>" });

// check if the group has any connections
const hasConnections = await serviceClient.groupExists("<groupName>");
```

#### <a name="access-the-raw-http-response-for-an-operation"></a>Acceso a la respuesta HTTP sin formato para una operación

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

function onResponse(rawResponse: FullOperationResponse): void {
  console.log(rawResponse);
}
const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" }, { onResponse });
```

### <a name="troubleshooting"></a>Solución de problemas

#### <a name="enable-logs"></a>Habilitamiento de registros

Puede establecer la siguiente variable de entorno para obtener los registros de depuración cuando se usa esta biblioteca.

- Obtención de registros de depuración de la biblioteca cliente de SignalR

```bash
export AZURE_LOG_LEVEL=verbose
```

Para obtener instrucciones más detalladas sobre cómo habilitar los registros, consulte los documentos del paquete [@azure/logger ](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/core/logger).

#### <a name="live-trace"></a>Seguimiento en vivo

Use **Seguimiento en vivo** desde el portal del servicio Web PubSub para ver el tráfico en directo.

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Controladores de CloudEvents de Azure Web PubSub para Express

Cuando se conecta una conexión de WebSocket, el servicio PubSub web transforma el ciclo de vida de la conexión y los mensajes en [eventos en formato CloudEvents](concept-service-internals.md#workflow). Esta biblioteca proporciona un middleware express para controlar eventos que representan el ciclo de vida y los mensajes de la conexión webSocket, como se muestra en el diagrama siguiente:

![El diagrama de desbordamiento muestra el desbordamiento que supone el uso del middleware de controlador de eventos.](media/sdk-reference/event-handler-middleware.png)

(Los detalles sobre los términos usados aquí se describen en la sección [Conceptos clave](#key-concepts)):

[Código fuente](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/web-pubsub/web-pubsub-express) |
[Paquete (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[Documentación de referencia de API](https://aka.ms/awps/sdk/js) |
[Documentación del producto](https://aka.ms/awps/doc) |
[Ejemplos][samples_ref]

### <a name="getting-started"></a>Introducción

#### <a name="currently-supported-environments"></a>Entornos admitidos actualmente

- [Versiones de LTS de Node.js](https://nodejs.org/about/releases/)
- [Express](https://expressjs.com/) versión 4.x.x o superior

#### <a name="prerequisites"></a>Requisitos previos

- Una [suscripción de Azure][azure_sub].
- Un punto de conexión de Azure Web PubSub existente.

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. Instale el paquete `@azure/web-pubsub-express`.

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. Cree un `WebPubSubEventHandler`

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat");

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>Conceptos clave

#### <a name="connection"></a>Conexión

Una conexión, también conocida como "cliente" o "conexión de cliente", representa una conexión WebSocket individual conectada al servicio Web PubSub. Cuando se conecta correctamente, el servicio Web PubSub asigna un identificador de conexión único a esta conexión.

#### <a name="hub"></a>Hub

El centro de conectividad es un concepto lógico para un conjunto de conexiones de clientes. Normalmente se usa un centro de conectividad para una finalidad, por ejemplo, un centro de conectividad de chats o uno de notificaciones. Cuando se establece una conexión de cliente, esta se realiza con un centro de conectividad y, durante su vigencia, pertenece a dicho centro. Diferentes aplicaciones pueden compartir un servicio de Azure Web PubSub mediante el uso de nombres de centros de conectividad diferentes.

#### <a name="group"></a>Grupo

El grupo es un subconjunto de conexiones al centro de conectividad. Puede agregar una conexión de cliente a un grupo o quitarla de este cuando quiera. Por ejemplo, cuando un cliente se une a una sala de chat o sale de ella, dicha sala puede considerarse un grupo. Un cliente puede unirse a varios grupos, y un grupo puede contener varios clientes.

#### <a name="user"></a>Usuario

Las conexiones a Web PubSub pueden pertenecer a un usuario. Un usuario puede tener varias conexiones, por ejemplo, cuando está conectado a través de varios dispositivos o distintas pestañas del explorador.

#### <a name="client-events"></a>Eventos de cliente

Los eventos se crean durante el ciclo de vida de una conexión de cliente. Por ejemplo, una conexión del cliente WebSocket simple crea un evento `connect` cuando intenta conectarse al servicio, un evento `connected` cuando se conecta correctamente al servicio, un evento `message` cuando envía mensajes al servicio y un evento `disconnected` cuando se desconecta del servicio.

#### <a name="event-handler"></a>Controlador de eventos

El controlador de eventos contiene la lógica para controlar los eventos del cliente. El controlador de eventos debe registrarse y configurarse en el servicio a través del portal o de la CLI de Azure de antemano. El lugar donde hospedar la lógica del controlador de eventos se suele considerar como el lado servidor.

### <a name="examples"></a>Ejemplos

#### <a name="handle-the-connect-request-and-assign-userid"></a>Control de la solicitud `connect` y asignación de `<userId>`

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  handleConnect: (req, res) => {
    // auth the connection and set the userId of the connection
    res.success({
      userId: "<userId>"
    });
  },
  allowedEndpoints: ["https://<yourAllowedService>.webpubsub.azure.com"]
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="only-allow-specified-endpoints"></a>Permitir solo los puntos de conexión especificados

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  allowedEndpoints: [
    "https://<yourAllowedService1>.webpubsub.azure.com",
    "https://<yourAllowedService2>.webpubsub.azure.com"
  ]
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="set-custom-event-handler-path"></a>Establecer la ruta de acceso del controlador de eventos personalizado

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler("chat", {
  path: "customPath1"
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  // Azure WebPubSub Upstream ready at http://localhost:3000/customPath1
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

#### <a name="set-and-read-connection-state"></a>Establecer y leer el estado de conexión

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");

const handler = new WebPubSubEventHandler("chat", {
  handleConnect(req, res) {
    // You can set the state for the connection, it lasts throughout the lifetime of the connection
    res.setState("calledTime", 1);
    res.success();
  },
  handleUserEvent(req, res) {
    var calledTime = req.context.states.calledTime++;
    console.log(calledTime);
    // You can also set the state here
    res.setState("calledTime", calledTime);
    res.success();
  }
});

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="troubleshooting"></a>Solución de problemas

#### <a name="enable-logs"></a>Habilitamiento de registros

Puede establecer la siguiente variable de entorno para obtener los registros de depuración cuando se usa esta biblioteca.

- Obtención de registros de depuración de la biblioteca cliente de SignalR

```bash
export AZURE_LOG_LEVEL=verbose
```

Para obtener instrucciones más detalladas sobre cómo habilitar los registros, consulte los documentos del paquete [@azure/logger ](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/core/logger).

#### <a name="live-trace"></a>Seguimiento en vivo

Use **Seguimiento en vivo** desde el portal del servicio Web PubSub para ver el tráfico en directo.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]