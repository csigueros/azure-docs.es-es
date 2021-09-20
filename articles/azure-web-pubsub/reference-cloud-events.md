---
title: 'Referencia: extensión CloudEvents para Azure Web PubSub'
description: En la referencia se describe la extensión CloudEvents definida para el servicio Azure Web PubSub.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 6503433f164e0b8153aa8832473fd06ad3959bae
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434869"
---
#  <a name="cloudevents-extension-for-azure-web-pubsub"></a>Extensión CloudEvents para Azure Web PubSub

El servicio entrega eventos de cliente al webhook ascendente mediante el [protocolo HTTP CloudEvents](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md).

Los datos que se envían desde el servicio al servidor siempre están en formato `binary` de CloudEvents.

- [Validación del webhook](#protection)
- [Extensión de atributo CloudEvents de Web PubSub](#extension)
- [Eventos](#events)
    - Eventos de bloqueo
        - [Evento `connect` del sistema](#connect)
        - [Eventos de usuario](#message)
    - Eventos de desbloqueo
        - [Evento `connected` del sistema](#connected)
        - [Evento `disconnected` del sistema](#disconnected)

## <a name="webhook-validation"></a>Validación del webhook

<a name="protection"></a>

La validación del webhook sigue a [CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). La solicitud siempre contiene `WebHook-Request-Origin: xxx.webpubsub.azure.com` en el encabezado.

Solo si el destino de entrega permite la entrega de los eventos, DEBE responder a la solicitud incluyendo el encabezado `WebHook-Allowed-Origin`, por ejemplo:

`WebHook-Allowed-Origin: *`

O:

`WebHook-Allowed-Origin: xxx.webpubsub.azure.com`

Por ahora, no se admiten [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) ni [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback).


## <a name="web-pubsub-cloudevents-attribute-extension"></a>Extensión de atributo CloudEvents de Web PubSub
<a name="extension"></a>

> También se indicó que la especificación HTTP ahora sigue un patrón similar al no sugerir que los encabezados HTTP de extensión tengan como prefijo X-.

Esta extensión define los atributos que usa Web PubSub para cada evento que genera.

### <a name="attributes"></a>Atributos

| Nombre | Tipo | Descripción | Ejemplo|
|--|--|--|--|
| `userId` | `string` | Usuario autenticado por la conexión. | |
| `hub` | `string` | Centro al que pertenece la conexión. | |
| `connectionId` | `string` | connectionId es único para la conexión de cliente. | |
| `eventName` | `string` | Nombre del evento sin prefijo. | |
| `subprotocol` | `string` | Subprotocolo que usa el cliente, si existe. | |
| `connectionState` | `string` | Define el estado de la conexión. Puede usar el mismo encabezado de respuesta para restablecer el valor del estado. No se permiten varios encabezados `connectionState`. Codifica el valor de cadena en Base64 si contiene caracteres complejos dentro, por ejemplo, puede usar `base64(jsonString)` para pasar un objeto complejo mediante este atributo.| |
| `signature` | `string` | Firma del webhook ascendente que se va a validar si la solicitud entrante es del origen esperado. El servicio calcula el valor mediante la clave de acceso principal y la clave de acceso secundaria como clave HMAC: `Hex_encoded(HMAC_SHA256(accessKey, connectionId))`. El webhook ascendente debe comprobar si la solicitud es válida antes de procesarla. | |

## <a name="events"></a>Events

Hay dos tipos de eventos, uno *bloquea* los eventos en los que el servicio espera la respuesta del evento para continuar. Otro *desbloquea* los eventos en los que el servicio no espera la respuesta antes de procesar el mensaje siguiente.

- Eventos de bloqueo
    - [Evento `connect` del sistema](#connect)
    - [Eventos de usuario](#message)
- Eventos de desbloqueo
    - [Evento `connected` del sistema](#connected)
    - [Evento `disconnected` del sistema](#disconnected)

### <a name="system-connect-event"></a>Evento `connect` del sistema
<a name="connect"></a>

* `ce-type`: `azure.webpubsub.sys.connect`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>Formato de solicitud:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connect
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect

{
    "claims": {},
    "query": {},
    "headers": {},
    "subprotocols": [],
    "clientCertificates": [
        {
            "thumbprint": "ABC"
        }
    ]
}

```

#### <a name="success-response-format"></a>Formato de respuesta correcto:
* Código de estado:
    * `204`: correcto, sin contenido.
    * `200`: correcto, el contenido DEBE ser un formato JSON, con las siguientes propiedades permitidas:
* Encabezado `ce-connectionState`: si este encabezado existe, el estado de esta conexión se actualizará al valor del encabezado. Tenga en cuenta que solo los eventos de *bloqueo* pueden actualizar el estado de conexión. En el ejemplo siguiente se usa una cadena JSON codificada en Base64 para almacenar el estado complejo de la conexión.
* 
```HTTP
HTTP/1.1 200 OK
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "groups": [],
    "userId": "",
    "roles": [],
    "subprotocol": ""
}

```

* `subprotocols`

    El evento `connect` reenvía el subprotocolo y la información de autenticación a Upstream desde el cliente. Azure SignalR Service usa el código de estado para determinar si la solicitud se actualizará al protocolo de WebSocket.

    Si la solicitud contiene la propiedad `subprotocols`, el servidor debe devolver un subprotocolo que admita. Si el servidor no quiere usar ningún subprotocolo, **no** debe enviar la propiedad `subprotocol` en la respuesta. [El envío de un encabezado en blanco no es válido](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_servers#Subprotocols).

* `userId`: `{auth-ed user ID}`

    Como el servicio permite conexiones anónimas, es responsabilidad del evento `connect` que se le informe al servicio el identificador de usuario de la conexión de cliente. El servicio leerá el identificador de usuario de la carga de respuesta `userId`, si existe. La conexión se anula si el identificador de usuario no se puede leer desde las notificaciones de solicitud ni desde la carga de respuesta del evento `connect`.

<a name="connect_response_header_group"></a>
 
* `groups`: `{groups to join}`

    La propiedad proporciona una manera cómoda para que el usuario agregue esta conexión a uno o varios grupos. De este modo, no es necesario tener otra llamada para agregar esta conexión a algún grupo.

* `roles`: `{roles the client has}`
    
    La propiedad proporciona una manera de que el canal de subida autorice al cliente. Los distintos roles definen distintos permisos iniciales que tiene el cliente; puede ser útil cuando el cliente es un cliente de PubSub WebSocket. Los detalles sobre los permisos se describen en [Permisos de cliente](./concept-client-protocols.md#permissions).

#### <a name="error-response-format"></a>Formato de respuestas de error:

* `4xx`: error, la respuesta de Upstream se devolverá como respuesta para la solicitud de cliente.

```HTTP
HTTP/1.1 401 Unauthorized
```

### <a name="system-connected-event"></a>Evento `connected` del sistema
<a name="connected"></a> El servicio llama a Upstream cuando el cliente completa el protocolo de enlace WebSocket y se conecta correctamente.

* `ce-type`: `azure.webpubsub.sys.connected`
* `Content-Type`: `application/json`
* `ce-connectionState`: `eyJrZXkiOiJhIn0=`

El cuerpo de la solicitud es un JSON vacío.

#### <a name="request-format"></a>Formato de solicitud:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.connected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: connect
ce-subprotocol: abc
ce-connectionState: eyJrZXkiOiJhIn0=

{}

```

#### <a name="response-format"></a>Formato de respuesta:

`2xx`: respuesta correcta.

`connected` es un evento asincrónico: cuando el código de estado de respuesta no es correcto, el servicio registra un error.

```HTTP
HTTP/1.1 200 OK
```


### <a name="system-disconnected-event"></a>Evento `disconnected` del sistema
El evento <a name="disconnected"></a>
`disconnected` **siempre** se desencadena cuando se completa la solicitud de cliente si el evento de **conexión** devuelve el código de estado `2xx`.

* `ce-type`: `azure.webpubsub.sys.disconnected`
* `Content-Type`: `application/json`

#### <a name="request-format"></a>Formato de solicitud:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/json; charset=utf-8
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.sys.disconnected
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: disconnect
ce-subprotocol: abc
ce-connectionState: eyJrZXkiOiJhIn0=

{
    "reason": "{Reason}"
}

```

* `reason`

    `reason` describe el motivo por el que el cliente se desconecta.


#### <a name="response-format"></a>Formato de respuesta:

`2xx`: respuesta correcta.

`disconnected` es un evento asincrónico: cuando el código de estado de respuesta no es correcto, el servicio registra un error.

```HTTP
HTTP/1.1 200 OK
```


### <a name="user-event-message-for-the-simple-websocket-clients"></a>Evento de usuario `message` para los clientes sencillos de WebSocket
<a name="message"></a> El servicio invoca el canal de subida del controlador de eventos para cada fotograma de mensaje de WebSocket.

* `ce-type`: `azure.webpubsub.user.message`
* `Content-Type`: `application/octet-stream` para marco binario; `text/plain` para marco de texto; 

UserPayload es lo que envía el cliente.

#### <a name="request-format"></a>Formato de solicitud:

```HTTP
POST /upstream HTTP/1.1
Host: xxxxxx
WebHook-Request-Origin: xxx.webpubsub.azure.com
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn
ce-specversion: 1.0
ce-type: azure.webpubsub.user.message
ce-source: /hubs/{hub}/client/{connectionId}
ce-id: {eventId}
ce-time: 2021-01-01T00:00:00Z
ce-signature: sha256={connection-id-hash-primary},sha256={connection-id-hash-secondary}
ce-userId: {userId}
ce-connectionId: {connectionId}
ce-hub: {hub}
ce-eventName: message
ce-connectionState: eyJrZXkiOiJhIn0=

UserPayload

```

#### <a name="success-response-format"></a>Formato de respuesta correcto

* status code
    * `204`: correcto, sin contenido.
    * `200`: correcto, el formato de `UserResponsePayload` depende del de la respuesta `Content-Type`.
* `Content-Type`: `application/octet-stream` para marco binario; `text/plain` para marco de texto; 
* Encabezado `Content-Type`: `application/octet-stream` para marco binario; `text/plain` para marco de texto; 
* Encabezado `ce-connectionState`: si este encabezado existe, el estado de esta conexión se actualizará al valor del encabezado. Tenga en cuenta que solo los eventos de *bloqueo* pueden actualizar el estado de conexión. En el ejemplo siguiente se usa una cadena JSON codificada en Base64 para almacenar el estado complejo de la conexión.

Cuando `Content-Type` es `application/octet-stream`, el servicio envía `UserResponsePayload` al cliente mediante el marco `binary` de WebSocket. Cuando `Content-Type` es `text/plain`, el servicio envía `UserResponsePayload` al cliente mediante el marco `text` de WebSocket. 

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream (for binary frame) or text/plain (for text frame)
Content-Length: nnnn
ce-connectionState: eyJrZXkiOiJhIn0=

UserResponsePayload
```

#### <a name="error-response-format"></a>Formato de respuestas de error
Cuando el código de estado no es correcto, se considera una respuesta de error. La conexión se **anula** si el código de estado de respuesta `message` no es correcto.

### <a name="user-custom-event-custom_event-for-pubsub-websocket-clients"></a>Evento personalizado de usuario `{custom_event}` para clientes de PubSub WebSocket
<a name="custom_event"></a>

El servicio llama al webhook del controlador de eventos para cada mensaje de evento personalizado válido.

#### <a name="case-1-send-event-with-text-data"></a>Caso 1: envío de eventos con datos de texto:
```json
{
    "type": "event",
    "event": "<event_name>",
    "dataType" : "text",
    "data": "text data"
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
ce-subprotocol: json.webpubsub.azure.v1
ce-connectionState: eyJrZXkiOiJhIn0=

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
ce-subprotocol: json.webpubsub.azure.v1
ce-connectionState: eyJrZXkiOiJhIn0=

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
    "data": "aGVsbG8gd29ybGQ=" // base64 encoded binary
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
ce-subprotocol: json.webpubsub.azure.v1

<binary data>

```

#### <a name="success-response-format"></a>Formato de respuesta correcto

```HTTP
HTTP/1.1 200 OK
Content-Type: application/octet-stream | text/plain | application/json
Content-Length: nnnn

UserResponsePayload
```
* status code
    * `204`: correcto, sin contenido.
    * `200`: correcto, el envío de datos al cliente de PubSub WebSocket depende de `Content-Type`; 
* Encabezado `ce-connectionState`: si este encabezado existe, el estado de esta conexión se actualizará al valor del encabezado. Tenga en cuenta que solo los eventos de *bloqueo* pueden actualizar el estado de conexión. En el ejemplo siguiente se usa una cadena JSON codificada en Base64 para almacenar el estado complejo de la conexión.
* Cuando el encabezado `Content-Type` es `application/octet-stream`, el servicio devuelve `UserResponsePayload` al cliente usando `dataType` como `binary` con base64 codificada. Respuesta de ejemplo:
    ```json
    {
        "type": "message",
        "from": "server",
        "dataType": "binary",
        "data" : "aGVsbG8gd29ybGQ="
    }
    ```
* Cuando `Content-Type` es `text/plain`, el servicio envía `UserResponsePayload` al cliente usando `dataType` como `text` con cadena de carga.
* Cuando `Content-Type` es `application/json`, el servicio envía `UserResponsePayload` al cliente usando `dataType`=`json` con el token de valor `data` como cuerpo de carga de la respuesta.


#### <a name="error-response-format"></a>Formato de respuestas de error
Cuando el código de estado no es correcto, se considera una respuesta de error. La conexión se **anula** si el código de estado de respuesta `{custom_event}` no es correcto.

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
