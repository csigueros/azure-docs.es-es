---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: c2deff9146d0f6ec28e3e37981bcb82d3e056947
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699421"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

> [!NOTE]
> Cuando la aplicación implementa delegados de eventos, tiene que mantener una referencia segura a los objetos que requieren suscripciones a eventos. Por ejemplo, cuando se devuelve un objeto `RemoteParticipant` al invocar el método `call.addParticipant` y la aplicación establece que el delegado escuche en `RemoteParticipantDelegate`, la aplicación debe contener una referencia segura al objeto `RemoteParticipant`. De lo contrario, si se recopila este objeto, el delegado generará una excepción irrecuperable cuando el SDK de llamadas intente invocar al objeto.

## <a name="place-an-outgoing-call"></a>Realización de una llamada saliente

Para crear e iniciar una llamada, debe llamar a una de las API en `CallAgent` y proporcionar la identidad de Communication Services de un usuario que haya aprovisionado mediante el SDK de administración de Communication Services.

La creación y el inicio de la llamada son sincrónicos. Recibirá una instancia de la llamada que le permitirá suscribirse a todos los eventos de la llamada.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Realice una llamada de uno a uno a un usuario o una llamada de uno a varios con usuarios y RTC

```swift
let callees = [CommunicationUser(identifier: 'UserId')]
self.callAgent?.startCall(participants: callees, options: StartCallOptions()) { (call, error) in
     if error == nil {
         print("Successfully started outgoing call")
         self.call = call
     } else {
         print("Failed to start outgoing call")
     }
}
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Realice una llamada de uno a varios con usuarios y RTC
Para realizar la llamada con RTC, debe especificar el número de teléfono adquirido con Communication Services.

```swift
let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier('UserId')
self.callAgent?.startCall(participants: [pstnCallee, callee], options: StartCallOptions()) { (groupCall, error) in
     if error == nil {
         print("Successfully started outgoing call to multiple participants")
         self.call = groupCall
     } else {
         print("Failed to start outgoing call to multiple participants")
     }
}
```

## <a name="join-a-group-call"></a>Unión a una llamada grupal
Para unirse a una llamada, debe llamar a una de las API en `CallAgent`.

```swift
let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string")!)
self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions()) { (call, error) in
    if error == nil {
        print("Successfully joined group call")
        self.call = call
    } else {
        print("Failed to join group call")
    }
}
```

## <a name="subscribe-to-an-incoming-call"></a>Suscripción a una llamada entrante
Suscríbase a un evento de llamada entrante.

```swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func callAgent(_ callAgent: CallAgent, didReceiveIncomingCall incomingcall: IncomingCall) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func incomingCall(_ incomingCall: IncomingCall, didEnd args: PropertyChangedEventArgs) {
        print("Incoming call was not answered")
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Aceptar una llamada entrante
Para aceptar una llamada, llame al método `accept` en un objeto `IncomingCall`.

```swift
self.incomingCall!.accept(options: AcceptCallOptions()) { (call, error) in
   if (error == nil) {
       print("Successfully accepted incoming call")
       self.call = call
   } else {
       print("Failed to accept incoming call")
   }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras.first
localVideoStreams = [LocalVideoStream]()
localVideoStreams!.append(LocalVideoStream(camera: firstCamera!))
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions.videoOptions = VideoOptions(localVideoStreams: localVideoStreams!)
if let incomingCall = self.incomingCall {
    incomingCall.accept(options: acceptCallOptions) { (call, error) in
        if error == nil {
            print("Incoming call accepted")
        } else {
            print("Failed to accept incoming call")
        }
    }
} else {
  print("No incoming call found to accept")
}
```

## <a name="perform-mid-call-operations"></a>Realización de operaciones durante la llamada

Durante una llamada, puede realizar varias operaciones para administrar la configuración relacionada con el vídeo y el audio.

### <a name="mute-and-unmute"></a>Silencio y reactivación del sonido

Para silenciar o reactivar el sonido del punto de conexión local, puede usar las API asincrónicas `mute` y `unmute`.

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}
```

Use el código siguiente para silenciar el punto de conexión local de forma asincrónica.

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

## <a name="manage-remote-participants"></a>Administración de participantes remotos

El tipo `RemoteParticipant` representa a todos los participantes remotos y estos están disponibles mediante la colección `remoteParticipants` en una instancia de llamada.

### <a name="list-participants-in-a-call"></a>Lista de los participantes en una llamada

```swift
call.remoteParticipants
```

### <a name="add-a-participant-to-a-call"></a>Incorporación de un participante a una llamada

Para agregar un participante a una llamada (ya sea un usuario o un número de teléfono), puede invocar a `addParticipant`. Este comando devolverá de manera sincrónica la instancia de un participante remoto.

```swift
let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))
```

### <a name="remove-a-participant-from-a-call"></a>Eliminación de un participante de una llamada
Para quitar un participante de una llamada (ya sea un usuario o un número de teléfono), puede invocar a la API `removeParticipant`. Esto se resolverá de manera asincrónica.

```swift
call!.remove(participant: remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}
```

### <a name="get-remote-participant-properties"></a>Obtención de las propiedades de los participantes remotos

```swift
// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision a token for another user
var identity = remoteParticipant.identifier

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]
```