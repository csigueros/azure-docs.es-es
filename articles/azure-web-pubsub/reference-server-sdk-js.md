---
title: 'Referencia: SDK de JavaScript para Azure Web PubSub'
description: En esta referencia se describe el SDK de JavaScript para el servicio Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/01/2021
ms.openlocfilehash: 4571bb8581892525785c3f08e0c627bf20f511aa
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578352"
---
# <a name="javascript-sdk-for-azure-web-pubsub"></a>SDK de JavaScript para Azure Web PubSub

Hay dos bibliotecas que se ofrecen para JavaScript: la biblioteca cliente de servicio y el middleware express. Las secciones siguientes contienen más información sobre estas bibliotecas.

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>Biblioteca cliente del servicio Azure Web PubSub para JavaScript

Puede usar esta biblioteca para:
- Enviar mensajes a centros y grupos. 
- Enviar mensajes a determinados usuarios y conexiones.
- Organizar usuarios y conexiones en grupos.
- Cerrar conexiones.
- Conceder, revocar y comprobar permisos para una conexión existente.

[Código fuente](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[Paquete (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[Documentación de referencia de API](/javascript/api/@azure/web-pubsub/) |
[Documentación del producto](./index.yml) |
[Ejemplos][samples_ref]

### <a name="get-started"></a>Introducción

Use [Node.js](https://nodejs.org/), la versión 8.x o posteriores. Asimismo, asegúrese de que cumple los siguientes requisitos previos:

- Una [suscripción de Azure][azure_sub].
- Una instancia existente del servicio Azure Web PubSub.

#### <a name="install-the-azureweb-pubsub-package"></a>Instalar el paquete `@azure/web-pubsub`

```bash
npm install @azure/web-pubsub
```

#### <a name="create-and-authenticate-webpubsubserviceclient"></a>Creación y autenticación de `WebPubSubServiceClient`

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

### <a name="key-concepts"></a>Conceptos clave

[!INCLUDE [Terms](includes/terms.md)]

### <a name="examples"></a>Ejemplos

#### <a name="broadcast-a-json-message-to-all-users"></a>Difusión de un mensaje JSON a todos los usuarios

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll({ message: "Hello world!" });
```

#### <a name="broadcast-a-plain-text-message-to-all-users"></a>Difusión de un mensaje de texto sin formato a todos los usuarios

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");
await serviceClient.sendToAll("Hi there!", { contentType: "text/plain" });
```

#### <a name="broadcast-a-binary-message-to-all-users"></a>Difusión de un mensaje binario a todos los usuarios

```js
const { WebPubSubServiceClient } = require("@azure/web-pubsub");

const serviceClient = new WebPubSubServiceClient("<ConnectionString>", "<hubName>");

const payload = new Uint8Array(10);
await serviceClient.sendToAll(payload.buffer);
```

### <a name="troubleshooting"></a>Solución de problemas

Puede establecer la siguiente variable de entorno para obtener los registros de depuración cuando use esta biblioteca:

- Obtención de registros de depuración de la biblioteca cliente de SignalR

```bash
export AZURE_LOG_LEVEL=verbose
```

Para obtener instrucciones más detalladas sobre cómo habilitar los registros, vea los documentos del paquete [@azure/logger](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Controladores de CloudEvents de Azure Web PubSub para Express

Puede usar Express Library para:
- Agregar middleware de Web PubSub CloudEvents para controlar los eventos de cliente entrantes.
  - Controlar solicitudes de validación de abusos.
  - Controlar solicitudes de eventos de cliente.

[Código fuente](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[Paquete (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[Documentación de referencia de API](/javascript/api/@azure/web-pubsub-express/) |
[Documentación del producto](./index.yml) |
[Ejemplos][samples_ref]

### <a name="get-started"></a>Primeros pasos

Use la versión 8.x.x o posterior de [Node.js](https://nodejs.org/) o la versión 4.x.x o posterior de [Express](https://expressjs.com/). Asimismo, asegúrese de que cumple los siguientes requisitos previos:

- Una [suscripción de Azure][azure_sub].
- Un punto de conexión de Azure Web PubSub existente.

#### <a name="install-the-azureweb-pubsub-express-package"></a>Instalar el paquete `@azure/web-pubsub-express`

```bash
npm install @azure/web-pubsub-express
```

#### <a name="create-webpubsubeventhandler"></a>Cree `WebPubSubEventHandler`

```js
const express = require("express");

const { WebPubSubEventHandler } = require("@azure/web-pubsub-express");
const handler = new WebPubSubEventHandler(
  "chat",
  ["https://<yourAllowedService>.webpubsub.azure.com"],
  {
    handleConnect: (req, res) => {
      // auth the connection and set the userId of the connection
      res.success({
        userId: "<userId>"
      });
    }
  }
);

const app = express();

app.use(handler.getMiddleware());

app.listen(3000, () =>
  console.log(`Azure WebPubSub Upstream ready at http://localhost:3000${handler.path}`)
);
```

### <a name="key-concepts"></a>Conceptos clave

- **Eventos del cliente**:un cliente crea eventos durante el ciclo de vida de una conexión. Por ejemplo, una conexión de cliente de webSocket simple crea los siguientes eventos:
  - Un evento `connect` cuando intenta conectarse al servicio.
  - Un evento `connected` cuando se conecta al servicio.
  - Un evento `message` cuando envía mensajes al servicio.
  - Un evento `disconnected` cuando se desconecta del servicio.

- **Controlador de eventos**: un controlador de eventos contiene la lógica para controlar los eventos del cliente. El controlador de eventos debe registrarse y configurarse en el servicio de antemano, a través de Azure Portal o de la CLI de Azure. Por lo general, el servidor hospeda la lógica del controlador de eventos.

### <a name="troubleshooting"></a>Solución de problemas

#### <a name="dump-request"></a>Solicitud de volcado

Establezca `dumpRequest` en `true` para ver las solicitudes entrantes.

#### <a name="live-trace"></a>Seguimiento en vivo

Use **Seguimiento en vivo** desde el portal del servicio Web PubSub para ver el tráfico en directo.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]