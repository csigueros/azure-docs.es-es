---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 97e28db07e950fde42033ef57c76b2734b73f7cc
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699418"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

## <a name="device-management"></a>Administración de dispositivos
Para empezar a usar vídeo con llamadas, debe saber cómo administrar dispositivos. Los dispositivos permiten controlar lo que transmite audio y vídeo a la llamada.

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
- Ni Chrome para Android ni Safari para iOS ni Safari para macOS admiten la selección o enumeración de hablantes.

## <a name="start-and-stop-sending-local-video"></a>Inicio y detención del envío de vídeo local

Para iniciar un vídeo, tiene que enumerar las cámaras con el método `getCameras` en el objeto `deviceManager`. Luego, cree una instancia de `LocalVideoStream` con la cámara deseada y luego pase el objeto `LocalVideoStream` al método `startVideo` de un objeto de llamada existente:

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

Hay cuatro métodos en los que puede pasar una instancia de `localVideoStream` para iniciar el vídeo en una llamada, `callAgent.startCall()`, `callAgent.join()`, `call.accept()` y `call.startVideo()`. Para usar `call.stopVideo()`, debe pasar la misma instancia de `localVideoStream` que pasó al método original usado para iniciar el vídeo.

Puede cambiar a un dispositivo de cámara distinto mientras se envía vídeo si invoca `switchSource` en una instancia de `localVideoStream`:

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

Si otro proceso usa el dispositivo de vídeo especificado o si este dispositivo está deshabilitado en el sistema:
- Mientras está en una llamada, si el vídeo está desactivado y lo inicia mediante `call.startVideo()`, este método se iniciará con `SourceUnavailableError` y `cameraStartFiled` se establecerá en true.
- Una llamada al método `localVideoStream.switchSource()` hará que `cameraStartFailed` se establezca en true.
Nuestra guía de diagnóstico de llamadas proporciona información adicional sobre cómo diagnosticar problemas relacionados con las llamadas.

## <a name="place-a-11-call-with-video-camera"></a>Realización de una llamada 1:1 con videocámara

> [!IMPORTANT]
> Actualmente,. solo se admite una secuencia de vídeo local saliente.

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

## <a name="videostreamrenderer-methods-and-properties"></a>Métodos y propiedades de VideoStreamRenderer
Cree una instancia de `VideoStreamRendererView` que se pueda adjuntar en la interfaz de usuario de la aplicación para representar la secuencia de vídeo remota, usar el método `createView()` asincrónico, se resuelve cuando la secuencia está lista para representarse y devuelve un objeto con la propiedad `target` que representa el elemento `video` que se puede anexar en cualquier parte del árbol DOM.

```js
await videoStreamRenderer.createView();
```

Elimine el `videoStreamRenderer` y todas las instancias de `VideoStreamRendererView` asociadas.
```js
videoStreamRenderer.dispose();
```

## <a name="videostreamrendererview-methods-and-properties"></a>Métodos y propiedades de VideoStreamRenderer

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
view.updateScalingMode('Crop');
```