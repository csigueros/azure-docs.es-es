---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 4c95736394cd112daa48f3bfa6a47ae4ba0f147d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699414"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

## <a name="manage-devices"></a>Administración de dispositivos
Para empezar a usar vídeo con llamadas, debe saber cómo administrar dispositivos. Los dispositivos permiten controlar lo que transmite audio y vídeo a la llamada.

`DeviceManager` permite enumerar los dispositivos locales que se pueden usar en una llamada para transmitir secuencias de audio o vídeo. También permite solicitar permiso a un usuario para acceder al micrófono o a la cámara. Puede acceder a `deviceManager` en el objeto `callClient`.

```swift
self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Enumeración de los dispositivos locales

Para acceder a los dispositivos locales, puede usar métodos de enumeración en el administrador de dispositivos. La enumeración es una acción sincrónica.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras // [VideoDeviceInfo, VideoDeviceInfo...]
```

### <a name="get-a-local-camera-preview"></a>Obtención de una vista previa de la cámara local

Puede usar `Renderer` para empezar a representar una secuencia desde su cámara local. Esta secuencia no se enviará a los demás participantes, porque es una fuente de vista previa local. Se trata de una acción asincrónica.

```swift
let camera: VideoDeviceInfo = self.deviceManager!.cameras.first!
let localVideoStream = LocalVideoStream(camera: camera)
let localRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream)
self.view = try! localRenderer.createView()
```

### <a name="get-local-camera-preview-properties"></a>Obtención de las propiedades de la vista previa de la cámara local

El representador tiene un conjunto de propiedades y métodos que le permiten controlar la representación.

```swift
// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = VideoStreamRenderer(localVideoStream:localVideoStream)
let remoteRenderer = VideoStreamRenderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [VideoStreamRendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer!.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.fit))

// [Synchronous] dispose rendering view
localRenderer.dispose()
```

## <a name="place-a-11-call-with-video"></a>Realización de una llamada 1:1 con vídeo
Para obtener una instancia del administrador de dispositivos, consulte la sección acerca de la [administración de dispositivos](#manage-devices).

```swift
let firstCamera = self.deviceManager!.cameras.first
self.localVideoStreams = [LocalVideoStream]()
self.localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let videoOptions = VideoOptions(localVideoStreams: self.localVideoStreams!)

let startCallOptions = StartCallOptions()
startCallOptions.videoOptions = videoOptions

let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [callee], options: startCallOptions) { (call, error) in
    if error == nil {
        print("Successfully started outgoing video call")
        self.call = call
    } else {
        print("Failed to start outgoing video call")
    }
}
```

## <a name="render-remote-participant-video-streams"></a>Representación de secuencias de vídeo de participantes remotos

Los participantes remotos pueden iniciar el uso compartido de pantalla o vídeo durante una llamada.

### <a name="handle-video-sharing-or-screen-sharing-streams-of-remote-participants"></a>Control de las transmisiones de uso compartido de vídeo o de pantalla de participantes remotos

Para enumerar las transmisiones de los participantes remotos, inspeccione las colecciones `videoStreams`.

```swift
var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]
```

### <a name="get-remote-video-stream-properties"></a>Obtención de las propiedades de las transmisiones de vídeo remotas

```swift
var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'
var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available
var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream
```

### <a name="render-remote-participant-streams"></a>Representación de las transmisiones de participantes remotos

Para iniciar la representación de las transmisiones de los participantes remotos, use el código siguiente.

```swift
let renderer = VideoStreamRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView = renderer?.createView(withOptions: CreateViewOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)
```

### <a name="get-remote-video-renderer-methods-and-properties"></a>Obtención de los métodos y propiedades del representador de vídeo

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```