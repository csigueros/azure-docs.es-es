---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0a3e3d80e5b669884d19d5eb60260a31b1836de3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699427"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión beta del SDK web de llamada de ACS.

La trasferencia de llamadas es una característica extendida de la API `Call` principal. Primero debe obtener el objeto de API de la característica de transferencia:

```js
const callTransferApi = call.api(Features.Transfer);
```

Las transferencias de llamadas implican tres entidades:

- *Transmitente*: la persona que inicia la solicitud de transferencia.
- *Adquirente*: la persona que se va a transferir.
- *Destino de la transferencia*: la persona a la que se transfiere.

Las transferencias siguen estos pasos:

1. Ya hay una llamada conectada entre el *transmitente* y el *adquirente*. El *transmitente* decide transferir la llamada del *adquirente* al *destino de la transferencia*.
1. El *transmitente* llama a `transfer` API.
1. El *adquirente* decide si `accept` o `reject` la solicitud de transferencia al *destino de la transferencia* mediante un evento `transferRequested`.
1. El *destino de la transferencia* recibe una llamada entrante solo si el *adquirente* acepta la solicitud de transferencia.

Para transferir la llamada actual, puede usar `transfer` API. `transfer` toma el objeto `transferCallOptions` opcional, que permite establecer la marca `disableForwardingAndUnanswered`:

- `disableForwardingAndUnanswered = false`: si el *destino de la transferencia* no responde a la llamada de transferencia, la transferencia sigue las configuraciones de reenvío y sin respuesta del *destino de la transferencia*.
- `disableForwardingAndUnanswered = true`: si el *destino de la transferencia* no responde a la llamada de transferencia, el intento de transferencia finalizará.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer` API le permite suscribirse a los eventos `transferStateChanged` y `transferRequested`. Un evento `transferRequested` procede de una instancia de `call`, un evento `transferStateChanged` y la transferencia `state` y `error` provienen de una instancia de `transfer`.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

El *adquirente* puede aceptar o rechazar la solicitud de transferencia iniciada por el *transmitente* en el evento `transferRequested` a través de `accept()` o `reject()` en `transferRequestedEventArgs`. Puede acceder a la información de `targetParticipant` y a los métodos `accept` y `reject` en `transferRequestedEventArgs`.

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
    args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
    args.reject();
});
```