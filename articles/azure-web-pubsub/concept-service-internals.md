---
title: Elementos internos del servicio Azure Web PubSub
description: Obtenga información sobre los aspectos internos del servicio Azure Web PubSub, la arquitectura, las conexiones y cómo se transmiten los datos.
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: a7adef1e705d14578cdec9bec7a947cc9be0db20
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598331"
---
#  <a name="azure-web-pubsub-service-internals"></a>Elementos internos del servicio Azure Web PubSub

El servicio Azure Web PubSub ofrece una manera sencilla de publicar y suscribir mensajes mediante conexiones [WebSocket](https://tools.ietf.org/html/rfc6455) sencillas.

- El cliente se puede escribir en cualquier lenguaje que tenga compatibilidad con WebSocket.
- Se admiten tanto los mensajes de texto como binarios dentro de una conexión.
- Un protocolo simple para que los clientes realicen la publicación directa de mensajes de cliente a cliente.
- El servicio administra las conexiones de WebSocket automáticamente.

## <a name="terms"></a>Términos
* **Servicio**: Azure Web PubSub.

[!INCLUDE [Terms](includes/terms.md)]

* **Conexión del cliente** y **ConnectionId**: Un cliente se conecta al punto de conexión `/client`, cuando está conectado, el servicio genera un único `connectionId` como identidad única de la conexión del cliente. A continuación, los usuarios pueden administrar la conexión del cliente mediante este `connectionId`. En la sección [Protocolo del cliente](#client_protocol) se describen más detalles.

* **Eventos del cliente**: Los eventos se crean durante el ciclo de vida de una conexión del cliente. Por ejemplo, una conexión del cliente WebSocket simple crea un evento `connect` cuando intenta conectarse al servicio, un evento `connected` cuando se conecta correctamente al servicio, un evento `message` cuando envía mensajes al servicio y un evento `disconnected` cuando se desconecta del servicio. En la sección [Protocolo del cliente](#client_protocol) se describen más detalles sobre los *eventos del cliente*.

* **Controlador de eventos**: El controlador de eventos contiene la lógica para controlar los eventos del cliente. Registre y configure controladores de eventos en el servicio a través del portal o la CLI de Azure de antemano. En la sección [Controlador de eventos](#event_handler) se describen más detalles. El lugar donde se hospedará la lógica del controlador de eventos se considera el lado servidor.

* **Servidor**: El servidor puede controlar eventos de cliente, administrar las conexiones del cliente y publicar mensajes en grupos. El servidor, en comparación con el cliente, es de confianza. En la sección [Protocolo del servidor](#server_protocol) se describen más detalles sobre el **servidor**.

<a name="workflow"></a>

## <a name="workflow"></a>Flujo de trabajo

![Diagrama que muestra el flujo de trabajo del servicio Web PubSub.](./media/concept-service-internals/workflow.png)

Como se muestra en el gráfico de flujo de trabajo anterior:
1. Un *cliente* se conecta al punto de conexión `/client` del servicio mediante el transporte de WebSocket. El servicio reenvía cada marco de WebSocket al servidor ascendente configurado. La conexión de WebSocket puede conectarse con cualquier subprotocolo personalizado para que el servidor lo controle, o bien puede conectarse con el subprotocolo `json.webpubsub.azure.v1` compatible con el servicio, que permite a los clientes publicar o suscribir directamente. En [Protocolo del cliente](#client_protocol) se describen los detalles.
2. El servicio invoca el servidor mediante el **protocolo HTTP CloudEvents** en eventos de cliente diferentes. [**CloudEvents**](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md) es una definición del protocolo, estandarizada e independiente de la estructura, y una descripción de metadatos de los eventos hospedados por Cloud Native Computing Foundation (CNCF). Los detalles se describen en [Protocolo del servidor](#server_protocol).
3. El servidor puede invocar el servicio mediante la API REST para enviar mensajes a los clientes o para administrar los clientes conectados. Los detalles se describen en [Protocolo del servidor](#server_protocol).

<a name="client_protocol"></a>

## <a name="client-protocol"></a>Protocolo del cliente

Una conexión del cliente se conecta al punto de conexión `/client` del servicio mediante el [protocolo de WebSocket](https://tools.ietf.org/html/rfc6455). El protocolo de WebSocket ofrece canales de comunicación dúplex completos a través de una única conexión TCP, e IETF lo estandarizó como RFC 6455 en 2011. La mayoría de los lenguajes tienen compatibilidad nativa para iniciar conexiones WebSocket. 

Nuestro servicio admite dos tipos de cliente:
- Uno se denomina [cliente simple de WebSocket](#simple_client).
- El otro se denomina [cliente WebSocket de PubSub](#pubsub_client).

<a name="simple_client"></a>

### <a name="the-simple-websocket-client"></a>El cliente simple de WebSocket
Un cliente simple de WebSocket, como indica la nomenclatura, es una conexión simple de WebSocket. También puede tener su subprotocolo personalizado. 

Por ejemplo, en JS, se puede crear un cliente simple de WebSocket mediante:
```js
// simple WebSocket client1
var client1 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1');

// simple WebSocket client2 with some custom subprotocol
var client2 = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'custom.subprotocol')

```

Un cliente simple de WebSocket sigue una arquitectura de servidor<->cliente, como se muestra en el diagrama de secuencia siguiente: ![Diagrama que muestra la secuencia de una conexión del cliente.](./media/concept-service-internals/simple-client-sequence.png)


1. Cuando el cliente inicia el protocolo de enlace WebSocket, el servicio intenta invocar el controlador de eventos `connect` (el servidor) para el protocolo de enlace WebSocket. Los usuarios pueden usar este controlador para controlar el protocolo de enlace WebSocket, determinar el subprotocolo que se va a usar, autenticar el cliente y unir el cliente a algunos grupos.
2. Cuando el cliente se conecta correctamente, el servicio invoca un controlador de eventos `connected`. Funciona como una notificación y no bloquea el envío de mensajes al cliente. Los usuarios pueden usar este controlador para almacenar algunos datos, y pueden responder con mensajes que se envían al cliente.
2. Cuando el cliente envía mensajes, los servicios desencadenan el evento `message` al controlador de eventos (el servidor) para controlar los mensajes enviados. Este es un evento general que contiene los mensajes enviados en un marco de WebSocket. El usuario tiene que enviar los mensajes por su cuenta dentro de este controlador de eventos.
3. Cuando el cliente se desconecta, el servicio intenta desencadenar el evento `disconnected` en el controlador de eventos (el servidor) una vez que detecta la desconexión.

Los eventos se dividen en dos categorías:
* Eventos sincrónicos (bloqueo). Los eventos sincrónicos bloquean el flujo de trabajo del cliente. Cuando se produce un error en este tipo de desencadenador de eventos, el servicio anula la conexión del cliente.
    * `connect`
    * `message`
* Eventos asincrónicos (sin bloqueo). Los eventos asincrónicos no bloquean el flujo de trabajo del cliente, actúan como una notificación al controlador de eventos ascendente. Cuando se produce un error en este tipo de desencadenador de eventos, el servicio registra los detalles del error.
    * `connected`
    * `disconnected`
    
#### <a name="scenarios"></a>Escenarios:
Esta conexión se puede usar en una arquitectura habitual de cliente-servidor, donde el cliente envía mensajes al servidor, y el servidor controla los mensajes entrantes mediante [controladores de eventos](#event_handler). También se puede usar cuando los clientes aplican [subprotocolos](https://www.iana.org/assignments/websocket/websocket.xml) existentes en su lógica de aplicación.

<a name="pubsub_client"></a>

### <a name="the-pubsub-websocket-client"></a>El cliente WebSocket de PubSub
El servicio también admite un subprotocolo específico denominado `json.webpubsub.azure.v1`, que permite a los clientes publicar o suscribirse directamente en lugar de realizar un recorrido de ida y vuelta al servidor ascendente. Llamamos a la conexión de WebSocket con el subprotocolo `json.webpubsub.azure.v1` un cliente WebSocket de PubSub.

Por ejemplo, en JS, se puede crear un cliente WebSocket de PubSub mediante:
```js
// PubSub WebSocket client
var pubsub = new WebSocket('wss://test.webpubsub.azure.com/client/hubs/hub1', 'json.webpubsub.azure.v1');
```

Un cliente WebSocket de PubSub puede:
* Unirse a un grupo, por ejemplo:
    ```json
    {
        "type": "joinGroup",
        "group": "<group_name>"
    }
    ```
* Abandonar un grupo, por ejemplo:
    ```json
    {
        "type": "leaveGroup",
        "group": "<group_name>"
    }
    ```
* Publicar mensajes en un grupo, por ejemplo:
    ```json
    {
        "type": "sendToGroup",
        "group": "<group_name>",
        "data": { "hello": "world" }
    }
    ```
* Enviar eventos personalizados al servidor ascendente, por ejemplo:

    ```json
    {
        "type": "event",
        "event": "<event_name>",
        "data": { "hello": "world" }
    }
    ```

El [subprotocolo WebSocket de PubSub](./reference-json-webpubsub-subprotocol.md) contiene los detalles del subprotocolo `json.webpubsub.azure.v1`.

Puede que haya observado que, para un [cliente simple de WebSocket](#simple_client), el *servidor* es un rol IMPRESCINDIBLE para controlar los eventos de los clientes. Una conexión simple de WebSocket siempre desencadena un evento `message` cuando envía mensajes, y siempre se basa en el lado servidor para procesar mensajes y realizar otras operaciones. Con la ayuda del subprotocolo `json.webpubsub.azure.v1`, un cliente autorizado puede unirse a un grupo y publicar mensajes en un grupo directamente. También puede enrutar mensajes a distintos canales de subida (controladores de eventos) personalizando el *evento* al que pertenece el mensaje. 

#### <a name="scenarios"></a>Escenarios:
Estos clientes se pueden usar cuando los clientes quieren comunicarse entre sí. Los mensajes se envían de `client1` al servicio, y el servicio entrega el mensaje directamente a `client2` si los clientes están autorizados para hacerlo.

Client1:

```js
var client1 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client1.onmessage = e => {
    if (e.data) {
        var message = JSON.parse(e.data);
        if (message.type === "message" 
        && message.group === "Group1"){
            // Only print messages from Group1
            console.log(message.data);
        }
    }
};

client1.onopen = e => {
    client1.send(JSON.stringify({
        type: "joinGroup",
        group: "Group1"
    }));
};
```

Client2:

```js
var client2 = new WebSocket("wss://xxx.webpubsub.azure.com/client/hubs/hub1", "json.webpubsub.azure.v1");
client2.onopen = e => {
    client2.send(JSON.stringify({
        type: "sendToGroup",
        group: "Group1",
        data: "Hello Client1"
    });
};
```

Como se muestra en el ejemplo anterior, `client2` envía datos directamente a `client1` mediante la publicación de mensajes en `Group1` donde se encuentra `client1`.

<a name="client_message_limit"></a>

### <a name="client-message-limit"></a>Límite de mensajes del cliente
El tamaño máximo permitido de mensaje para un marco WebSocket es de **1 MB**.

<a name="client_auth"></a>

### <a name="client-auth"></a>Autenticación de cliente

#### <a name="auth-workflow"></a>Flujo de trabajo de autenticación

El cliente usa un token JWT firmado para conectarse al servicio. El servidor ascendente también puede rechazar el cliente cuando es el controlador de eventos `connect` del cliente entrante. El controlador de eventos autentica el cliente al especificar los valores de `userId` y `role` que tiene el cliente en la respuesta del webhook, o bien rechaza el cliente con 401. En la sección [Controlador de eventos](#event_handler) se describe en detalle.

En el gráfico siguiente se describe el flujo de trabajo:

![Diagrama que muestra el flujo de trabajo de autenticación de cliente.](./media/concept-service-internals/client-connect-workflow.png)

Como puede que haya observado, cuando se describieron los clientes WebSocket de PubSub, un cliente solo puede publicar en otros clientes cuando está *autorizado*. Los valores de `role` del cliente determinan los permisos *iniciales* que tiene el cliente:

| Rol | Permiso |
|---|---|
| No especificado | El cliente puede enviar eventos.
| `webpubsub.joinLeaveGroup` | El cliente puede unirse a cualquier grupo o abandonarlo.
| `webpubsub.sendToGroup` | El cliente puede publicar mensajes en cualquier grupo.
| `webpubsub.joinLeaveGroup.<group>` | El cliente puede unirse al grupo `<group>` o abandonarlo.
| `webpubsub.sendToGroup.<group>` | El cliente puede publicar mensajes en el grupo `<group>`.

El lado servidor también puede conceder o revocar permisos del cliente dinámicamente a través del [protocolo del servidor](#connection_manager), como se muestra en una sección posterior.

<a name="server_protocol"></a>

## <a name="server-protocol"></a>Protocolo del servidor

El protocolo del servidor brinda la funcionalidad para que el servidor administre las conexiones del cliente y los grupos.

En general, el protocolo del servidor contiene dos roles:
1. [Controlador de eventos](#event_handler)
2. [Connection manager](#connection_manager)

<a name="event_handler"></a>

### <a name="event-handler"></a>Controlador de eventos
El controlador de eventos controla los eventos de cliente entrantes. Los controladores de eventos se registran y configuran en el servicio a través del portal o la CLI de Azure de antemano para que, cuando se desencadene un evento de cliente, el servicio pueda identificar si se espera que el evento se controle o no. Para la versión preliminar pública, se usa el modo `PUSH` para invocar el controlador de eventos: el controlador de eventos, como lado servidor, expone el punto de conexión de acceso público que invocará el servicio cuando se desencadene el evento. Funciona como **webhook**. 

El servicio entrega los eventos de cliente al webhook ascendente mediante el [protocolo HTTP CloudEvents](https://github.com/cloudevents/spec/blob/v1.0.1/http-protocol-binding.md).

Para cada evento, formula una solicitud HTTP POST al servidor ascendente registrado y espera una respuesta HTTP. 

Los datos que se envían del servicio al servidor siempre están en formato `binary` de CloudEvents.

![Diagrama que muestra el modo de inserción de eventos del servicio Web PubSub.](./media/concept-service-internals/event-push.png)

#### <a name="upstream-and-validation"></a>Servidor ascendente y validación

Los controladores de eventos tienen que registrarse y configurarse en el servicio a través del portal o la CLI de Azure de antemano para que, cuando se desencadene un evento de cliente, el servicio pueda identificar si se espera que el evento se controle o no. Para la versión preliminar pública, se usa el modo `PUSH` para invocar el controlador de eventos: el controlador de eventos, como lado servidor, expone el punto de conexión de acceso público que invocará el servicio cuando se desencadene el evento. Funciona como **webhook** **ascendente**. 

Al configurar el punto de conexión de webhook, la dirección URL puede usar el parámetro `{event}` para definir una plantilla de dirección URL. El servicio calcula el valor de la dirección URL del webhook dinámicamente cuando entra la solicitud del cliente. Por ejemplo, cuando llega una solicitud `/client/hubs/chat`, con un patrón `http://host.com/api/{event}` de dirección URL de controlador de eventos configurado para el centro `chat`, cuando el cliente se conecta, primero se aplica POST a esta dirección URL: `http://host.com/api/connect`. Esto puede ser útil cuando un cliente de WebSocket de PubSub envía eventos personalizados, que el controlador de eventos ayuda a enviar eventos diferentes a diferentes niveles ascendentes. Tenga en cuenta que el parámetro `{event}` no está permitido en el nombre de dominio de la dirección URL.

Al configurar el controlador de eventos ascendente a través de Azure Portal o la CLI, el servicio sigue la [protección contra abusos de CloudEvents](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) para validar el webhook ascendente. El encabezado de la solicitud `WebHook-Request-Origin` se establece en el nombre de dominio del servicio `xxx.webpubsub.azure.com`, y espera que la respuesta que tiene el encabezado `WebHook-Allowed-Origin` contenga este nombre de dominio.

Al realizar la validación, el parámetro `{event}` se resuelve en `validate`. Por ejemplo, al intentar establecer la dirección URL en `http://host.com/api/{event}`, el servicio intenta aplicar **OPTIONS** a una solicitud a `http://host.com/api/validate`, y la configuración se puede establecer correctamente solo cuando la respuesta es válida.

Por ahora, no se admiten [WebHook-Request-Rate](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#414-webhook-request-rate) ni [WebHook-Request-Callback](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#413-webhook-request-callback).

#### <a name="authentication-between-service-and-webhook"></a>Autenticación entre el servicio y el webhook
- Modo anónimo
- Autenticación simple que se proporciona a `code` a través de la dirección URL del webhook configurada.
- Autenticación de AAD. 
   - Agregue un secreto de cliente en [Registros de aplicaciones] de AAD y proporcione el [secreto de cliente] a Azure Web PubSub a través del portal o la CLI.
   - Proporcione la [Identidad](/azure/app-service/overview-managed-identity?tabs=dotnet) a Azure Web PubSub mediante el portal o la CLI.

<a name="connection_manager"></a>

### <a name="connection-manager"></a>Administrador de conexiones

Por naturaleza, el servidor es un usuario autorizado. Con la ayuda del *rol de controlador de eventos*, el servidor conoce los metadatos de los clientes, por ejemplo, `connectionId` y `userId`, para que pueda:
   - Cierre de una conexión de cliente
   - Enviar mensajes a un cliente
   - Enviar mensajes a clientes que pertenecen al mismo usuario
   - Agregar un cliente a un grupo
   - Agregar clientes autenticados como el mismo usuario a un grupo
   - Quitar un cliente de un grupo
   - Quitar clientes autenticados como el mismo usuario de un grupo
   - Publicar mensajes en un grupo

También puede conceder o revocar permisos de publicación/unión para un cliente de PubSub:
   - Conceder permisos de unión o publicación a un grupo específico o a todos los grupos
   - Revocar permisos de unión o publicación a un grupo específico o a todos los grupos
   - Comprobar si el cliente tiene permiso para unirse o publicar en un grupo específico o en todos los grupos.
   
Para la versión preliminar pública, el servicio proporciona API REST para que el servidor se encargue de la administración de las conexiones:

![Diagrama que muestra el flujo de trabajo del administrador de conexiones del servicio Web PubSub.](./media/concept-service-internals/manager-rest.png)

El protocolo de API REST detallado se define [aquí][rest].

### <a name="summary"></a>Resumen
Es posible que haya observado que el *rol de controlador de eventos* controla la comunicación del servicio al servidor, mientras que el *rol de administrador* controla la comunicación del servidor al servicio. Por lo tanto, al combinar los dos roles, el flujo de datos entre el servicio y el servidor es similar al siguiente, mediante el protocolo HTTP:

![Diagrama que muestra el flujo de trabajo bidireccional del servicio Web PubSub.](./media/concept-service-internals/http-service-server.png)

[rest]: /rest/api/webpubsub/

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]
