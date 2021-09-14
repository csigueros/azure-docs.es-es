---
title: 'Referencia: SDK de JavaScript para el servicio Azure Web PubSub'
description: En la referencia se describe el SDK de JavaScript para el servicio Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 1d507f29b184403cb9ef31d84111af60f75c1584
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123114231"
---
# <a name="javascript-sdk-for-the-azure-web-pubsub-service"></a>SDK de JavaScript para el servicio Azure Web PubSub

Hay dos bibliotecas que se ofrecen para JavaScript:
- [Biblioteca cliente de servicio](#service-client-library) para
    - Enviar mensajes a centros y grupos.
    - Enviar mensajes a determinados usuarios y conexiones
    - Organizar usuarios y conexiones en grupos
    - Cerrar conexiones
    - Conceder, revocar y comprobar permisos para una conexión existente
- [Middleware express](#express) para controlar eventos de cliente entrantes
  - Control de solicitudes de validación de abusos
  - Control de solicitudes de eventos de cliente

<a name="service-client-library"></a>

## <a name="azure-web-pubsub-service-client-library-for-javascript"></a>Biblioteca cliente del servicio Azure Web PubSub para JavaScript
Use la biblioteca para:

- Enviar mensajes a centros y grupos.
- Enviar mensajes a determinados usuarios y conexiones
- Organizar usuarios y conexiones en grupos
- Cerrar conexiones
- Conceder, revocar y comprobar permisos para una conexión existente

[Código fuente](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub) |
[Paquete (NPM)](https://www.npmjs.com/package/@azure/web-pubsub) |
[Documentación de referencia de API](/javascript/api/@azure/web-pubsub/) |
[Documentación del producto](https://aka.ms/awps/doc) |
[Ejemplos][samples_ref]

### <a name="getting-started"></a>Introducción

#### <a name="currently-supported-environments"></a>Entornos admitidos actualmente

- [Node.js](https://nodejs.org/) versión 8.x.x o posterior

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

#### <a name="enable-logs"></a>Habilitamiento de registros

Puede establecer la siguiente variable de entorno para obtener los registros de depuración cuando se usa esta biblioteca.

- Obtención de registros de depuración de la biblioteca cliente de SignalR

```bash
export AZURE_LOG_LEVEL=verbose
```

Para obtener instrucciones más detalladas sobre cómo habilitar los registros, consulte los documentos del paquete [@azure/logger ](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

<a name="express"></a>

## <a name="azure-web-pubsub-cloudevents-handlers-for-express"></a>Controladores de CloudEvents de Azure Web PubSub para Express

Use Express Library para:
- Agregar middleware de Web PubSub CloudEvents para controlar los eventos de cliente entrantes
  - Controlar solicitudes de validación de abusos
  - Controlar solicitudes de eventos de cliente

[Código fuente](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/web-pubsub/web-pubsub-express) |
[Paquete (NPM)](https://www.npmjs.com/package/@azure/web-pubsub-express) |
[Documentación de referencia de API](/javascript/api/@azure/web-pubsub-express/) |
[Documentación del producto](https://aka.ms/awps/doc) |
[Ejemplos][samples_ref]

### <a name="getting-started"></a>Introducción

#### <a name="currently-supported-environments"></a>Entornos admitidos actualmente

- [Node.js](https://nodejs.org/) versión 8.x.x o posterior
- [Express](https://expressjs.com/) versión 4.x.x o superior

#### <a name="prerequisites"></a>Requisitos previos

- Una [suscripción de Azure][azure_sub].
- Un punto de conexión de Azure Web PubSub existente.

#### <a name="1-install-the-azureweb-pubsub-express-package"></a>1. Instale el paquete `@azure/web-pubsub-express`.

```bash
npm install @azure/web-pubsub-express
```

#### <a name="2-create-a-webpubsubeventhandler"></a>2. Cree un WebPubSubEventHandler.

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

#### <a name="client-events"></a>Eventos de cliente

Los eventos se crean durante el ciclo de vida de una conexión de cliente. Por ejemplo, una conexión del cliente WebSocket simple crea un evento `connect` cuando intenta conectarse al servicio, un evento `connected` cuando se conecta correctamente al servicio, un evento `message` cuando envía mensajes al servicio y un evento `disconnected` cuando se desconecta del servicio.

#### <a name="event-handler"></a>Controlador de eventos

El controlador de eventos contiene la lógica para controlar los eventos del cliente. El controlador de eventos debe registrarse y configurarse en el servicio a través del portal o de la CLI de Azure de antemano. El lugar donde hospedar la lógica del controlador de eventos se suele considerar como el lado servidor.

### <a name="troubleshooting"></a>Solución de problemas

#### <a name="dump-request"></a>Solicitud de volcado

Establezca `dumpRequest` en `true` para ver las solicitudes entrantes.

#### <a name="live-trace"></a>Seguimiento en vivo

Use **Seguimiento en vivo** desde el portal del servicio Web PubSub para ver el tráfico en directo.

[azure_sub]: https://azure.microsoft.com/free/
[samples_ref]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript


## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
