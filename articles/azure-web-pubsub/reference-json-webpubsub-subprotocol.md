---
title: 'Referencia: subprotocolo `json.webpubsub.azure.v1` de WebSocket JSON compatible con Azure Web PubSub'
description: En la referencia se describe el subprotocolo `json.webpubsub.azure.v1` de WebSocket compatible con Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 724ffa23cf533133603db717b4d01ebbd81894a1
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446723"
---
#  <a name="azure-web-pubsub-supported-json-websocket-subprotocol"></a>Subprotocolo de WebSocket JSON compatible con Azure Web PubSub
     
En este documento se describe el subprotocolo `json.webpubsub.azure.v1`.

Cuando el cliente usa este subprotocolo, se espera que tanto la trama de datos saliente como la trama de datos entrante sean cargas **JSON**.

## <a name="overview"></a>Información general

El subprotocolo `json.webpubsub.azure.v1` permite a los clientes publicar o suscribirse directamente en lugar de realizar un recorrido de ida y vuelta al servidor ascendente. Llamamos a la conexión de WebSocket con el subprotocolo `json.webpubsub.azure.v1` un cliente WebSocket de PubSub.

Por ejemplo, en JS, se puede crear un cliente WebSocket de PubSub mediante:
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```
Para un cliente WebSocket simple, el *servidor* es un rol IMPRESCINDIBLE para controlar los eventos de los clientes. Una conexión simple de WebSocket siempre desencadena un evento `message` cuando envía mensajes y siempre se basa en el lado servidor para procesar mensajes y realizar otras operaciones. Con la ayuda del subprotocolo `json.webpubsub.azure.v1`, un cliente autorizado puede unirse a un grupo mediante [solicitudes de unión](#join-groups) y publicar mensajes en un grupo mediante [solicitudes de publicación](#publish-messages) directamente. También puede enrutar mensajes a distintos canales de subida (controladores de eventos) personalizando el *evento* al que pertenece el mensaje mediante [solicitudes de evento](#send-custom-events).

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

### <a name="join-groups"></a>Unión a grupos

Formato:

```json
{
    "type": "joinGroup",
    "group": "<group_name>",
    "ackId" : 1 // optional
}
```

* `ackId` es opcional, es un entero incremental para este mensaje de comando. Cuando se especifica `ackId`, el servicio devuelve un [mensaje de respuesta confirmación](#ack-response) al cliente cuando se ejecuta el comando.

### <a name="leave-groups"></a>Salida de grupos

Formato:

```json
{
    "type": "leaveGroup",
    "group": "<group_name>",
    "ackId" : 1 // optional
}
```

* `ackId` es opcional, es un entero incremental para este mensaje de comando. Cuando se especifica `ackId`, el servicio devuelve un [mensaje de respuesta confirmación](#ack-response) al cliente cuando se ejecuta el comando.

### <a name="publish-messages"></a>Publicar mensajes

Formato:

```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "ackId" : 1, // optional
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

* `ackId` es opcional, es un entero incremental para este mensaje de comando. Cuando se especifica `ackId`, el servicio devuelve un [mensaje de respuesta confirmación](#ack-response) al cliente cuando se ejecuta el comando.

`dataType` puede ser de tipo `json`, `text` o `binary`:
* `json`: `data` puede ser cualquier tipo que JSON admita y se publicará como lo que sea. Si `dataType` no se especifica, el valor predeterminado es `json`.
* `text`: `data` debe estar en formato de cadena y se publicarán los datos de cadena.
* `binary`: `data` debe estar en formato base64 y se publicarán los datos binarios.

#### <a name="case-1-publish-text-data"></a>Caso 1: publicar datos de texto:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "text",
    "data": "text data" 
}
```

* Lo que recibe el cliente de subprotocolo del grupo `<group_name>`:
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "text",
    "data" : "text data"
}
```
* Lo que recibe el cliente sin procesar del grupo `<group_name>` son los datos de cadena `text data`.

#### <a name="case-2-publish-json-data"></a>Caso 2: publicar datos JSON:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "json",
    "data": {
        "hello": "world"
    }
}
```

* Lo que recibe el cliente de subprotocolo del grupo `<group_name>`:
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "json",
    "data" : {
        "hello": "world"
    }
}
```
* Lo que recibe el cliente sin procesar del grupo `<group_name>` son los datos de cadena serializados `{"hello": "world"}`.


#### <a name="case-3-publish-binary-data"></a>Caso 3: publicar datos binarios:
```json
{
    "type": "sendToGroup",
    "group": "<group_name>",
    "dataType" : "binary",
    "data": "<base64_binary>"
}
```

* Lo que recibe el cliente de subprotocolo del grupo `<group_name>`:
```json
{
    "type": "message",
    "from": "group",
    "group": "<group_name>",
    "dataType" : "binary",
    "data" : "<base64_binary>", 
}
```
* Lo que recibe el cliente sin procesar del grupo `<group_name>` son los datos **binarios** del marco binario.

### <a name="send-custom-events"></a>Envío de eventos personalizados

Formato:

```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "json|text|binary",
    "data": {}, // data can be string or valid json token depending on the dataType 
}
```

`dataType` puede ser de tipo `text`, `binary` o `json`:
* `json`: los datos pueden ser cualquier tipo que JSON admita y se publicará como lo que sea. Si `dataType` no se especifica, el valor predeterminado es `json`.
* `text`: los datos deben estar en formato de cadena y se publicarán los datos de cadena.
* `binary`: los datos deben estar en formato base64 y se publicarán los datos binarios.

#### <a name="case-1-send-event-with-text-data"></a>Caso 1: envío de eventos con datos de texto:
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "text",
    "data": "text data", 
}
```

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

#### <a name="case-2-send-event-with-json-data"></a>Caso 2: envío de eventos con datos JSON:
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "json",
    "data": {
        "hello": "world"
    }, 
}
```

Lo que recibe el controlador de eventos ascendente, como se muestra a continuación, `Content-Type` para la solicitud HTTP de CloudEvents es `application/json` para `dataType`=`json`.

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

{
    "hello": "world"
}

```

#### <a name="case-3-send-event-with-binary-data"></a>Caso 3: envío de eventos con datos binarios:
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "binary",
    "data": "base64_binary", 
}
```

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

binary

```

El marco de WebSocket puede tener formato `text` para marcos de mensajes de texto o binarios con codificación UTF8 para marcos de mensaje `binary`.

El servicio rechaza el cliente si el mensaje no coincide con el formato descrito.

## <a name="responses"></a>Respuestas

Los mensajes recibidos por el cliente pueden ser de varios tipos: `ack`, `message` y `system`: 

### <a name="ack-response"></a>Respuesta de confirmación

Si la solicitud contiene `ackId`, el servicio devolverá una respuesta rápida para esta solicitud. La implementación del cliente debe controlar este mecanismo de confirmación, incluida la espera de la respuesta de confirmación para una operación `async` `await` y la realización de una comprobación de tiempo de espera cuando no se recibe la respuesta de confirmación durante un período determinado.

Formato:
```json
{
    "type": "ack",
    "ackId": 1, // The ack id for the request to ack
    "success": false, // true or false
    "error": {
        "name": "NotFound|Forbidden|Timeout|InternalServerError",
        "message": "<error_detail>"
    }
}
```

La implementación del cliente siempre DEBE comprobar en primer lugar si `success` es `true` o `false`. Solo cuando `success` es `false`, el cliente lee desde `error`.

### <a name="message-response"></a>Respuesta del mensaje

Los clientes pueden recibir mensajes publicados de un grupo al que se ha unido el cliente o del rol de administración del servidor desde el que el servidor envía mensajes al cliente o al usuario específicos.

1. Cuando el mensaje es de un grupo.

    ```json
    {
        "type": "message",
        "from": "group",
        "group": "<group_name>",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
    }
    ```

1. Cuando el mensaje es del servidor.

    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "json|text|binary",
        "data" : {} // The data format is based on the dataType
    }
    ```

#### <a name="case-1-sending-data-hello-world-to-the-connection-through-rest-api-with-content-typetextplain"></a>Caso 1: envío de datos `Hello World` a la conexión a través de la API REST con `Content-Type`=`text/plain` 
* Lo que recibe un cliente WebSocket simple es un marco WebSocket de texto con datos: `Hello World`;
* Lo que recibe un cliente WebSocket de PubSub se indica a continuación:
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "text",
        "data": "Hello World", 
    }
    ```

#### <a name="case-2-sending-data--hello--world-to-the-connection-through-rest-api-with-content-typeapplicationjson"></a>Caso 2: envío de datos `{ "Hello" : "World"}` a la conexión a través de la API REST con `Content-Type`=`application/json`
* Lo que recibe un cliente WebSocket simple es un marco WebSocket de texto con datos en cadena: `{ "Hello" : "World"}`;
* Lo que recibe un cliente WebSocket de PubSub se indica a continuación:
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "json",
        "data": {
            "Hello": "World"
        }
    }
    ```

Si la API REST envía una cadena `Hello World` mediante el tipo de contenido `application/json`, lo que recibe el cliente WebSocket simple es una cadena JSON, que encapsula la cadena `"Hello World"` con `"`.

#### <a name="case-3-sending-binary-data-to-the-connection-through-rest-api-with-content-typeapplicationoctet-stream"></a>Caso 3: envío de datos binarios a la conexión a través de la API REST con `Content-Type`=`application/octet-stream`
* Lo que recibe un cliente WebSocket simple es un marco de WebSocket binario con los datos binarios.
* Lo que recibe un cliente WebSocket de PubSub se indica a continuación:
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType" : "binary",
        "data": "<base64_binary>"
    }
    ```

### <a name="system-response"></a>Respuesta del sistema

El servicio Web PubSub también puede enviar respuestas relacionadas con el sistema al cliente. 

#### <a name="connected"></a>Conectado

Cuando la conexión se conecta al servicio.

```json
{
    "type": "system",
    "event": "connected",
    "userId": "user1",
    "connectionId": "abcdefghijklmnop",
}
```

#### <a name="disconnected"></a>Escenario desconectado

Cuando el servidor cierra la conexión o cuando el servicio rechaza el cliente.

```json
{
    "type": "system",
    "event": "disconnected",
    "message": "reason"
}
```

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]