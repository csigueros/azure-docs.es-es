---
title: Protocolos de cliente de WebSocket para Azure Web PubSub
description: En este artículo se presenta una introducción a los protocolos de cliente para Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 8f78dbcdecc552e94c3d871f610ef227a1795f8e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576267"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Protocolos de cliente de WebSocket para Azure Web PubSub

Los clientes se conectan a Azure Web PubSub mediante el protocolo [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) estándar.

## <a name="service-endpoints"></a>Puntos de conexión del servicio
El servicio Web PubSub proporciona dos tipos de puntos de conexión a los que se pueden conectar los clientes:
* `/client/hubs/{hub}`
* `/client/?hub={hub}`

`{hub}` es un parámetro obligatorio que actúa como aislamiento para distintas aplicaciones. Se puede establecer en la ruta o en la consulta.

## <a name="authorization"></a>Authorization

Los clientes se conectan al servicio con un JSON Web Token (JWT). El token puede estar en la cadena de consulta, como `/client/?hub={hub}&access_token={token}`, o bien en el encabezado, como `Authorization``Authorization: Bearer {token}`.

Este es un flujo de trabajo de autorización general:

1. El cliente negocia con el servidor de aplicaciones. El servidor de aplicaciones contiene el middleware de autenticación, que controla la solicitud de cliente y firma un JWT para que el cliente se conecte al servicio.
1. El servidor de aplicaciones devuelve el JWT y la dirección URL del servicio al cliente.
1. El cliente intenta conectarse al servicio Web PubSub mediante la dirección URL y el JWT devueltos desde el servidor de aplicaciones.

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>Cliente simple de WebSocket

Un cliente simple de WebSocket, como indica la nomenclatura, es una conexión simple de WebSocket. También puede tener su propio subprotocolo personalizado. 

Por ejemplo, en JavaScript, puede crear un cliente WebSocket simple mediante el código siguiente:

```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')
```

## <a name="the-pubsub-websocket-client"></a>El cliente WebSocket de PubSub

El cliente WebSocket de PubSub admite dos subprotocolos:
* `json.webpubsub.azure.v1`
* `protobuf.webpubsub.azure.v1`

#### <a name="the-json-subprotocol"></a>El subprotocolo JSON

Por ejemplo, en JavaScript, puede crear un cliente WebSocket de PubSub con un subprotocolo JSON mediante lo siguiente:

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="the-protobuf-subprotocol"></a>El subprotocolo protobuf

Los búferes de protocolo (protobuf) son un protocolo basado en binarios e independientes de la plataforma y el lenguaje que simplifica el envío de datos binarios. Protobuf proporciona herramientas a fin de generar clientes para muchos lenguajes como Java, Python, Objective-C, C# y C++. [Más información sobre protobuf](https://developers.google.com/protocol-buffers).

Por ejemplo, en JavaScript, puede crear un cliente WebSocket de PubSub con un subprotocolo protobuf mediante el código siguiente:

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

Cuando el cliente usa un subprotocolo, se espera que tanto la trama de datos saliente como la entrante sean cargas JSON. Un cliente autorizado puede publicar mensajes en otros clientes directamente con el servicio Azure Web PubSub.

### <a name="permissions"></a>Permisos

Como habrá observado en la descripción anterior del cliente WebSocket de PubSub, un cliente solo puede publicar en otros cuando está *autorizado* para hacerlo. Se pueden conceder permisos al cliente cuando se conecta o durante la vigencia de la conexión.

| Rol | Permiso |
|---|---|
| No especificado | El cliente puede enviar solicitudes de eventos. |
| `webpubsub.joinLeaveGroup` | El cliente puede unirse a cualquier grupo o abandonarlo. |
| `webpubsub.sendToGroup` | El cliente puede publicar mensajes en cualquier grupo. |
| `webpubsub.joinLeaveGroup.<group>` | El cliente puede unirse al grupo `<group>` o abandonarlo. |
| `webpubsub.sendToGroup.<group>` | El cliente puede publicar mensajes en el grupo `<group>`. |
| | |

Para obtener más información sobre el subprotocolo JSON, vea [Subprotocolo JSON](./reference-json-webpubsub-subprotocol.md).

Para obtener más información sobre el subprotocolo protobuf, vea [Subprotocolo protobuf](./reference-protobuf-webpubsub-subprotocol.md).

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
