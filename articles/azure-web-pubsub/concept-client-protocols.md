---
title: Protocolos de cliente de WebSocket para Azure Web PubSub
description: En este artículo se presenta una introducción a los protocolos de cliente para Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 11/08/2021
ms.openlocfilehash: 58fef1f2ada19af2ff81ef27d5ff5ab2ad4cebf7
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997956"
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

Un **cliente simple de WebSocket**, como indica la nomenclatura, es una conexión simple de WebSocket. También puede tener su propio subprotocolo personalizado. 

Por ejemplo, en JavaScript, puede crear un cliente WebSocket simple mediante el código siguiente:

```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')
```

## <a name="the-pubsub-websocket-client"></a>El cliente WebSocket de PubSub

Un **cliente WebSocket de PubSub** es el cliente de WebSocket que usa los subprotocolos definidos por el servicio Azure Web PubSub:

* `json.webpubsub.azure.v1`
* `protobuf.webpubsub.azure.v1`

Con el subprotocolo compatible con el servicio, los **clientes WebSocket de PubSub** pueden publicar mensajes directamente en grupos cuando tienen los [permisos](#permissions) necesarios.

### <a name="the-jsonwebpubsubazurev1-subprotocol"></a>El subprotocolo `json.webpubsub.azure.v1`

Consulte [aquí todos los detalles sobre el subprotocolo JSON](reference-json-webpubsub-subprotocol.md).

#### <a name="create-a-pubsub-websocket-client"></a>Creación de un cliente WebSocket de PubSub

```js
var pubsubClient = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

#### <a name="join-a-group-from-the-client-directly"></a>Unirse a un grupo directamente desde el cliente

```js
let ackId = 0;
pubsubClient.send(    
    JSON.stringify({
        type: 'joinGroup',
        group: 'group1',
        ackId: ++ackId
    }));
```

#### <a name="send-messages-to-a-group-from-the-client-directly"></a>Envío de mensajes a un grupo directamente desde el cliente

```js
let ackId = 0;
pubsubClient.send(    
    JSON.stringify({
        type: 'sendToGroup',
        group: 'group1',
        ackId: ++ackId,
        dataType: "json",
        data: {
            "hello": "world"
        }
    }));
```

### <a name="the-protobufwebpubsubazurev1-subprotocol"></a>El subprotocolo `protobuf.webpubsub.azure.v1`

Los búferes de protocolo (protobuf) son un protocolo basado en binarios e independientes de la plataforma y el lenguaje que simplifica el envío de datos binarios. Protobuf proporciona herramientas a fin de generar clientes para muchos lenguajes como Java, Python, Objective-C, C# y C++. [Más información sobre protobuf](https://developers.google.com/protocol-buffers).

Por ejemplo, en JavaScript, el siguiente código permite crear un **cliente WebSocket de PubSub** con un subprotocolo protobuf:

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

Consulte [aquí todos los detalles sobre el subprotocolo protobuf](reference-protobuf-webpubsub-subprotocol.md).


### <a name="ackid-and-ack-response"></a>AckId y respuesta de confirmación

El **cliente WebSocket de PubSub** admite la propiedad `ackId` para los mensajes `joinGroup`, `leaveGroup`, `sendToGroup` y `event`. Si usa `ackId`, puede recibir un mensaje de respuesta de confirmación cuando se procesa la solicitud. Puede optar por omitir `ackId` en los escenarios del tipo "activar y olvidar". En el artículo, se describen las diferencias de comportamiento si se especifica `ackId` o si no se hace.

#### <a name="behavior-when-no-ackid-specified"></a>Comportamiento cuando no se especifica `ackId`

Si no se especifica `ackId`, se produce una situación de "activar y olvidar". Incluso si hay errores al intermediar mensajes, no tiene forma de recibir notificaciones.

#### <a name="behavior-when-ackid-specified"></a>Comportamiento cuando se especifica `ackId`

##### <a name="idempotent-publish"></a>Publicación idempotente

`ackId` es un número uint64 y debe ser único dentro de un cliente con el mismo identificador de conexión. El servicio Azure Web PubSub registra `ackId` y los mensajes con el mismo `ackId` se tratarán como el mismo mensaje. El servicio rechaza la intermediación en el mismo mensaje más de una vez, lo que resulta útil para volver a intentar evitar que haya mensajes duplicados. Por ejemplo, si un cliente envía un mensaje con `ackId=5` y no recibe una respuesta de confirmación con `ackId=5`, el cliente vuelve a intentarlo y envía el mismo mensaje de nuevo. En algunos casos, el mensaje ya es asincrónico y, por algún motivo, se pierde la respuesta confirmación, el servicio rechazará el reintento y enviará una respuesta de confirmación con el motivo `Duplicate`.

#### <a name="ack-response"></a>Respuesta de confirmación

El servicio Web PubSub envía una respuesta de confirmación a cada solicitud con `ackId`.

Formato:
```json
{
    "type": "ack",
    "ackId": 1, // The ack id for the request to ack
    "success": false, // true or false
    "error": {
        "name": "Forbidden|InternalServerError|Duplicate",
        "message": "<error_detail>"
    }
}
```

* `ackId` asocia la solicitud.

* `success` es un valor booleano e indica si el servicio procesa correctamente la solicitud. Si es `false`, los clientes deben comprobar `error`.

* `error` solo existe cuando `success` es `false` y los clientes deben tener una lógica diferente para diferentes `name`. Debe suponer que puede haber más tipos de `name` en el futuro.
    - `Forbidden`: el cliente no tiene permiso para la solicitud. El cliente necesita que se agreguen roles pertinentes.
    - `InternalServerError`: se produjo un error interno en el servicio. Se requiere un reintento.
    - `Duplicate`: el servicio ya ha procesado el mensaje con el mismo `ackId`.

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

El permiso de un cliente se puede conceder de varias maneras:

#### <a name="1-assign-the-role-to-the-client-when-generating-the-access-token"></a>1. Asignación del rol al cliente al generar el token de acceso

El cliente puede conectarse al servicio mediante un token JWT; la carga del token puede llevar información como el `role` del cliente. Al firmar el token JWT en el cliente, puede conceder permisos al cliente mediante la asignación al mismo de roles concretos.

Por ejemplo, vamos a firmar un token JWT que tenga permiso para enviar mensajes a `group1` y `group2`: 

```js
let token = await serviceClient.getClientAccessToken({
    roles: [ "webpubsub.sendToGroup.group1", "webpubsub.sendToGroup.group2" ]
});
```

#### <a name="2-assign-the-role-to-the-client-with-the-connect-event-handler"></a>2. Asignación del rol al cliente con el controlador de eventos `connect`

Los roles de los clientes también se pueden establecer cuando se registra el controlador de eventos `connect` y el controlador de eventos ascendente puede devolver el `roles` del cliente al servicio Web PubSub al controlar los eventos `connect`.

Por ejemplo, en JavaScript, puede configurar el evento `handleConnect` para hacerlo:

```js
let handler = new WebPubSubEventHandler("hub1", {
  handleConnect: (req, res) => {
    // auth the connection and set the userId of the connection
    res.success({
      roles: [ "webpubsub.sendToGroup.group1", "webpubsub.sendToGroup.group2" ]
    });
  },
});
```

#### <a name="3-assign-the-role-to-the-client-through-rest-apis-or-server-sdks-during-runtime"></a>3. Asignación del rol al cliente a través de varias API REST o SDK de servidor durante el tiempo de ejecución

```js
let service = new WebPubSubServiceClient("<your_connection_string>", "test-hub");
await service.grantPermission("<connection_id>", "joinLeaveGroup", { targetName: "group1" });
```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
