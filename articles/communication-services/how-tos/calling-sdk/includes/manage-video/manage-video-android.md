---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 7d77857152ad381bb12e5bacc9889c10ca633948
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585209"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="device-management"></a>Administración de dispositivos
Para empezar a usar vídeo con llamadas, debe saber cómo administrar dispositivos. Los dispositivos permiten controlar lo que transmite audio y vídeo a la llamada.

`DeviceManager` permite enumerar los dispositivos locales que se pueden usar en una llamada para transmitir las secuencias de audio o vídeo. También permite solicitar permiso a un usuario para acceder a su micrófono y cámara mediante la API nativa del explorador.

Puede llamar al método `callClient.getDeviceManager()` para acceder a `deviceManager`.

```java
Context appContext = this.getApplicationContext();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```

### <a name="enumerate-local-devices"></a>Enumeración de los dispositivos locales

Para acceder a los dispositivos locales, puede usar métodos de enumeración en el Administrador de dispositivos. La enumeración es una acción sincrónica.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="local-camera-preview"></a>Vista previa de la cámara local

Puede usar `DeviceManager` y `Renderer` para empezar a representar secuencias desde la cámara local. Esta secuencia no se enviará a los demás participantes, porque es una fuente de vista previa local. Se trata de una acción asincrónica.

```java
VideoDeviceInfo videoDevice = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

LocalVideoStream currentVideoStream = new LocalVideoStream(videoDevice, appContext);

LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;

VideoOptions videoOptions = new VideoOptions(localVideoStreams);

RenderingOptions renderingOptions = new RenderingOptions(ScalingMode.Fit);
VideoStreamRenderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);

VideoStreamRendererView uiView = previewRenderer.createView(renderingOptions);

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="place-a-11-call-with-video-camera"></a>Realización de una llamada 1:1 con videocámara
> [!WARNING]
> Actualmente, solo se admite una secuencia de vídeo local saliente. Para realizar una llamada con vídeo, debe enumerar las cámaras locales mediante la API `deviceManager` `getCameras`.
Una vez que seleccione la cámara deseada, úsela para crear una instancia de `LocalVideoStream` y pásela a `videoOptions` como elemento de la matriz `localVideoStream` a un método `call`.
Una vez que la llamada se conecta, iniciará automáticamente el envío de una secuencia de vídeo desde la cámara seleccionada hasta los demás participantes.

> [!NOTE]
> Debido a cuestiones de privacidad, el vídeo no se compartirá con la llamada si no se muestra como una versión preliminar localmente.
Consulte [Versión preliminar de la cámara local](#local-camera-preview) para obtener más detalles.

```java
VideoDeviceInfo desiredCamera = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);

LocalVideoStream[] localVideoStreams = new LocalVideoStream[1];
localVideoStreams[0] = currentVideoStream;

VideoOptions videoOptions = new VideoOptions(localVideoStreams);

// Render a local preview of video so the user knows that their video is being shared
Renderer previewRenderer = new VideoStreamRenderer(currentVideoStream, appContext);
View uiView = previewRenderer.createView(new CreateViewOptions(ScalingMode.FIT));

// Attach the uiView to a viewable location on the app at this point
layout.addView(uiView);

CommunicationUserIdentifier[] participants = new CommunicationUserIdentifier[]{ new CommunicationUserIdentifier("<acs user id>") };

StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);

Call call = callAgent.startCall(context, participants, startCallOptions);
```

## <a name="start-and-stop-sending-local-video"></a>Inicio y detención del envío de vídeo local

Para iniciar un vídeo, tiene que enumerar las cámaras con la API `getCameraList` en el objeto `deviceManager`. Luego, cree una instancia de `LocalVideoStream` pasando la cámara deseada y pásela a la API `startVideo` como un argumento:

```java
VideoDeviceInfo desiredCamera = <get-video-device>; // See the `Enumerate local devices` topic above
Context appContext = this.getApplicationContext();

LocalVideoStream currentLocalVideoStream = new LocalVideoStream(desiredCamera, appContext);

VideoOptions videoOptions = new VideoOptions(currentLocalVideoStream);

Future startVideoFuture = call.startVideo(appContext, currentLocalVideoStream);
startVideoFuture.get();
```

Una vez que logre empezar a enviar vídeo, se agregará una instancia de `LocalVideoStream` a la colección `localVideoStreams` en la instancia de llamada.

```java
List<LocalVideoStream> videoStreams = call.getLocalVideoStreams();
LocalVideoStream currentLocalVideoStream = videoStreams.get(0); // Please make sure there are VideoStreams in the list before calling get(0).
```

Para detener el vídeo local, pase la instancia `LocalVideoStream` disponible en la colección `localVideoStreams`:

```java
call.stopVideo(appContext, currentLocalVideoStream).get();
```

Puede cambiar a un dispositivo de cámara distinto mientras se envía vídeo si invoca `switchSource` en una instancia de `LocalVideoStream`:
```java
currentLocalVideoStream.switchSource(source).get();
```

## <a name="render-remote-participant-video-streams"></a>Representación de secuencias de vídeo de participantes remotos

Para una lista de las secuencias de vídeo y de las secuencias de uso compartido de pantalla de participantes remotos, revise las colecciones `videoStreams`:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
RemoteParticipant remoteParticipant = remoteParticipants.get(0); // Please make sure there are remote participants in the list before calling get(0).

List<RemoteVideoStream> remoteStreams = remoteParticipant.getVideoStreams();
RemoteVideoStream remoteParticipantStream = remoteStreams.get(0); // Please make sure there are video streams in the list before calling get(0).

MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Para representar una `RemoteVideoStream` de un participante remoto, debe suscribirse a un evento `OnVideoStreamsUpdated`.

En el evento, el cambio de la propiedad `isAvailable` a true indica que el participante remoto está enviando una secuencia actualmente. Si es el caso, cree una nueva instancia de `Renderer` y, a continuación, cree una nueva instancia de `RendererView` con la API de `createView` asincrónica y adjunte `view.target` en cualquier parte de la interfaz de usuario de la aplicación.

Cada vez que cambia la disponibilidad de una secuencia remota, puede elegir destruir todo el representador, un `RendererView` específico o mantenerlos, pero esto hará que el fotograma del vídeo se vea en blanco.

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteParticipantStream, appContext);
VideoStreamRendererView uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.FIT));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Propiedades de secuencias de vídeo remotas
La secuencia de vídeo remota tiene un par de propiedades

* `Id`: id. de una secuencia de vídeo remota
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType`: puede ser "Video" o "ScreenSharing"
```java
MediaStreamType type = remoteVideoStream.getMediaStreamType();
```

* `isAvailable`: indica si el punto de conexión del participante remoto envía secuencias de manera activa
```java
boolean availability = remoteVideoStream.isAvailable();
```

### <a name="renderer-methods-and-properties"></a>Métodos y propiedades del representador
Objeto del representador que sigue a las API

* Cree una instancia de `VideoStreamRendererView` que se pueda adjuntar posteriormente en la interfaz de usuario de la aplicación para representar la secuencia de vídeo remota.
```java
// Create a view for a video stream
VideoStreamRendererView.createView()
```
* Eliminación del representador y de todos los elementos `VideoStreamRendererView` asociados a este representador. Se llamará cuando se hayan quitado todas las vistas asociadas de la interfaz de usuario.
```java
VideoStreamRenderer.dispose()
```

* `StreamSize`: tamaño (ancho y alto) de una secuencia de vídeo remota
```java
StreamSize renderStreamSize = VideoStreamRenderer.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```

### <a name="rendererview-methods-and-properties"></a>Métodos y propiedades de RendererView
Al crear un objeto `VideoStreamRendererView`, puede especificar las propiedades `ScalingMode` y `mirrored` que se aplicarán a esta vista: el modo de escalado puede ser "CROP" | "FIT".

```java
VideoStreamRenderer remoteVideoRenderer = new VideoStreamRenderer(remoteVideoStream, appContext);
VideoStreamRendererView rendererView = remoteVideoRenderer.createView(new CreateViewOptions(ScalingMode.Fit));
```

A continuación, el elemento RendererView creado se puede asociar a la interfaz de usuario de la aplicación mediante el siguiente fragmento de código:
```java
layout.addView(rendererView);
```

Posteriormente podrá actualizar el modo de escalado invocando la API `updateScalingMode` en el objeto RendererView con ScalingMode.CROP | ScalingMode.FIT como argumento.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.CROP)
```
