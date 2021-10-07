---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 3ab9331dddb90803867f6cfb5118aa33bdd313d0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699420"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="place-a-call"></a>Realización de una llamada

Para crear e iniciar una llamada, utilice una de las API en `callAgent` y proporcione un usuario que haya creado mediante el SDK de identidades de Communication Services.

La creación y el inicio de la llamada son sincrónicos. La instancia `call` le permite suscribirse a eventos de llamada.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Realización de una llamada 1:n a un usuario o RTC

Para llamar a otro usuario de Communication Services, invoque el método `startCall` en `callAgent` y pase la instancia de `CommunicationUserIdentifier` que ha [creado con la biblioteca de administración de Communication Services](../../../../quickstarts/access-tokens.md).

Para una llamada 1:1 a un usuario, utilice el código siguiente:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Para realizar una llamada a una red telefónica conmutada (RTC) pública, use el método `startCall` en `callAgent` y pase el `PhoneNumberIdentifier` del destinatario. El recurso de Communication Services debe estar configurado para permitir llamadas de RTC.

Cuando llame a un número de RTC, debe especificar su identificador alternativo de autor de llamada. Un identificador del autor de llamada alternativo es un número de teléfono (basado en el estándar E.164), que identifica al autor de llamada en una llamada RTC. Es el número de teléfono que el destinatario de la llamada ve en una llamada entrante.

> [!NOTE]
> Llamadas RTC están actualmente en versión preliminar privada. Para acceder, [suscríbase al programa de usuarios pioneros](https://aka.ms/ACS-EarlyAdopter).

Para una llamada 1:1 a un número de RTC, utilice el código siguiente:
```js
const pstnCallee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Para una llamada 1:n a un usuario y a un número RTC, utilice el código siguiente:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const pstnCallee = { phoneNumber: '<PHONE_NUMBER>'};
const alternateCallerId = {alternateCallerId: '<ALTERNATE_CALLER_ID>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="join-a-group-call"></a>Unión a una llamada grupal

> [!NOTE]
> El parámetro `groupId` se considera metadatos del sistema y Microsoft puede usarlo para las operaciones necesarias para ejecutar el sistema. No incluya datos personales en el valor `groupId`. Microsoft no trata este parámetro como datos personales y su contenido puede ser visible para los empleados de Microsoft o almacenarse a largo plazo.
>
> El parámetro `groupId` requiere que los datos estén en formato GUID. Se recomienda usar GUID generados de forma aleatoria que no se consideran datos personales en los sistemas.
>

Para iniciar una llamada grupal nueva o unirse a una en curso, use el método `join` y pase un objeto con una propiedad `groupId`. El valor `groupId` debe ser un GUID.

```js
const context = { groupId: '<GUID>'};
const call = callAgent.join(context);
```

## <a name="receive-an-incoming-call"></a>Recepción de una llamada entrante

La instancia de `callAgent` emite un evento `incomingCall` cuando la identidad que ha iniciado sesión recibe una llamada entrante. Para escuchar este evento, suscríbase mediante una de estas opciones:

```js
const incomingCallHandler = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 

    // Get incoming call ID
    var incomingCallId = incomingCall.id

    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHandler);
```

El evento `incomingCall` incluye una instancia de `incomingCall` que puede aceptar o rechazar.

Al iniciar o aceptar una llamada o al unirse a ella con el vídeo encendido, si otro proceso utiliza el dispositivo de cámara de vídeo especificado o si este dispositivo está deshabilitado en el sistema, la llamada se iniciará con el vídeo apagado y aparecerá un diagnóstico de llamada cameraStartFailed: true.

## <a name="mute-and-unmute"></a>Silencio y reactivación del sonido

Para silenciar o reactivar el sonido del punto de conexión local, puede usar las API asincrónicas `mute` y `unmute`:

```js
//mute local device
await call.mute();

//unmute local device
await call.unmute();
```

## <a name="manage-remote-participants"></a>Administración de participantes remotos

El tipo `RemoteParticipant` representa a todos los participantes remotos, los que están disponibles a través de la colección `remoteParticipants` en una instancia de llamada.

### <a name="list-the-participants-in-a-call"></a>Enumeración de los participantes en una llamada

La colección `remoteParticipants` devuelve una lista de los participantes remotos en una llamada:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>Incorporación de un participante a una llamada

Para agregar un participante (ya sea un usuario o un número de teléfono) a una llamada, puede usar `addParticipant`. Proporcione uno de los tipos de `Identifier`. Devuelve la instancia de `remoteParticipant` de forma sincrónica. El evento `remoteParticipantsUpdated` de Call se genera cuando un participante se agrega correctamente a la llamada.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<ALTERNATE_CALLER_ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Eliminación de un participante de una llamada

Para quitar un participante (ya sea un usuario o un número de teléfono) de una llamada, puede invocar `removeParticipant`. Tiene que pasar uno de los tipos de `Identifier`. Este método se resolverá de manera asincrónica una vez que el participante se quite de la llamada. El participante también se quitará de la colección `remoteParticipants`.

```js
const userIdentifier = { communicationUserId: '<ACS_USER_ID>' };
const pstnIdentifier = { phoneNumber: '<PHONE_NUMBER>' }
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

### <a name="access-remote-participant-properties"></a>Acceso a las propiedades de los participantes remotos

Los participantes remotos tienen un conjunto de propiedades y colecciones asociadas:

- `CommunicationIdentifier`: obtenga el identificador de este participante remoto. "Identity" es uno de los tipos de `CommunicationIdentifier`:

    ```js
    const identifier = remoteParticipant.identifier;
    ```

Puede ser uno de los siguientes tipos de `CommunicationIdentifier`:

- `{ communicationUserId: '<ACS_USER_ID'> }`: objeto que representa el usuario de ACS.
- `{ phoneNumber: '<E.164>' }`: objeto que representa el número de teléfono en formato E.164.
- `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: objeto que representa al usuario de Teams.
- `{ id: string }`: objeto que representa el identificador que no se ajusta a ninguno de los otros tipos de identificadores.

- `state`: obtenga el estado de este participante remoto.

    ```js
    const state = remoteParticipant.state;
    ```

El estado puede ser:

- `Idle`: estado inicial.
- `Connecting`: estado de transición mientras el participante se conecta a la llamada.
- `Ringing`: el participante está sonando.
- `Connected`: el participante se conecta a la llamada.
- `Hold`: el participante está en espera.
- `EarlyMedia`: anuncio que se reproduce antes de que un participante se conecte a la llamada.
- `InLobby`: indica que el participante remoto está en sala de espera.
- `Disconnected`: estado final. El participante se desconecta de la llamada. Si el participante remoto pierde la conectividad de red, su estado cambia a `Disconnected` tras dos minutos.

- `callEndReason`: para saber por qué el participante dejó la llamada, compruebe la propiedad `callEndReason`:

    ```js
    const callEndReason = remoteParticipant.callEndReason;
    const callEndReasonCode = callEndReason.code // (number) code associated with the reason
    const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
    ```

- Estado `isMuted`: para averiguar si un participante remoto está silenciado, compruebe la propiedad `isMuted`. Devuelve `Boolean`.

    ```js
    const isMuted = remoteParticipant.isMuted;
    ```

- Estado `isSpeaking`: para averiguar si un participante remoto está hablando, compruebe la propiedad `isSpeaking`. Devuelve `Boolean`.

    ```js
    const isSpeaking = remoteParticipant.isSpeaking;
    ```

- `videoStreams`: para revisar todas las secuencias de vídeo que un participante determinado envía en esta llamada, compruebe la colección `videoStreams`: Contiene objetos `RemoteVideoStream`.

    ```js
    const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
    ```
- `displayName`: para obtener el nombre para mostrar de este participante remoto, revise la propiedad `displayName` que devuelve la cadena. 

    ```js
    const displayName = remoteParticipant.displayName;
    ```

## <a name="check-call-properties"></a>Comprobación de las propiedades de llamada

Obtenga el identificador único (cadena) de una llamada:

```js
const callId: string = call.id;
```
Obtenga información acerca de la llamada:
> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión beta del SDK web de llamada de ACS.
```js
const callInfo = call.info;
```

Para más información sobre los demás participantes de la llamada, revise la colección `remoteParticipants` de la instancia "call".

```js
const remoteParticipants = call.remoteParticipants;
```

Identifique al autor de la llamada de una llamada entrante:
```js
const callerIdentity = call.callerInfo.identifier;
```

`identifier` es uno de los tipos de `CommunicationIdentifier`.

Obtenga el estado de una llamada:

```js
const callState = call.state;
```

Esto devuelve una cadena que representa el estado actual de una llamada:

- `None`: estado inicial de la llamada.
- `Connecting`: estado de transición inicial cuando se realiza o acepta la llamada.
- `Ringing`: en el caso de una llamada saliente, indica que se está llamando a los participantes remotos. Es `Incoming` en su lado.
- `EarlyMedia`: indica un estado en el que se reproduce un anuncio antes de conectar la llamada.
- `Connected`: indica que la llamada está conectada.
- `LocalHold`: indica que un participante local ha puesto en espera la llamada. No hay flujo de medios entre el punto de conexión local y los participantes remotos.
- `RemoteHold`: indica que el participante remoto ha puesto en espera la llamada. No hay flujo de medios entre el punto de conexión local y los participantes remotos.
- `InLobby`: indica que el usuario está en sala de espera.
- `Disconnecting`: estado de la transición antes de que la llamada pase a un estado `Disconnected`.
- `Disconnected`: estado final de la llamada. Si se pierde la conexión de red, el estado cambia a `Disconnected` después de dos minutos.

Averigüe por qué finalizó una llamada mediante la inspección de la propiedad `callEndReason`:

```js
const callEndReason = call.callEndReason;
const callEndReasonCode = callEndReason.code // (number) code associated with the reason
const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
```

Obtenga información sobre si la llamada actual es entrante o saliente mediante la inspección de la propiedad `direction`. Devuelve `CallDirection`.

```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

Compruebe si el micrófono actual está silenciado. Devuelve `Boolean`.

```js
const muted = call.isMuted;
```

Para averiguar si la secuencia de uso compartido de pantalla se envía desde un punto de conexión determinado, revise la propiedad `isScreenSharingOn`: Devuelve `Boolean`.

```js
const isScreenSharingOn = call.isScreenSharingOn;
```

Para inspeccionar las secuencias de vídeo activas, compruebe la colección `localVideoStreams`. Devuelve objetos `LocalVideoStream`.

```js
const localVideoStreams = call.localVideoStreams;
```