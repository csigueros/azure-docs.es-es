---
title: Protocolos de cliente de WebSocket para Azure Web PubSub
description: Introducción a los protocolos de cliente para Azure Web PubSub
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: e08f595e2dc80abe06fa68d1a3e30ac68ff0097c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426353"
---
#  <a name="websocket-client-protocols-for-azure-web-pubsub"></a>Protocolos de cliente de WebSocket para Azure Web PubSub

Los clientes se conectan a Azure Web PubSub mediante el protocolo [WebSocket](https://datatracker.ietf.org/doc/html/rfc6455) estándar.

## <a name="service-endpoint"></a>Punto de conexión de servicio
El servicio proporciona dos tipos de puntos de conexión a los que se pueden conectar los clientes:
1. `/client/hubs/{hub}`
2. `/client/?hub={hub}`

`{hub}` es un parámetro obligatorio que actúa como aislamiento para distintas aplicaciones. Se puede establecer en la ruta de acceso o en la consulta.

## <a name="auth"></a>Auth
1. El cliente se conecta al servicio con el token JWT

### <a name="1-the-client-connects-using-the-jwt-token"></a>1. El cliente se conecta mediante el token JWT.

El token JWT puede estar en la cadena de consulta `/client/?hub={hub}&access_token={token}` o en el encabezado `Authorization`: `Authorization: Bearer {token}`.

Un flujo de trabajo general consiste en lo siguiente:
1. El cliente negocia con el servidor de aplicaciones. El servidor de aplicaciones tiene middleware de autenticación para controlar la solicitud de cliente y firmar un token JWT para que el cliente se conecte al servicio.
2. El servidor de aplicaciones devuelve el token JWT y la dirección URL del servicio al cliente.
3. El cliente intenta conectarse al servicio de Web PubSub mediante la dirección URL y el token JWT devueltos desde el servidor de aplicaciones.

<a name="simple_client"></a>

## <a name="the-simple-websocket-client"></a>Cliente simple de WebSocket
Un cliente simple de WebSocket, como indica la nomenclatura, es una conexión simple de WebSocket. También puede tener su subprotocolo personalizado. 

Por ejemplo, en JS, se puede crear un cliente simple de WebSocket mediante:
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

## <a name="the-pubsub-websocket-client"></a>El cliente WebSocket de PubSub

El cliente de WebSocket de PubSub admite dos subprotocolos: `json.webpubsub.azure.v1` y `protobuf.webpubsub.azure.v1`

#### <a name="json-subprotocol"></a>Subprotocolo json

Por ejemplo, en JS, se puede crear un cliente WebSocket de PubSub con el subprotocolo json del modo siguiente:
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="protobuf-subprotocol"></a>Subprotocolo protobuf

Protobuf es un protocolo basado en binarios independiente del lenguaje, independiente de la plataforma, que es muy fácil de usar para enviar datos binarios. Protobuf proporciona herramientas para generar clientes para muchos lenguajes como Java, Python, Objective-C, C# y C++. [Más información sobre protobuf](https://developers.google.com/protocol-buffers).

Por ejemplo, en JS, se puede crear un cliente WebSocket de PubSub con el subprotocolo protobuf del modo siguiente:
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

Cuando el cliente usa un subprotocolo, se espera que tanto la trama de datos saliente como la trama de datos entrante sean cargas JSON. Un cliente autenticado puede publicar mensajes en otros clientes directamente a través del servicio de Azure Web PubSub.

### <a name="permissions"></a>Permisos

Como puede que haya observado, cuando se describieron los clientes WebSocket de PubSub, un cliente solo puede publicar en otros clientes cuando está *autorizado*. Se puede conceder el permiso del cliente cuando está conectado o durante la duración de la conexión.

| Rol | Permiso |
|---|---|
| No especificado | El cliente puede enviar solicitudes de eventos.
| `webpubsub.joinLeaveGroup` | El cliente puede unirse a cualquier grupo o salir de este.
| `webpubsub.sendToGroup` | El cliente puede publicar mensajes en cualquier grupo.
| `webpubsub.joinLeaveGroup.<group>` | El cliente puede unirse al grupo `<group>` o abandonarlo.
| `webpubsub.sendToGroup.<group>` | El cliente puede publicar mensajes en el grupo `<group>`.

* Los detalles del subprotocolo json se describen en [Subprotocolo JSON](./reference-json-webpubsub-subprotocol.md).
* Los detalles del subprotocolo Protobuf se describen en [Subprotocolo Protobuf](./reference-protobuf-webpubsub-subprotocol.md).

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
