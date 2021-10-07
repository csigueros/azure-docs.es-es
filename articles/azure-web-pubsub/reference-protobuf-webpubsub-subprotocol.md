---
title: 'Referencia: subprotocolo protobuf `protobuf.webpubsub.azure.v1` de WebSocket compatible con Azure Web PubSub'
description: En la referencia se describe el subprotocolo `protobuf.webpubsub.azure.v1` de WebSocket compatible con Azure Web PubSub.
author: chenyl
ms.author: chenyl
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 045d7946a94ba9658dcdc235e1d30e36b4c4e09b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626995"
---
#  <a name="the-azure-web-pubsub-supported-protobuf-websocket-subprotocol"></a>Subprotocolo protobuf de WebSocket compatible con Azure Web PubSub
     
En este documento se describe el subprotocolo `protobuf.webpubsub.azure.v1`.

Cuando un cliente usa este subprotocolo, se espera que las tramas de datos salientes y entrantes sean cargas de búferes de protocolo (protobuf).

## <a name="overview"></a>Información general

El subprotocolo `protobuf.webpubsub.azure.v1` permite al cliente realizar una publicación/suscripción (PubSub) directamente en lugar de realizar un recorrido de ida y vuelta al servidor que precede en la cadena. La conexión de WebSocket con el subprotocolo `protobuf.webpubsub.azure.v1` se denomina cliente WebSocket de PubSub.

Por ejemplo, en JavaScript puede crear un cliente WebSocket de PubSub con el subprotocolo protobuf mediante:

```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```

En el caso de un cliente WebSocket simple, el servidor tiene el rol *necesario* de controlar los eventos de los clientes. Una conexión simple de WebSocket siempre desencadena un evento `message` cuando envía mensajes, y siempre se basa en el lado servidor para procesar mensajes y realizar otras operaciones. Con la ayuda del subprotocolo `protobuf.webpubsub.azure.v1`, un cliente autorizado puede unirse a un grupo mediante [solicitudes de unión](#join-groups) y publicar mensajes en un grupo mediante [solicitudes de publicación](#publish-messages) directamente. El cliente también puede enrutar mensajes a distintos controladores de eventos ascendentes mediante [solicitudes de eventos](#send-custom-events) para personalizar el *evento* al que pertenece el mensaje.

> [!NOTE]
> Actualmente, el servicio Web PubSub solo admite [proto3](https://developers.google.com/protocol-buffers/docs/proto3).

## <a name="permissions"></a>Permisos

En la descripción anterior del cliente WebSocket de PubSub es posible que haya observado que un cliente solo puede publicar en otros cuando está *autorizado* para hacerlo. Los roles del cliente determinan sus permisos *iniciales*, que se indican en la tabla siguiente:

| Rol | Permiso |
|---|---|
| No especificado | El cliente puede enviar solicitudes de eventos. |
| `webpubsub.joinLeaveGroup` | El cliente puede unirse a cualquier grupo o abandonarlo. |
| `webpubsub.sendToGroup` | El cliente puede publicar mensajes en cualquier grupo. |
| `webpubsub.joinLeaveGroup.<group>` | El cliente puede unirse al grupo `<group>` o abandonarlo. |
| `webpubsub.sendToGroup.<group>` | El cliente puede publicar mensajes en el grupo `<group>`. |
| | |

El lado servidor también puede conceder o revocar los permisos de un cliente de forma dinámica por medio de API REST o SDK de servidor.

## <a name="requests"></a>Requests

Todos los mensajes de solicitud siguen este formato protobuf:

```protobuf
syntax = "proto3";

import "google/protobuf/any.proto";

message UpstreamMessage {
    oneof message {
        SendToGroupMessage send_to_group_message = 1;
        EventMessage event_message = 5;
        JoinGroupMessage join_group_message = 6;
        LeaveGroupMessage leave_group_message = 7;
    }

    message SendToGroupMessage {
        string group = 1;
        optional int32 ack_id = 2;
        MessageData data = 3;
    }

    message EventMessage {
        string event = 1;
        MessageData data = 2;
    }
    
    message JoinGroupMessage {
        string group = 1;
        optional int32 ack_id = 2;
    }

    message LeaveGroupMessage {
        string group = 1;
        optional int32 ack_id = 2;
    }
}

message MessageData {
    oneof data {
        string text_data = 1;
        bytes binary_data = 2;
        google.protobuf.Any protobuf_data = 3;
    }
}
```

### <a name="join-groups"></a>Unión a grupos

Formato:

Establezca `join_group_message.group` para el nombre del grupo.

* `ackId` es opcional. Es un entero incremental para este mensaje de comando. Si se especifica `ackId`, el servicio envía un [mensaje de respuesta de confirmación](#ack-response) al cliente cuando se ejecuta el comando.

### <a name="leave-groups"></a>Salida de grupos

Formato:

Establezca `leave_group_message.group` para el nombre del grupo.

* `ackId` es opcional. Es un entero incremental para este mensaje de comando. Si se especifica `ackId`, el servicio envía un [mensaje de respuesta de confirmación](#ack-response) al cliente cuando se ejecuta el comando.

### <a name="publish-messages"></a>Publicar mensajes

Formato:

* `ackId` es opcional. Es un entero incremental para este mensaje de comando. Si se especifica `ackId`, el servicio envía un [mensaje de respuesta de confirmación](#ack-response) al cliente cuando se ejecuta el comando.

Hay un valor `dataType` implícito que puede ser `protobuf`, `text` o `binary`, en función del valor `data` de `MessageData` que se establezca. Los clientes receptores pueden usar `dataType` para controlar el contenido correctamente.

* `protobuf`: si establece `send_to_group_message.data.protobuf_data`, el valor implícito `dataType` es `protobuf`. `protobuf_data` puede ser un tipo de mensaje [Any](https://developers.google.com/protocol-buffers/docs/proto3#any). Todos los demás clientes reciben un binario codificado con protobuf que se puede deserializar mediante el SDK de protobuf. Los clientes que solo admiten contenido basado en texto (por ejemplo,`json.webpubsub.azure.v1`) reciben un binario codificado en Base64.

* `text`: si establece `send_to_group_message.data.text_data`, el valor implícito `dataType` es `text`. `text_data` debe ser una cadena. Todos los clientes con otros protocolos reciben una cadena codificada en UTF-8.

* `binary`: si establece `send_to_group_message.data.binary_data`, el valor implícito `dataType` es `binary`. `binary_data` debe ser una matriz de bytes. Todos los clientes con otros protocolos reciben un binario sin formato ni codificación protobuf. Los clientes que solo admiten contenido basado en texto (por ejemplo,`json.webpubsub.azure.v1`) reciben un binario codificado en Base64.

#### <a name="case-1-publish-text-data"></a>Caso 1: publicación de datos de texto

Establezca `send_to_group_message.group` en `group` y `send_to_group_message.data.text_data` en `"text data"`.

* El cliente de subprotocolo protobuf del grupo `group` recibe el marco binario y puede usar [DownstreamMessage](#responses) para su deserialización.

* El cliente de subprotocolo JSON del grupo `group` recibe:

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "text",
        "data" : "text data"
    }
    ```

* El cliente sin formato del grupo `group` recibe la cadena `text data`.

#### <a name="case-2-publish-protobuf-data"></a>Caso 2: publicación de datos protobuf

Imagine que tiene un mensaje del cliente:

```
message MyMessage {
    int32 value = 1;
}
```

Establezca `send_to_group_message.group` en `group` y `send_to_group_message.data.protobuf_data` en `Any.pack(MyMessage)` con `value = 1`.

* El cliente de subprotocolo protobuf del grupo `group` recibe el marco binario y puede usar [DownstreamMessage](#responses) para su deserialización.

* El cliente de subprotocolo del grupo `group` recibe:

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "G",
        "dataType" : "protobuf",
        "data" : "Ci90eXBlLmdvb2dsZWFwaXMuY29tL2F6dXJlLndlYnB1YnN1Yi5UZXN0TWVzc2FnZRICCAE=" // Base64-encoded bytes
    }
    ```

    > [!NOTE]
    > Los datos son un binario protobuf deserializable codificado en Base64. 

Puede usar la siguiente definición de protobuf y emplear `Any.unpack()` para deserializarla:

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

* El cliente sin formato del grupo `group` recibe el marco binario:

    ```
    # Show in hexadecimal
    0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
    ```

#### <a name="case-3-publish-binary-data"></a>Caso 3: publicación de datos binarios

Establezca `send_to_group_message.group` en `group` y `send_to_group_message.data.binary_data` en `[1, 2, 3]`.

* El cliente de subprotocolo protobuf del grupo `group` recibe el marco binario y puede usar [DownstreamMessage](#responses) para su deserialización.

* El cliente de subprotocolo JSON del grupo `group` recibe:

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "group",
        "dataType" : "binary",
        "data" : "AQID", // Base64-encoded [1,2,3]
    }
    ```

    Dado que el cliente de subprotocolo JSON solo admite mensajería basada en texto, el binario siempre está codificado en Base64.

* El cliente sin formato del grupo `group` recibe los datos binarios del marco binario:

    ```
    # Show in hexadecimal
    01 02 03
    ```

### <a name="send-custom-events"></a>Envío de eventos personalizados

Hay un valor `dataType` implícito que puede ser `protobuf`, `text` o `binary`, en función del valor `dataType` que se establezca. Los clientes receptores pueden usar `dataType` para controlar el contenido correctamente.

* `protobuf`: si establece `event_message.data.protobuf_data`, el valor implícito `dataType` es `protobuf`. `protobuf_data` puede ser cualquier tipo protobuf compatible. El controlador de eventos recibe el binario codificado con protobuf que cualquier SDK de protobuf puede deserializar.

* `text`: si establece `event_message.data.text_data`, el valor implícito es `text`. `text_data` debe ser una cadena. El controlador de eventos recibe una cadena codificada en UTF-8.

* `binary`: si establece `event_message.data.binary_data`, el valor implícito es `binary`. `binary_data` debe ser una matriz de bytes. El controlador de eventos recibe el marco binario sin formato.

#### <a name="case-1-send-an-event-with-text-data"></a>Caso 1: envío de un evento con datos de texto

Establezca `event_message.data.text_data` en `"text data"`.

El controlador de eventos ascendente recibe una solicitud similar a la siguiente. Observe que el valor `Content-Type` de la solicitud HTTP de CloudEvents es `text/plain`, donde `dataType`=`text`.

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: text/plain
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

text data

```

#### <a name="case-2-send-an-event-with-protobuf-data"></a>Caso 2: envío de un evento con datos protobuf

Imagine que ha recibido el siguiente mensaje del cliente:

```
message MyMessage {
    int32 value = 1;
}
```

Establezca `event_message.data.protobuf_data` en `any.pack(MyMessage)` con `value = 1`.

El controlador de eventos ascendente recibe una solicitud similar a la siguiente. Observe que el valor `Content-Type` de la solicitud HTTP de CloudEvents es `application/x-protobuf`, donde `dataType`=`protobuf`.

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

// Just show in hexadecimal; read it as binary
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

Los datos son un archivo binario protobuf válido. Puede usar el siguiente valor `proto` y `any.unpack()` para deserializarlo:

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

#### <a name="case-3-send-an-event-with-binary-data"></a>Caso 3: envío de un evento con datos binarios

Establezca `send_to_group_message.binary_data` en `[1, 2, 3]`.

El controlador de eventos ascendente recibe una solicitud similar a la siguiente. En `dataType`=`binary`, el valor `Content-Type` de la solicitud HTTP de CloudEvents es `application/octet-stream`. 

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.<event_name>
ce-source: /client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub_name}
ce-eventName: <event_name>

// Just show in hexadecimal; you need to read it as binary
01 02 03 
```

El marco de WebSocket puede tener formato `text` para marcos de mensajes de texto o binarios con codificación UTF8 para marcos de mensajes `binary`.

El servicio rechaza al cliente si el mensaje no coincide con el formato prescrito.

## <a name="responses"></a>Respuestas

Todos los mensajes de respuesta siguen este formato protobuf:

```protobuf
message DownstreamMessage {
    oneof message {
        AckMessage ack_message = 1;
        DataMessage data_message = 2;
        SystemMessage system_message = 3;
    }
    
    message AckMessage {
        int32 ack_id = 1;
        bool success = 2;
        optional ErrorMessage error = 3;
    
        message ErrorMessage {
            string name = 1;
            string message = 2;
        }
    }

    message DataMessage {
        string from = 1;
        optional string group = 2;
        MessageData data = 3;
    }

    message SystemMessage {
        oneof message {
            ConnectedMessage connected_message = 1;
            DisconnectedMessage disconnected_message = 2;
        }
    
        message ConnectedMessage {
            string connection_id = 1;
            string user_id = 2;
        }

        message DisconnectedMessage {
            string reason = 2;
        }
    }
}
```

Los mensajes recibidos por el cliente pueden ser de cualquiera de estos tres tipos: `ack`, `message` o `system`. 

### <a name="ack-response"></a>Respuesta de confirmación

Si la solicitud contiene `ackId`, el servicio devuelve una respuesta de confirmación para esta solicitud. La implementación del cliente debe controlar este mecanismo de confirmación, lo que incluye:
* Espera de la respuesta de confirmación de una operación `async` `await`. 
* Comprobación de tiempo de espera si no se recibe la respuesta de confirmación durante un período determinado.

La implementación del cliente siempre debe comprobar primero si el estado `success` es `true` o `false`. Si el estado `success` es `false`, el cliente puede leer en la propiedad `error` para obtener detalles del error.

### <a name="message-response"></a>Respuesta del mensaje

Los clientes pueden recibir mensajes publicados de un grupo al que se ha unido el cliente. También pueden recibir mensajes del rol de administración del servidor cuando el servidor envía mensajes a un cliente o un usuario determinado.

Siempre se recibe un mensaje `DownstreamMessage.DataMessage` en los escenarios siguientes:

- Cuando el mensaje es de un grupo, `from` es `group`. Cuando el mensaje es del servidor, `from` es `server`.
- Cuando el mensaje es de un grupo, `group` es el nombre del grupo.

El valor `dataType` del remitente hace que se envíe uno de los mensajes siguientes: 
* Si `dataType` es `text`, use `message_response_message.data.text_data`. 
* Si `dataType` es `binary`, use `message_response_message.data.binary_data`. 
* Si `dataType` es `protobuf`, use `message_response_message.data.protobuf_data`. 
* Si `dataType` es `json`, use `message_response_message.data.text_data`, y el contenido es una cadena JSON serializada.

### <a name="system-response"></a>Respuesta del sistema

El servicio Web PubSub también puede enviar respuestas relacionadas con el sistema al cliente. 

#### <a name="connected"></a>Conectado

Cuando el cliente se conecta al servicio, recibe un mensaje `DownstreamMessage.SystemMessage.ConnectedMessage`.

#### <a name="disconnected"></a>Escenario desconectado

Cuando el servidor cierra la conexión o el servicio rechaza al cliente, recibe un mensaje `DownstreamMessage.SystemMessage.DisconnectedMessage`.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
