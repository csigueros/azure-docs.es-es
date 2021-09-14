---
title: 'Referencia: subprotocolo `protobuf.webpubsub.azure.v1` de WebSocket compatible con Azure Web PubSub'
description: En la referencia se describe el subprotocolo `protobuf.webpubsub.azure.v1` de WebSocket compatible con Azure Web PubSub.
author: chenyl
ms.author: chenyl
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 9b54f219f52c9fa19134841488fcdf7f2bbc844d
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440125"
---
#  <a name="azure-web-pubsub-supported-protobuf-websocket-subprotocol"></a>Subprotocolo de WebSocket de Protobuf compatible con Azure Web PubSub
     
En este documento se describe el subprotocolo `protobuf.webpubsub.azure.v1`.

Cuando el cliente usa este subprotocolo, se espera que tanto la trama de datos saliente como la trama de datos entrante sean cargas **protobuf**.

## <a name="overview"></a>Información general

El subprotocolo `protobuf.webpubsub.azure.v1` permite a los clientes publicar o suscribirse directamente en lugar de realizar un recorrido de ida y vuelta al servidor ascendente. Llamamos a la conexión de WebSocket con el subprotocolo `protobuf.webpubsub.azure.v1` un cliente WebSocket de PubSub.

Por ejemplo, en JS, se puede crear un cliente WebSocket de PubSub con el subprotocolo Protobuf del modo siguiente:
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'protobuf.webpubsub.azure.v1');
```
Para un cliente WebSocket simple, el *servidor* es un rol IMPRESCINDIBLE para controlar los eventos de los clientes. Una conexión simple de WebSocket siempre desencadena un evento `message` cuando envía mensajes y siempre se basa en el lado servidor para procesar mensajes y realizar otras operaciones. Con la ayuda del subprotocolo `protobuf.webpubsub.azure.v1`, un cliente autorizado puede unirse a un grupo mediante [solicitudes de unión](#join-groups) y publicar mensajes en un grupo mediante [solicitudes de publicación](#publish-messages) directamente. También puede enrutar mensajes a distintos canales de subida (controladores de eventos) personalizando el *evento* al que pertenece el mensaje mediante [solicitudes de evento](#send-custom-events).

> [!NOTE]
> Actualmente, el servicio WebPubSub solo admite [proto3](https://developers.google.com/protocol-buffers/docs/proto3).

## <a name="permissions"></a>Permisos

Es posible que haya observado que, cuando se describen los clientes WebSocket de PubSub, un cliente solo puede publicar en otros clientes cuando está *autorizado*. Los valores de `role` del cliente determinan los permisos *iniciales* que tiene el cliente:

| Rol | Permiso |
|---|---|
| No especificado | El cliente puede enviar solicitudes de eventos.
| `webpubsub.joinLeaveGroup` | El cliente puede unirse a cualquier grupo o salir de este.
| `webpubsub.sendToGroup` | El cliente puede publicar mensajes en cualquier grupo.
| `webpubsub.joinLeaveGroup.<group>` | El cliente puede unirse al grupo `<group>` o salir de este.
| `webpubsub.sendToGroup.<group>` | El cliente puede publicar mensajes en el grupo `<group>`.

El lado servidor también puede conceder o revocar permisos del cliente dinámicamente a través de API REST o SDK de servidor.

## <a name="requests"></a>Requests

Todos los mensajes de solicitud utilizan el siguiente formato protobuf.

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

* `ackId` es opcional, es un entero incremental para este mensaje de comando. Cuando se especifica `ackId`, el servicio devuelve un [mensaje de respuesta confirmación](#ack-response) al cliente cuando se ejecuta el comando.

### <a name="leave-groups"></a>Salida de grupos

Formato:

Establezca `leave_group_message.group` para el nombre del grupo.

* `ackId` es opcional, es un entero incremental para este mensaje de comando. Cuando se especifica `ackId`, el servicio devuelve un [mensaje de respuesta confirmación](#ack-response) al cliente cuando se ejecuta el comando.

### <a name="publish-messages"></a>Publicar mensajes

Formato:

* `ackId` es opcional, es un entero incremental para este mensaje de comando. Cuando se especifica `ackId`, el servicio devuelve un [mensaje de respuesta confirmación](#ack-response) al cliente cuando se ejecuta el comando.

Hay un valor `dataType` implícito que puede ser `protobuf`, `text` o `binary`, en función del valor de `data` que establezca en `MessageData`. Los clientes receptores pueden aprovechar `dataType` para usar el contenido correctamente.

* `protobuf`: si establece `send_to_group_message.data.protobuf_data`, el valor implícito es `protobuf`. `protobuf_data` puede ser de [cualquier](https://developers.google.com/protocol-buffers/docs/proto3#any) tipo. Todos los demás clientes recibirán un archivo binario codificado con protobuf que se puede deserializar mediante el SDK de protobuf. En los clientes que solo admiten contenido basado en texto (por ejemplo, `json.webpubsub.azure.v1`), se recibirá un archivo binario codificado en Base64.

* `text`: si establece `send_to_group_message.data.text_data`, el valor implícito es `text`. `text_data` debe ser una cadena. Todos los clientes con otro protocolo recibirán una cadena codificada en UTF-8.

* `binary`: si establece `send_to_group_message.data.binary_data`, el valor implícito es `binary`. `binary_data` debe ser una matriz de bytes. Todos los clientes con otro protocolo recibirán un valor binario sin formato ni codificación protobuf. En los clientes que solo admiten contenido basado en texto (por ejemplo, `json.webpubsub.azure.v1`), se recibirá un archivo binario codificado en Base64.

#### <a name="case-1-publish-text-data"></a>Caso 1: publicar datos de texto:

Establezca `send_to_group_message.group` en `group` y `send_to_group_message.data.text_data` en `"text data"`.

* Lo que el cliente de subprotocolo protobuf de este grupo `group` recibe es el marco binario y puede usar [DownstreamMessage](#responses) para la deserialización.

* El contenido que recibe el cliente de subprotocolo de este grupo `group`:

```json
{
    "type": "message",
    "from": "group",
    "group": "group",
    "dataType" : "text",
    "data" : "text data"
}
```

* Lo que recibe el cliente sin procesar del grupo `group` son los datos de cadena `text data`.

#### <a name="case-2-publish-protobuf-data"></a>Caso 2: publicar datos de protobuf:

Suponga que tiene un mensaje del cliente:

```
message MyMessage {
    int32 value = 1;
}
```

Establezca `send_to_group_message.group` en `group` y `send_to_group_message.data.protobuf_data` en `Any.pack(MyMessage)` con `value = 1`.

* Lo que el cliente de subprotocolo protobuf de este grupo `group` recibe es el marco binario y puede usar [DownstreamMessage](#responses) para la deserialización.

* Lo que recibe el cliente de subprotocolo del grupo `group`:

```json
{
    "type": "message",
    "from": "group",
    "group": "G",
    "dataType" : "protobuf",
    "data" : "Ci90eXBlLmdvb2dsZWFwaXMuY29tL2F6dXJlLndlYnB1YnN1Yi5UZXN0TWVzc2FnZRICCAE=" // Base64 encoded bytes
}
```

Tenga en cuenta que los datos son un archivo binario protobuf deserializable codificado en Base64. Puede usar la siguiente definición de protobuf y emplear `Any.unpack()` para deserializarla:

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

* Lo que el cliente sin procesar de este grupo `group` recibe es el marco binario.

```
# Show in Hex
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

#### <a name="case-3-publish-binary-data"></a>Caso 3: publicar datos binarios:

Establezca `send_to_group_message.group` en `group` y `send_to_group_message.data.binary_data` en `[1, 2, 3]`.

* Lo que el cliente de subprotocolo protobuf de este grupo `group` recibe es el marco binario y puede usar [DownstreamMessage](#responses) para la deserialización.

* El contenido que recibe el cliente de subprotocolo de este grupo `group`:

```json
{
    "type": "message",
    "from": "group",
    "group": "group",
    "dataType" : "binary",
    "data" : "AQID", // Base64 encoded [1,2,3]
}
```

Como el cliente de subprotocolo json solo admite mensajes de texto, el archivo binario siempre se codifica como base64.

* Lo que recibe el cliente sin procesar del grupo `group` son los datos **binarios** del marco binario.

```
# Show in Hex
01 02 03
```

### <a name="send-custom-events"></a>Envío de eventos personalizados

Hay un valor `dataType` implícito que puede ser `protobuf`, `text` o `binary`, en función del valor dataType que establezca. Los clientes receptores pueden aprovechar `dataType` para usar el contenido correctamente.

* `protobuf`: si establece `event_message.data.protobuf_data`, el valor implícito es `protobuf`. `protobuf_data` puede ser cualquier tipo protobuf compatible. El controlador de eventos recibirá un archivo binario codificado con protobuf que cualquier SDK de protobuf puede deserializar.

* `text`: si establece `event_message.data.text_data`, el valor implícito es `text`. `text_data` debe ser una cadena. El controlador de eventos recibirá una cadena codificada en UTF-8.

* `binary`: si establece `event_message.data.binary_data`, el valor implícito es `binary`. `binary_data` debe ser una matriz de bytes. El controlador de eventos recibirá el marco binario sin formato.

#### <a name="case-1-send-event-with-text-data"></a>Caso 1: envío de eventos con datos de texto:

Establezca `event_message.data.text_data` en `"text data"`.

Lo que recibe el controlador de eventos ascendente, como se muestra a continuación, `Content-Type` para la solicitud HTTP de CloudEvents es `text/plain` para `dataType`=`text`.

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

#### <a name="case-2-send-event-with-protobuf-data"></a>Caso 2: enviar evento con datos protobuf:

Suponga que tiene un mensaje del cliente:

```
message MyMessage {
    int32 value = 1;
}
```

Establezca `event_message.data.protobuf_data` en `any.pack(MyMessage)` con `value = 1`.

Lo que recibe el controlador de eventos ascendente, como se muestra a continuación. Tenga en cuenta que `Content-Type` para la solicitud HTTP de CloudEvents es `application/x-protobuf` para `dataType`=`protobuf`.

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

// Just show in hex, you need to read as binary
0A 2F 74 79 70 65 2E 67 6F 6F 67 6C 65 61 70 69 73 2E 63 6F 6D 2F 61 7A 75 72 65 2E 77 65 62 70 75 62 73 75 62 2E 54 65 73 74 4D 65 73 73 61 67 65 12 02 08 01
```

Los datos son un archivo binario protobuf válido. Puede usar el siguiente valor `proto` y `any.unpack()` para deserializarlo:

```protobuf
syntax = "proto3";

message MyMessage {
    int32 value = 1;
}
```

#### <a name="case-3-send-event-with-binary-data"></a>Caso 3: envío de eventos con datos binarios:

Establezca `send_to_group_message.binary_data` en `[1, 2, 3]`.

Lo que recibe el controlador de eventos ascendente, como se muestra a continuación, `Content-Type` para la solicitud HTTP de CloudEvents es `application/octet-stream` para `dataType`=`binary`.

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

// Just show in hex, you need to read as binary
01 02 03 
```

El marco de WebSocket puede tener formato `text` para marcos de mensajes de texto o binarios con codificación UTF8 para marcos de mensaje `binary`.

El servicio rechaza el cliente si el mensaje no coincide con el formato descrito.

## <a name="responses"></a>Respuestas

Todos los mensajes de respuesta siguen el siguiente formato protobuf:

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

Los mensajes recibidos por el cliente pueden ser de varios tipos: `ack`, `message` y `system`: 

### <a name="ack-response"></a>Respuesta de confirmación

Si la solicitud contiene `ackId`, el servicio devolverá una respuesta rápida para esta solicitud. La implementación del cliente debe controlar este mecanismo de confirmación, incluida la espera de la respuesta de confirmación para una operación `async` `await` y la realización de una comprobación de tiempo de espera cuando no se recibe la respuesta de confirmación durante un período determinado.

La implementación del cliente siempre DEBE comprobar en primer lugar si `success` es `true` o `false`. Solo cuando `success` es `false`, el cliente lee desde `error`.

### <a name="message-response"></a>Respuesta del mensaje

Los clientes pueden recibir mensajes publicados de un grupo al que se ha unido el cliente o del rol de administración del servidor desde el que el servidor envía mensajes al cliente o al usuario específicos.

Siempre recibirá: `DownstreamMessage.DataMessage`

- Cuando el mensaje sea de un grupo, `from` será `group`. Cuando el mensaje es sea del servidor. `from` será `server`.

- Cuando el mensaje sea de un grupo, `group` será el nombre del grupo.

- El valor `dateType` del remitente provocará que se establezca en uno de los mensajes. Si `dateType` es `text`, debería usar `message_response_message.data.text_data`. Si `dateType` es `binary`, debería usar `message_response_message.data.binary_data`. Si `dateType` es `protobuf`, debería usar `message_response_message.data.protobuf_data`. Si `dateType` es `json`, debería usar `message_response_message.data.text_data`, y el contenido es una cadena json serializada.

### <a name="system-response"></a>Respuesta del sistema

El servicio Web PubSub también puede enviar respuestas relacionadas con el sistema al cliente. 

#### <a name="connected"></a>Conectado

Cuando la conexión se conecta al servicio. Recibirá un valor `DownstreamMessage.SystemMessage.ConnectedMessage`.

#### <a name="disconnected"></a>Escenario desconectado

Cuando el servidor cierra la conexión o cuando el servicio rechaza el cliente. Recibirá un valor `DownstreamMessage.SystemMessage.DisconnectedMessage`.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]