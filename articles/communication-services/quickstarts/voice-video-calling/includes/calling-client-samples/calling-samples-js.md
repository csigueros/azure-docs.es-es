---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 06/30/2021
ms.author: rifox
ms.openlocfilehash: b251218593ddef69fc38520b5ff9e46ec7c8291f
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078228"
---
## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../../create-communication-resource.md).
- Un token de acceso de usuario para habilitar el cliente de llamada. Para más información, consulte [Inicio rápido: Creación y administración de tokens de acceso](../../../access-tokens.md).
- Opcional: Complete la guía de inicio rápido para [agregar llamadas de voz a la aplicación](../../getting-started-with-calling.md).

## <a name="install-the-sdk"></a>Instalar el SDK

Use el comando `npm install` para instalar los SDK comunes y de llamada de Azure Communication Services para JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
El SDK de llamadas web de Communication Services se debe usar a través de `https`. En caso de desarrollo local, use `localhost` o "file:" local.

## <a name="documentation-support"></a>Soporte de documentación
- [Envío de problemas o errores en GitHub](https://github.com/Azure/Communication/issues)
- [Aplicaciones de ejemplo](../../../../samples/overview.md)
- [Referencia de API](/javascript/api/azure-communication-services/@azure/communication-calling/?preserve-view=true&view=azure-communication-services-js)

## <a name="models"></a>Modelos
### <a name="object-model"></a>Modelo de objetos

Las siguientes clases e interfaces controlan algunas de las características principales del SDK de llamadas de Azure Communication Services:

| Nombre                                | Descripción                                                                                                                              |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                        | El punto de entrada principal al SDK de llamadas.                                                                                                 |
| `AzureCommunicationTokenCredential` | Implementa la interfaz de `CommunicationTokenCredential`, que se usa para crear instancias de `callAgent`.                                       |
| `CallAgent`                         | Se utiliza para iniciar y administrar las llamadas.                                                                                                          |
| `DeviceManager`                     | Se usa para administrar dispositivos multimedia.                                                                                                            |
| `Call`                              | Se usa para representar una llamada.                                                                                                              |
| `LocalVideoStream`                  | Se usa para crear una secuencia de vídeo local para un dispositivo de cámara en el sistema local.                                                          |
| `RemoteParticipant`                 | Se usa para representar a un participante remoto de la llamada.                                                                                   |
| `RemoteVideoStream`                 | Se usa para representar una secuencia de vídeo remota desde un participante remoto.                                                                  |

> [!NOTE]
> Las instancias de objetos del SDK de llamada no se deben considerar objetos de JavaScript sin formato. En realidad, se trata de instancias de varias clases y, por lo tanto, no se pueden serializar.

### <a name="events-model"></a>Modelo de eventos
Cada objeto del SDK de llamada tiene valores de propiedades y de colecciones cuyos valores cambian a lo largo de la vigencia del objeto.
Use el método `on()` para suscribirse a los eventos de los objetos y el método `off()` para finalizar la suscripción.

#### <a name="properties"></a>Propiedades
- Debe inspeccionar sus valores iniciales y suscribirse al evento `'<property>Changed'` para actualizaciones futuras de los valores.

#### <a name="collections"></a>Colecciones
- Debe inspeccionar sus valores iniciales y suscribirse al evento `'<collection>Updated'` para actualizaciones futuras de los valores.
- La carga del evento `'<collection>Updated'`tiene una matriz `added` que contiene los valores que se quitaron de la colección.
- La carga del evento `'<collection>Updated'` también tiene una matriz `removed` que contiene los valores que se quitaron de la colección.

```js
/*************************************
 * Example code - client.js          *
 * Convert this script into a        *
 * bundle.js that your html index    *
 * page can use.                     *
 *************************************/

// Make sure to install the necessary dependencies
const { CallClient, VideoStreamRenderer, LocalVideoStream } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential } = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the log level and output
setLogLevel('verbose');
AzureLogger.log = (...args) => {
    console.log(...args);
};

// Calling web sdk objects
let callAgent;
let deviceManager;
let call;
let incomingCall;
let localVideoStream;
let localVideoStreamRenderer;

// UI widgets
let userAccessToken = document.getElementById('user-access-token');
let calleeAcsUserId = document.getElementById('callee-acs-user-id');
let initializeCallAgentButton = document.getElementById('initialize-call-agent');
let startCallButton = document.getElementById('start-call-button');
let acceptCallButton = document.getElementById('accept-call-button');
let startVideoButton = document.getElementById('start-video-button');
let stopVideoButton = document.getElementById('stop-video-button');
let connectedLabel = document.getElementById('connectedLabel');
let remoteVideoContainer = document.getElementById('remoteVideoContainer');
let localVideoContainer = document.getElementById('localVideoContainer');

// Instantiate the Call Agent.
initializeCallAgentButton.onclick = async () => {
    try {
        const callClient = new CallClient(); 
        tokenCredential = new AzureCommunicationTokenCredential(userAccessToken.value.trim());
        callAgent = await callClient.createCallAgent(tokenCredential)
        // Set up a camera device to use.
        deviceManager = await callClient.getDeviceManager();
        await deviceManager.askDevicePermission({ video: true });
        await deviceManager.askDevicePermission({ audio: true });
        // Listen for an incoming call to accept.
        callAgent.on('incomingCall', async (args) => {
            try {
                incomingCall = args.incomingCall;
                acceptCallButton.disabled = false;
                startCallButton.disabled = true;
            } catch (error) {
                console.error(error);
            }
        });

        startCallButton.disabled = false;
        initializeCallAgentButton.disabled = true;
    } catch(error) {
        console.error(error);
    }
}

// Start an out-going call.
startCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = callAgent.startCall([{ communicationUserId: calleeAcsUserId.value.trim() }], { videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Accept the incoming call.
acceptCallButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        const videoOptions = localVideoStream ? { localVideoStreams: [localVideoStream] } : undefined;
        call = await incomingCall.accept({ videoOptions });
        // Subscribe to the call's properties and events.
        subscribeToCall(call);
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a call obj.
// Listen for property changes and collection udpates.
subscribeToCall = (call) => {
    try {
        // Inspect the initial call.id value.
        console.log(`Call Id: ${call.id}`);
        //Subsribe to call's 'idChanged' event for value changes.
        call.on('idChanged', () => {
            console.log(`Call Id changed: ${call.id}`); 
        });

        // Inspect the initial call.state value.
        console.log(`Call state: ${call.state}`);
        // Subscribe to call's 'stateChanged' event for value changes.
        call.on('stateChanged', async () => {
            console.log(`Call state changed: ${call.state}`);
            if(call.state === 'Connected') {
                connectedLabel.hidden = false;
                acceptCallButton.disabled = true;
                startCallButton.disabled = true;
                startVideoButton.disabled = false;
                stopVideoButton.disabled = false
            } else if (call.state === 'Disconnected') {
                connectedLabel.hidden = true;
                startCallButton.disabled = false;
                console.log(`Call ended, call end reason={code=${call.callEndReason.code}, subCode=${call.callEndReason.subCode}}`);
            }   
        });

        call.localVideoStreams.forEach(async (lvs) => {
            localVideoStream = lvs;
            await displayLocalVideoStream();
        });
        call.on('localVideoStreamsUpdated', e => {
            e.added.forEach(async (lvs) => {
                localVideoStream = lvs;
                await displayLocalVideoStream();
            });
            e.removed.forEach(lvs => {
               removeLocalVideoStream();
            });
        });
        
        // Inspect the call's current remote participants and subscribe to them.
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        // Subscribe to the call's 'remoteParticipantsUpdated' event to be
        // notified when new participants are added to the call or removed from the call.
        call.on('remoteParticipantsUpdated', e => {
            // Subscribe to new remote participants that are added to the call.
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            // Unsubscribe from participants that are removed from the call
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant obj.
// Listen for property changes and collection udpates.
subscribeToRemoteParticipant = (remoteParticipant) => {
    try {
        // Inspect the initial remoteParticipant.state value.
        console.log(`Remote participant state: ${remoteParticipant.state}`);
        // Subscribe to remoteParticipant's 'stateChanged' event for value changes.
        remoteParticipant.on('stateChanged', () => {
            console.log(`Remote participant state changed: ${remoteParticipant.state}`);
        });

        // Inspect the remoteParticipants's current videoStreams and subscribe to them.
        remoteParticipant.videoStreams.forEach(remoteVideoStream => {
            subscribeToRemoteVideoStream(remoteVideoStream)
        });
        // Subscribe to the remoteParticipant's 'videoStreamsUpdated' event to be
        // notified when the remoteParticiapant adds new videoStreams and removes video streams.
        remoteParticipant.on('videoStreamsUpdated', e => {
            // Subscribe to new remote participant's video streams that were added.
            e.added.forEach(remoteVideoStream => {
                subscribeToRemoteVideoStream(remoteVideoStream)
            });
            // Unsubscribe from remote participant's video streams that were removed.
            e.removed.forEach(remoteVideoStream => {
                console.log('Remote participant video stream was removed.');
            })
        });
    } catch (error) {
        console.error(error);
    }
}

// Subscribe to a remote participant's remote video stream obj.
// Listen for property changes and collection udpates.
// When their remote video streams become available, display them in the UI.
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.hidden = false;
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }   
    }
    
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
    }
}

// Start your local video stream.
// This will send your local video stream to remote participants so they can view it.
startVideoButton.onclick = async () => {
    try {
        const localVideoStream = await createLocalVideoStream();
        await call.startVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Stop your local video stream.
// This will stop your local video stream from being sent to remote participants.
stopVideoButton.onclick = async () => {
    try {
        await call.stopVideo(localVideoStream);
    } catch (error) {
        console.error(error);
    }
}

// Create a local video stream for your camera device
createLocalVideoStream = async () => {
    const camera = (await deviceManager.getCameras())[0];
    if (camera) {
        return new LocalVideoStream(camera);
    } else {
        console.error(`No camera device found on the system`);
    }
}

// Display your local video stream preview in your UI
displayLocalVideoStream = async () => {
    try {
        localVideoStreamRenderer = new VideoStreamRenderer(localVideoStream);
        const view = await localVideoStreamRenderer.createView();
        localVideoContainer.hidden = false;
        localVideoContainer.appendChild(view.target);
    } catch (error) {
        console.error(error);
    } 
}

// Remove your local video stream preview from your UI
removeLocalVideoStream = async() => {
    try {
        localVideoStreamRenderer.dispose();
        localVideoContainer.hidden = true;
    } catch (error) {
        console.error(error);
    } 
}
```

Código de ejemplo HTML que puede usar una agrupación generada a partir del ejemplo de JavaScript anterior (`client.js`).
```html
<!-- index.html -->
<!DOCTYPE html>
<html>
    <head>
        <title>Azure Communication Services - Calling Web SDK</title>
    </head>
    <body>
        <h4>Azure Communication Services - Calling Web SDK</h4>
        <input id="user-access-token"
            type="text"
            placeholder="User access token"
            style="margin-bottom:1em; width: 500px;"/>
        <button id="initialize-call-agent" type="button">Initialize Call Agent</button>
        <br>
        <br>
        <input id="callee-acs-user-id"
            type="text"
            placeholder="Enter callee's ACS user identity in format: '8:acs:resourceId_userId'"
            style="margin-bottom:1em; width: 500px; display: block;"/>
        <button id="start-call-button" type="button" disabled="true">Start Call</button>
        <button id="accept-call-button" type="button" disabled="true">Accept Call</button>
        <button id="start-video-button" type="button" disabled="true">Start Video</button>
        <button id="stop-video-button" type="button" disabled="true">Stop Video</button>
        <br>
        <br>
        <div id="connectedLabel" style="color: #13bb13;" hidden>Call is connected!</div>
        <br>
        <div id="remoteVideoContainer" style="width: 40%;" hidden>Remote participants' video streams:</div>
        <br>
        <div id="localVideoContainer" style="width: 30%;" hidden>Local video stream:</div>
        <!-- points to the bundle generated from client.js -->
        <script src="./bundle.js"></script>
    </body>
</html>
```
Tenga en cuenta que después de iniciar o aceptar una llamada o unirse a ella, también puede usar el evento "callsUpdated" de callAgent para recibir una notificación del objeto de llamada nuevo y empezar a suscribirse a él.
```js
callAgent.on('callsUpdated', e => {
    // New Call object is added to callAgent.calls collection
    e.added.forEach(call => {
        call.remoteParticipants.forEach(remoteParticipant => {
            subscribeToRemoteParticipant(remoteParticipant);
        })
        call.on('remoteParticipantsUpdated', e => {
            e.added.forEach(remoteParticipant => {
                subscribeToRemoteParticipant(remoteParticipant)
            });
            e.removed.forEach(remoteParticipant => {
                console.log('Remote participant removed from the call.');
            })
        });
    })
})
```

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Inicialización de una instancia de CallClient, creación de una instancia de CallAgent y acceso a deviceManager

Cree una instancia de `CallClient`. Se puede configurar con opciones personalizadas como una instancia del registrador.

Cuando haya creado una instancia de `CallClient`, puede crear una de `CallAgent` mediante una llamada al método `createCallAgent` en la instancia de `CallClient`. Este método devuelve un objeto de instancia `CallAgent` de manera asincrónica.

El método `createCallAgent` utiliza `CommunicationTokenCredential` como argumento. Acepta un [token de acceso de usuario](../../../access-tokens.md).

Puede usar el método `getDeviceManager` en la instancia de `CallClient` para acceder a `deviceManager`.

```js
const { CallClient } = require('@azure/communication-calling');
const { AzureCommunicationTokenCredential} = require('@azure/communication-common');
const { AzureLogger, setLogLevel } = require("@azure/logger");
// Set the logger's log level
setLogLevel('verbose');
// Redirect log output to wherever desired. To console, file, buffer, REST API, etc...
AzureLogger.log = (...args) => {
    console.log(...args); // Redirect log output to console
};
const userToken = '<USER_TOKEN>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Realización de una llamada

Para crear e iniciar una llamada, utilice una de las API en `callAgent` y proporcione un usuario que haya creado mediante el SDK de identidades de Communication Services.

La creación y el inicio de la llamada son sincrónicos. La instancia `call` le permite suscribirse a eventos de llamada.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Realización de una llamada 1:n a un usuario o RTC

Para llamar a otro usuario de Communication Services, invoque el método `startCall` en `callAgent` y pase la instancia de `CommunicationUserIdentifier` que ha [creado con la biblioteca de administración de Communication Services](../../../access-tokens.md).

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
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
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

### <a name="place-a-11-call-with-video-camera"></a>Realización de una llamada 1:1 con videocámara

> [!IMPORTANT]
> Actualmente, no puede haber más de una secuencia de vídeo local saliente.

Para realizar una llamada de vídeo, tiene que enumerar las cámaras mediante el método `getCameras()` de `deviceManager`.

Después de seleccionar una cámara, úsela para construir una instancia de `LocalVideoStream`. Páselo en `videoOptions` como un elemento de la matriz `localVideoStream` al método `startCall`.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const call = callAgent.startCall([userCallee], placeCallOptions);
```

- Cuando la llamada se conecta, iniciará automáticamente el envío de una secuencia de vídeo desde la cámara seleccionada hasta los demás participantes. Esto también se aplica a las opciones de vídeo de `Call.Accept()` y `CallAgent.join()`.


### <a name="join-a-group-call"></a>Unión a una llamada grupal

> [!NOTE]
> El parámetro `groupId` se considera metadatos del sistema y Microsoft puede usarlo para las operaciones necesarias para ejecutar el sistema. No incluya datos personales en el valor `groupId`. Microsoft no trata este parámetro como datos personales y su contenido puede ser visible para los empleados de Microsoft o almacenarse a largo plazo.
>
> El parámetro `groupId` requiere que los datos estén en formato GUID. Se recomienda usar GUID generados de forma aleatoria que no se consideran datos personales en los sistemas.
>

Para iniciar una llamada grupal nueva o unirse a una en curso, use el método `join` y pase un objeto con una propiedad `groupId`. El valor `groupId` debe ser un GUID.

```js

const context = { groupId: '<GUID>'}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Unirse a una reunión de Teams
> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión beta del SDK web de llamada de ACS.

Para unirse a una reunión de Teams, use el método `join` y pase un vínculo de reunión o las coordenadas de una reunión.

Unirse mediante un vínculo de reunión:

```js
const locator = { meetingLink: '<MEETING_LINK>'}
const call = callAgent.join(locator);
```

Unirse mediante las coordenadas de reunión:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Recepción de una llamada entrante

La instancia de `callAgent` emite un evento `incomingCall` cuando la identidad que ha iniciado sesión recibe una llamada entrante. Para escuchar este evento, suscríbase mediante una de estas opciones:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
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
callAgentInstance.on('incomingCall', incomingCallHander);
```

El evento `incomingCall` incluye una instancia de `incomingCall` que puede aceptar o rechazar.

Al iniciar o aceptar una llamada o al unirse a ella con el vídeo encendido, si otro proceso utiliza el dispositivo de cámara de vídeo especificado o si este dispositivo está deshabilitado en el sistema, la llamada se iniciará con el vídeo apagado y aparecerá un diagnóstico de llamada cameraStartFailed: true.

Consulte la sección Diagnóstico de llamada para ver cómo controlar este diagnóstico de llamada.

## <a name="manage-calls"></a>Administración de llamadas

Durante una llamada, puede tener acceso a las propiedades de llamada y administrar la configuración de vídeo y audio.

### <a name="check-call-properties"></a>Comprobación de las propiedades de llamada

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




### <a name="mute-and-unmute"></a>Silencio y reactivación del sonido

Para silenciar o reactivar el sonido del punto de conexión local, puede usar las API asincrónicas `mute` y `unmute`:

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Inicio y detención del envío de vídeo local

Para iniciar un vídeo, tiene que enumerar las cámaras con el método `getCameras` en el objeto `deviceManager`. A continuación, cree una nueva instancia de `LocalVideoStream` con la cámara deseada y luego pase el objeto `LocalVideoStream` al método `startVideo`:

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

Una vez que logre empezar a enviar vídeo, se agregará una instancia de `LocalVideoStream` a la colección `localVideoStreams` en una instancia de llamada.

```js
call.localVideoStreams[0] === localVideoStream;
```

Para detener el vídeo local, pase la instancia de `localVideoStream` disponible en la colección `localVideoStreams`:

```js
await call.stopVideo(localVideoStream);
// or
await call.stopVideo(call.localVideoStreams[0]);
```
Tenga en cuenta que hay 4 API en las que puede pasar una instancia localVideoStream para iniciar el vídeo en una llamada: API callAgent.startCall(), API callAgent.join(), API call.accept() y API call.startVideo(). En el caso de la API call.stopVideo(), debe pasar la misma instancia localVideoStream que pasó en esas 4 API.

Puede cambiar a un dispositivo de cámara distinto mientras se envía vídeo si invoca `switchSource` en una instancia de `localVideoStream`:

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

Si otro proceso usa el dispositivo de vídeo especificado o si este dispositivo está deshabilitado en el sistema:
- Mientras está en una llamada, si el vídeo está desactivado y lo inicia mediante la API call.startVideo(), esta API se iniciará con un error SourceUnavailableError y aparecerá un diagnóstico de llamada cameraStartFiled: true.
- Una llamada a la API localVideoStream.switchSource() hará que aparezca un diagnóstico de llamada cameraStartFailed: true.
Consulte la sección Diagnóstico de llamada para ver cómo controlar el diagnóstico de llamada.

## <a name="manage-remote-participants"></a>Administración de participantes remotos

El tipo `RemoteParticipant` representa a todos los participantes remotos, los que están disponibles a través de la colección `remoteParticipants` en una instancia de llamada.

### <a name="list-the-participants-in-a-call"></a>Enumeración de los participantes en una llamada

La colección `remoteParticipants` devuelve una lista de los participantes remotos en una llamada:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
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

## <a name="render-remote-participant-video-streams"></a>Representación de secuencias de vídeo de participantes remotos

Para una lista de las secuencias de vídeo y de las secuencias de uso compartido de pantalla de participantes remotos, revise las colecciones `videoStreams`:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Para representar `RemoteVideoStream`, se debe suscribir a su evento `isAvailableChanged`. Si la propiedad `isAvailable` cambia a `true`, un participante remoto envía una secuencia. Cuando esto suceda, cree una instancia de `VideoStreamRenderer` y, luego, cree una instancia de `VideoStreamRendererView` nueva a través del método `createView` asincrónico.  Después puede adjuntar `view.target` a cualquier elemento de la interfaz de usuario.

Cada vez que cambie la disponibilidad de una secuencia remota, puede elegir destruir todo el elemento `VideoStreamRenderer`, un elemento `VideoStreamRendererView` específico o mantenerlos, pero esto hará que el fotograma del vídeo se vea en blanco.

```js
subscribeToRemoteVideoStream = async (remoteVideoStream) => {
    // Create a video stream renderer for the remote video stream.
    let videoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    let view;
    const remoteVideoContainer = document.getElementById('remoteVideoContainer');
    const renderVideo = async () => {
        try {
            // Create a renderer view for the remote video stream.
            view = await videoStreamRenderer.createView();
            // Attach the renderer view to the UI.
            remoteVideoContainer.appendChild(view.target);
        } catch (e) {
            console.warn(`Failed to createView, reason=${e.message}, code=${e.code}`);
        }   
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        // Participant has switched video on.
        if (remoteVideoStream.isAvailable) {
            await renderVideo();

        // Participant has switched video off.
        } else {
            if (view) {
                view.dispose();
                view = undefined;
            }
        }
    });

    // Participant has video on initially.
    if (remoteVideoStream.isAvailable) {
        await renderVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Propiedades de secuencias de vídeo remotas

Las secuencias de vídeo remotas tienen las propiedades siguientes:

- `id`: el identificador de una secuencia de vídeo remota.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: puede ser `Video` o `ScreenSharing`.

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: si el punto de conexión del participante remoto envía secuencias de manera activa.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>Métodos y propiedades de VideoStreamRenderer
Cree una instancia de `VideoStreamRendererView` que se pueda adjuntar en la interfaz de usuario de la aplicación para representar la secuencia de vídeo remota, usar el método `createView()` asincrónico, se resuelve cuando la secuencia está lista para representarse y devuelve un objeto con la propiedad `target` que representa el elemento `video` que se puede anexar en cualquier parte del árbol DOM.

  ```js
  await videoStreamRenderer.createView()
  ```

Elimine el `videoStreamRenderer` y todas las instancias de `VideoStreamRendererView` asociadas.

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>Métodos y propiedades de VideoStreamRenderer

Cuando crea un objeto `VideoStreamRendererView`, puede especificar las propiedades `scalingMode` y `isMirrored`. `scalingMode` puede ser `Stretch`, `Crop` o `Fit`. Si se especifica `isMirrored`, la secuencia representada se voltea verticalmente.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Toda instancia de `VideoStreamRendererView` determinada tiene una propiedad `target` que representa la superficie de representación. Adjunte esta propiedad en la interfaz de usuario de la aplicación:

```js
htmlElement.appendChild(view.target);
```

Puede actualizar `scalingMode` mediante la llamada al método `updateScalingMode`:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Administración de dispositivos

En `deviceManager`, puede enumerar dispositivos locales que puedan transmitir secuencias de audio y vídeo en una llamada. También puede usarlo para solicitar permiso para acceder a los micrófonos y las cámaras del dispositivo local.

Puede llamar al método `callClient.getDeviceManager()` para acceder a `deviceManager`.

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Obtención de dispositivos locales

Para acceder a los dispositivos locales, puede usar métodos de enumeración en `deviceManager`. La enumeración es una acción asincrónica

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Establecimiento del micrófono o el altavoz predeterminados

En `deviceManager`, puede establecer un dispositivo predeterminado que usará para iniciar una llamada. Si no se establecen los valores predeterminados del cliente, Communication Services utiliza los valores predeterminados del sistema operativo.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Vista previa de la cámara local

Puede usar `deviceManager` y `VideoStreamRenderer` para empezar a representar secuencias desde la cámara local. Esta secuencia no se enviará a los demás participantes, porque es una fuente de vista previa local.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Solicitud de permiso para acceder a la cámara o micrófono

Pida al usuario que conceda permisos de cámara y micrófono:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Esto se resuelve con un objeto que indica si se concedieron los permisos de `audio` y `video`:

```js
console.log(result.audio);
console.log(result.video);
```
#### <a name="notes"></a>Notas
- El evento "videoDevicesUpdated" se activa cuando los dispositivos de vídeo se conectan y desconectan.
- El evento "audioDevicesUpdated" se activa cuando los dispositivos de audio se conectan.
- Cuando se crea DeviceManager, al principio no conoce ningún dispositivo si aún no se han concedido permisos y, por tanto, inicialmente sus listas de dispositivos están vacías. Si llamamos a la API DeviceManager.askPermission(), se le pedirá al usuario acceso al dispositivo y, si el usuario hace clic en "permitir" para conceder el acceso, el administrador de dispositivos sabrá de los dispositivos del sistema, actualizará sus listas de dispositivos y emitirá los eventos "audioDevicesUpdated" y "videoDevicesUpdated". Supongamos que luego actualizamos la página y creamos el administrador de dispositivos; el administrador de dispositivos podrá obtener información sobre los dispositivos porque el usuario ya concedió acceso, por lo que inicialmente tendrá sus listas de dispositivos completas y no emitirá eventos "audioDevicesUpdated" ni "videoDevicesUpdated".
- Chrome para Android, Safari para iOS ni Safari para macOS admiten la selección o enumeración de hablantes.

## <a name="call-feature-extensions"></a>Extensiones de características de llamada

### <a name="record-calls"></a>Registro de llamadas
> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión beta del SDK web de llamada de ACS.

La grabación de llamadas es una característica extendida de la API `Call` principal. Primero debe obtener el objeto de API de la característica de grabación:

```js
const callRecordingApi = call.api(Features.Recording);
```

Después, para comprobar si se está grabando la llamada, inspeccione la propiedad `isRecordingActive` de `callRecordingApi`. Devuelve `Boolean`.

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

También puede suscribirse a los cambios de grabación:

```js
const isRecordingActiveChangedHandler = () => {
    console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);

```

### <a name="transfer-calls"></a>Transferencia de llamadas
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

### <a name="dominant-speakers-of-a-call"></a>Hablantes dominantes de una llamada
> [!NOTE]
> Esta API se ofrece a los desarrolladores como versión preliminar y puede cambiar en función de los comentarios que recibamos. No utilice esta API en un entorno de producción. Para usar esta API, utilice la versión beta del SDK web de llamada de ACS.

Los hablantes dominantes para una llamada son una característica extendida de la API `Call` principal y le permite obtener una lista de los hablantes activos de la llamada. 

Se trata de una lista clasificada en la que el primer elemento representa el último hablante activo de la llamada, y así sucesivamente.

Si desea obtener los hablantes dominantes de una llamada, primero debe obtener el objeto de API de la característica de hablantes dominantes de una llamada:

```js
const callDominantSpeakersApi = call.api(Features.CallDominantSpeakers);
```

Luego, llame a `dominantSpeakers` para obtener la lista de los hablantes dominantes. Tiene un tipo de `DominantSpeakersInfo` que tiene estos miembros:

- `speakersList` contiene la lista de los hablantes dominantes clasificados de la llamada. Se representan con su id. de participante.
- `timestamp` es la hora de actualización más reciente para los hablantes dominantes en la llamada.

```js
let dominantSpeakers: DominantSpeakersInfo = callDominantSpeakersApi.dominantSpeakers;
```
Además, puede suscribirse al evento `dominantSpeakersChanged` para saber cuándo cambió la lista de hablantes dominantes.

```js
const dominantSpeakersChangedHandler = () => {
    // Get the most up to date list of dominant speakers
    let dominantSpeakers = callDominantSpeakersApi.dominantSpeakers;
};
callDominantSpeakersApi.api(Features.CallDominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);
``` 
#### <a name="handle-the-dominant-speakers-video-streams"></a>Control de las secuencias de vídeo del hablante dominante

La aplicación puede usar la característica `DominantSpeakers` para representar una o varias secuencias de vídeo del hablante dominante y seguir actualizando la interfaz de usuario cada vez que se actualiza la lista de hablantes dominantes. Esto se puede lograr con el ejemplo de código siguiente.

```js
// RemoteParticipant obj representation of the dominant speaker
let dominantRemoteParticipant: RemoteParticipant;
// It is recommended to use a map to keep track of a stream's associated renderer
let streamRenderersMap: new Map<RemoteVideoStream, VideoStreamRenderer>();

function getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier) {
    let dominantRemoteParticipant: RemoteParticipant;
    switch(dominantSpeakerIdentifier.kind) {
        case 'communicationUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as CommunicationUserIdentifier).communicationUserId === dominantSpeakerIdentifier.communicationUserId
            });
            break;
        }
        case 'microsoftTeamsUser': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as MicrosoftTeamsUserIdentifier).microsoftTeamsUserId === dominantSpeakerIdentifier.microsoftTeamsUserId
            });
            break;
        }
        case 'unknown': {
            dominantRemoteParticipant = currentCall.remoteParticipants.find(rm => {
                return (rm.identifier as UnknownIdentifier).id === dominantSpeakerIdentifier.id
            });
            break;
        }
    }
    return dominantRemoteParticipant;
}
// Handler function for when the dominant speaker changes
const dominantSpeakersChangedHandler = async () => {
    // Get the new dominant speaker's identifier
    const newDominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];

     if (newDominantSpeakerIdentifier) {
        // Get the remote participant object that matches newDominantSpeakerIdentifier
        const newDominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(newDominantSpeakerIdentifier);

        // Create the new dominant speaker's stream renderers
        const streamViews = [];
        for (const stream of newDominantRemoteParticipant.videoStreams) {
            if (stream.isAvailable && !streamRenderersMap.get(stream)) {
                const renderer = new VideoStreamRenderer(stream);
                streamRenderersMap.set(stream, renderer);
                const view = await videoStreamRenderer.createView();
                streamViews.push(view);
            }
        }

        // Remove the old dominant speaker's video streams by disposing of their associated renderers
        if (dominantRemoteParticipant) {
            for (const stream of dominantRemoteParticipant.videoStreams) {
                const renderer = streamRenderersMap.get(stream);
                if (renderer) {
                    streamRenderersMap.delete(stream);
                    renderer.dispose();
                }
            }
        }

        // Set the new dominant remote participant obj
        dominantRemoteParticipant = newDominantRemoteParticipant

        // Render the new dominant remote participant's streams
        for (const view of streamViewsToRender) {
            htmlElement.appendChild(view.target);
        }
     }
};

// When call is disconnected, set the dominant speaker to undefined
currentCall.on('stateChanged', () => {
    if (currentCall === 'Disconnected') {
        dominantRemoteParticipant = undefined;
    }
});

const dominantSpeakerIdentifier = currentCall.api(Features.DominantSpeakers).dominantSpeakers.speakersList[0];
dominantRemoteParticipant = getRemoteParticipantForDominantSpeaker(dominantSpeakerIdentifier);
currentCall.api(Features.DominantSpeakers).on('dominantSpeakersChanged', dominantSpeakersChangedHandler);

subscribeToRemoteVideoStream = async (stream: RemoteVideoStream, participant: RemoteParticipant) {
    let renderer: VideoStreamRenderer;

    const displayVideo = async () => {
        renderer = new VideoStreamRenderer(stream);
        streamRenderersMap.set(stream, renderer);
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }

    stream.on('isAvailableChanged', async () => {
        if (dominantRemoteParticipant !== participant) {
            return;
        }

        renderer = streamRenderersMap.get(stream);
        if (stream.isAvailable && !renderer) {
            await displayVideo();
        } else {
            streamRenderersMap.delete(stream);
            renderer.dispose();
        }
    });

    if (dominantRemoteParticipant !== participant) {
        return;
    }

    renderer = streamRenderersMap.get(stream);
    if (stream.isAvailable && !renderer) {
        await displayVideo();
    }
}
```
### <a name="call-diagnostics"></a>Diagnóstico de llamada
El diagnóstico de llamada es una características extendida de la API `Call` principal que le permite diagnosticar una llamada activa.

```js
const callDiagnostics = call.api(Features.Diagnostics);
```

Están disponibles los diagnósticos siguientes para los usuarios:

| Tipo    | Nombre                           | Descripción                                                     | Valores posibles                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      | Casos de uso                                                                       |
| ------- | ------------------------------ | --------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------- |
| Red | noNetwork                      | No hay red disponible.                                  | - Se establece en `True` cuando no se puede iniciar una llamada porque no hay red disponible. <br/> - Se establece en `False` cuando hay candidatos ICE.                                                                                                                                                                                                                                                                                                                                                      | El dispositivo no está conectado a una red.                                           |
| Red | networkRelaysNotReachable      | Problemas con una red.                                        | - Se establece en `True` cuando la red tiene alguna restricción que no le permite acceder a las retransmisiones de ACS. <br/> - Se establece en `False` al hacer una llamada nueva.                                                                                                                                                                                                                                                                                                                                                    | Durante una llamada cuando la señal WiFi es intermitente.                             |  |
| Red | networkReconnect               | Se perdió la conexión y nos estamos reconectando a la red. | - Se establece en `Poor` cuando se pierde la conectividad del transporte de medios.                                                                                                                                 <br/> - Se establece en `Bad` cuando se desconecta la red. <br/> - Se establece en `Good` cuando se desconecta una sesión nueva.                                                                                                                                                                                           | Ancho de banda reducido, sin Internet                                                      |
| Red | networkReceiveQuality          | Indicador relacionado con la calidad de la secuencia entrante.                 | - Se establece en `Bad` cuando hay un problema grave al recibir la secuencia. calidad                                                                                                                           <br/> - Se establece en `Poor` cuando hay un problema leve al recibir la secuencia. calidad                                                                                                                           <br/> - Se establece en `Good` cuando no hay ningún problema al recibir la secuencia. | Ancho de banda bajo                                                                   |
| Medios   | noSpeakerDevicesEnumerated     | No hay ningún dispositivo de salida de audio (altavoz) en el sistema del usuario. | - Se establece en `True` cuando no hay ningún dispositivo de altavoz en el sistema y se admite la selección del altavoz. <br/> - Se establece en `False` cuando hay al menos 1 dispositivo de altavoz en el sistema y se admite la selección del altavoz.                                                                                                                                                                                                                                                                                | Todos los altavoces están desconectados                                                      |
| Medios   | speakingWhileMicrophoneIsMuted | El usuario habla mientras está silenciado.                                   | - Se establece en `True` cuando el micrófono local está silenciado y el usuario local está hablando. <br/> - Se establece en `False` cuando el usuario local deja de hablar o activa el sonido del micrófono. <br/> * Nota: A día de hoy, esto todavía no se admite en Safari, porque los ejemplos de nivel de audio se tomaron de webrtc. stats.                                                                                                                                                                                                     | Durante una llamada, habla mientras tiene silenciado el micrófono.                          |
| Medios   | noMicrophoneDevicesEnumerated  | No hay dispositivos de captura de audio (micrófono) en el sistema del usuario.      | - Se establece en `True` cuando no hay dispositivos de micrófono en el sistema. <br/> - Se establece en `False` cuando hay al menos 1 dispositivo de micrófono en el sistema.                                                                                                                                                                                                                                                                                                                                                 | Todos los micrófonos se desconectan durante la llamada.                                  |
| Medios   | cameraFreeze                   | La cámara deja de generar fotogramas durante más de 5 segundos.          | - Se establece en `True` cuando se detiene la secuencia de vídeo local. Esto significa que el lado remoto ve el vídeo inmovilizado en su pantalla o que los participantes remotos no ven su vídeo en pantalla. <br/> - Se establece en `False` cuando finaliza la inmovilización y los usuarios pueden ver el vídeo de manera normal.                                                                                                                                                                                         | La cámara paró durante la llamada o una red insuficiente hizo que se inmovilizara. |
| Medios   | cameraStartFailed              | Error genérico de la cámara.                                         | - Se establece en `True` cuando no se puede iniciar el envío de vídeo local porque es posible que el dispositivo de cámara esté deshabilitado en el sistema o si lo usa otro proceso. <br/> - Se establece en `False` cuando el dispositivo de cámara seleccionado envía vídeo local correctamente. .                                                                                                                                                                                                                                           | Errores de la cámara                                                                 |
| Medios   | cameraStartTimedOut            | Escenario común en el que la cámara está en mal estado.                   | - Se establece en `True` cuando se agotó el tiempo de espera del dispositivo de cámara para empezar a enviar la secuencia de vídeo. <br/> - Se establece en `False` cuando el dispositivo de cámara seleccionado vuelve a enviar vídeo local correctamente.                                                                                                                                                                                                                                                                                                                                 | Errores de la cámara                                                                 |
| Medios   | microphoneNotFunctioning       | El micrófono no funciona.                                  | - Se establece en `True` cuando no se puede iniciar el envío de la secuencia de audio local porque es posible que el dispositivo de micrófono esté deshabilitado en el sistema o si lo usa otro proceso. Este UFD tarda unos 10 segundos en activarse. <br/> - Se establece en `False` cuando el micrófono empieza a enviar nuevamente la secuencia de audio de manera correcta.                                                                                                                                                                                    | No hay micrófonos disponibles, el acceso al micrófono está deshabilitado en el sistema.                |
| Medios   | microphoneMuteUnexpectedly     | Se silenció el micrófono.                                             | - Se establece en `True` cuando el micrófono se silencia de manera inesperada. <br/> - Se establece en `False` cuando el micrófono empieza a enviar la secuencia de audio correctamente.                                                                                                                                                                                                                                                                                                                                                     | El micrófono está silenciado desde el sistema.                                            |
| Medios   | screenshareRecordingDisabled   | Las preferencias en Configuración deniegan el uso compartido de la pantalla del sistema.     | - Se establece en `True`cuando la configuración del sistema (uso compartido) deniega el permiso para el uso compartido de la pantalla. <br/> - Se establece en `False` al adquirir correctamente la secuencia. <br/> Nota: Este diagnóstico solo funciona en macOS.Chrome.                                                                                                                                                                                                                                                                                               | La grabación de pantalla está deshabilitada en Configuración.                                       |
| Medios   | microphonePermissionDenied     | El volumen del dispositivo está bajo o casi sin sonido en macOS. | - Se establece en `True`cuando la configuración del sistema (audio) deniega el permiso de audio. <br/> - Se establece en `False` al adquirir correctamente la secuencia. <br/> Nota: Este diagnóstico solo funciona en macOS.                                                                                                                                                                                                                                                                                                                | Los permisos de micrófono están deshabilitados en Configuración.                            |
| Medios   | cameraPermissionDenied         | Se denegaron los permisos de cámara en la configuración.                     | - Se establece en `True`cuando la configuración del sistema (video) deniega el permiso de cámara. <br/> - Se establece en `False` al adquirir correctamente la secuencia. <br> Nota: Este diagnóstico solo funciona en macOS.Chrome.                                                                                                                                                                                                                                                                                                          | Los permisos de cámara están deshabilitados en Configuración.                                |

- Suscríbase al evento `diagnosticChanged` para supervisar cuándo cambia cualquier diagnóstico de llamada.
```js
/**
 *  Each diagnostic has the following data:
 * - diagnostic is the type of diagnostic, e.g. NetworkSendQuality, DeviceSpeakWhileMuted, etc...
 * - value is DiagnosticQuality or DiagnosticFlag:
 *     - DiagnosticQuality = enum { Good = 1, Poor = 2, Bad = 3 }.
 *     - DiagnosticFlag = true | false.
 * - valueType = 'DiagnosticQuality' | 'DiagnosticFlag'
 * - mediaType is the media type associated with the event, e.g. Audio, Video, ScreenShare. These are defined in `CallDiagnosticEventMediaType`.
 */
const diagnosticChangedListener = (diagnosticInfo: NetworkDiagnosticChangedEventArgs | MediaDiagnosticChangedEventArgs) => {
    console.log(`Diagnostic changed: ` +
        `Diagnostic: ${diagnosticInfo.diagnostic}` +
        `Value: ${diagnosticInfo.value}` + 
        `Value type: ${diagnosticInfo.valueType}` +
        `Media type: ${diagnosticInfo.mediaType}` +

    if (diagnosticInfo.valueType === 'DiagnosticQuality') {
        if (diagnosticInfo.value === DiagnosticQuality.Bad) {
            console.error(`${diagnosticInfo.diagnostic} is bad quality`);

        } else if (diagnosticInfo.value === DiagnosticQuality.Poor) {
            console.error(`${diagnosticInfo.diagnostic} is poor quality`);
        }

    } else if (diagnosticInfo.valueType === 'DiagnosticFlag') {
        if (diagnosticInfo.value === true) {
            console.error(`${diagnosticInfo.diagnostic}`);
        }
    }
};

callDiagnostics.network.on('diagnosticChanged', diagnosticChangedListener);
callDiagnostics.media.on('diagnosticChanged', diagnosticChangedListener);
```

- Obtenga los valores de diagnóstico de llamada más reciente generados. Si no se definió un diagnóstico es porque nunca se generó.
```js
const latestNetworkDiagnostics = callDiagnostics.network.getLatest();
    
console.log(`noNetwork: ${latestNetworkDiagnostics.noNetwork.value}, ` +
    `value type = ${latestNetworkDiagnostics.noNetwork.valueType}`);
            
console.log(`networkReconnect: ${latestNetworkDiagnostics.networkReconnect.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReconnect.valueType}`);
            
console.log(`networkReceiveQuality: ${latestNetworkDiagnostics.networkReceiveQuality.value}, ` +
    `value type = ${latestNetworkDiagnostics.networkReceiveQuality.valueType}`);


const latestMediaDiagnostics = callDiagnostics.media.getLatest();
    
console.log(`speakingWhileMicrophoneIsMuted: ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.value}, ` +
    `value type = ${latestMediaDiagnostics.speakingWhileMicrophoneIsMuted.valueType}`);
            
console.log(`cameraStartFailed: ${latestMediaDiagnostics.cameraStartFailed.value}, ` +
    `value type = ${latestMediaDiagnostics.cameraStartFailed.valueType}`);
            
console.log(`microphoneNotFunctioning: ${latestMediaDiagnostics.microphoneNotFunctioning.value}, ` +
    `value type = ${latestMediaDiagnostics.microphoneNotFunctioning.valueType}`);
```
## <a name="releasing-resources"></a>Liberación de recursos
1. Cómo liberar correctamente los recursos al finalizar una llamada:
    - Cuando finalice la llamada, el SDK finalizará las sesiones de señalización y medios y lo dejará con una instancia de la llamada que contiene el último estado de la misma. Todavía puede comprobar callEndReason. Si la aplicación no contiene la referencia a la instancia de llamada, el recolector de elementos no utilizados de JavaScript limpiará todo para que, en términos de consumo de memoria, la aplicación debe volver al estado inicial de antes de la llamada.
2. Qué tipos de recursos son de larga duración (vigencia de la aplicación) frente los que son de corta duración (duración de la llamada):
    - Los recursos siguientes se consideran "de larga duración": puede crearlos y mantener las referencias a ellos durante mucho tiempo, son muy ligeros en términos de consumo de recursos (memoria), por lo que no afectarán el rendimiento:
        - CallClient
        - CallAgent
        - DeviceManager
    - Los recursos siguientes se consideran "de corta duración" y son los que desempeñan algún rol en la llamada misma, emiten eventos a la aplicación o interactúan con dispositivos multimedia locales. Estos consumirán más CPU y memoria pero, una vez que finalice la llamada, el SDK los limpiará completamente y liberará el recurso:
        - Call, ya que es el recurso que contiene el estado real de la llamada (tanto la señalización como los medios).
        - RemoteParticipants, que representa los participantes remotos de la llamada.
        - VideoStreamRenderer con sus VideoStreamRendererViews, que controlan la representación de vídeo.