---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 319571066bd69d1bc80414de7bdb49da27102c18
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "128699419"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

## <a name="place-a-call"></a>Realización de una llamada

Para crear e iniciar una llamada, es necesario llamar al método `CallAgent.startCall()` y proporcionar el `Identifier` de los destinatarios.
Para unirse a una llamada grupal, debe llamar al método `CallAgent.join()` y proporcionar el groupId. Los identificadores de grupo deben tener el formato GUID o UUID.

La creación y el inicio de la llamada son sincrónicos. La instancia de llamada le permite suscribirse a todos los eventos de la llamada.

### <a name="place-a-11-call-to-a-user"></a>Realización de una llamada 1:1 a un usuario
Para realizar una llamada a otro usuario de Communication Services, invoque el método `call` en `callAgent` y pase un objeto con la clave `communicationUserId`.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUserIdentifier acsUserId = new CommunicationUserIdentifier(<USER_ID>);
CommunicationUserIdentifier participants[] = new CommunicationUserIdentifier[]{ acsUserId };
call oneToOneCall = callAgent.startCall(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Realización de una llamada 1:n con usuarios y RTC
> [!WARNING]
> Actualmente la llamada RTC no está disponible.

Para realizar una llamada 1:n a un usuario y un número de RTC, debe especificar el número de teléfono del destinatario.
El recurso de Communication Services debe estar configurado para permitir llamadas RTC:
```java
CommunicationUserIdentifier acsUser1 = new CommunicationUserIdentifier(<USER_ID>);
PhoneNumberIdentifier acsUser2 = new PhoneNumberIdentifier("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.startCall(participants, startCallOptions);
```

## <a name="accept-a-call"></a>Aceptación de una llamada
Para aceptar una llamada, llame al método "accept" en un objeto de llamada.

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
Call call = incomingCall.accept(context).get();
```

Para aceptar una llamada con la cámara de vídeo activada:

```java
Context appContext = this.getApplicationContext();
IncomingCall incomingCall = retrieveIncomingCall();
AcceptCallOptions acceptCallOptions = new AcceptCallOptions();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
acceptCallOptions.setVideoOptions(new VideoOptions(new LocalVideoStream(desiredCamera, appContext)));
Call call = incomingCall.accept(context, acceptCallOptions).get();
```

La llamada entrante se puede obtener mediante la suscripción al evento `onIncomingCall` en el objeto `callAgent`:

```java
// Assuming "callAgent" is an instance property obtained by calling the 'createCallAgent' method on CallClient instance 
public Call retrieveIncomingCall() {
    IncomingCall incomingCall;
    callAgent.addOnIncomingCallListener(new IncomingCallListener() {
        void onIncomingCall(IncomingCall inboundCall) {
            // Look for incoming call
            incomingCall = inboundCall;
        }
    });
    return incomingCall;
}
```

## <a name="join-a-group-call"></a>Unión a una llamada grupal
Para iniciar una llamada grupal nueva o unirse a una en curso, debe llamar al método "join" y pasar un objeto con una propiedad `groupId`. El valor debe ser un GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallLocator groupCallLocator = new GroupCallLocator("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallLocator, joinCallOptions);
```

## <a name="call-properties"></a>Propiedades de llamada

Obtenga el id. único de esta llamada:

```java
String callId = call.getId();
```

Para información sobre los demás participantes de la llamada, revise la colección `remoteParticipant` de la instancia de `call`:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

La identidad del autor de la llamada si la llamada es entrante:

```java
CommunicationIdentifier callerId = call.getCallerInfo().getIdentifier();
```

Obtenga el estado de la llamada: 

```java
CallState callState = call.getState();
```

Devuelve una cadena que representa el estado actual de una llamada:
* "NONE": estado inicial de la llamada.
* "EARLY_MEDIA": indica un estado en el que se reproduce un anuncio antes de conectar la llamada.
* "CONNECTING": estado de transición inicial una vez que se realiza o acepta la llamada.
* "RINGING": en el caso de una llamada saliente, indica que se está llamando a los participantes remotos.
* "CONNECTED": la llamada está conectada.
* "LOCAL_HOLD": el participante local ha puesto la llamada en espera; no fluye ningún elemento multimedia entre el punto de conexión local y los participantes remotos.
* "REMOTE_HOLD": un participante remoto ha puesto la llamada en espera; no fluye ningún elemento multimedia entre el punto de conexión local y los participantes remotos.
* "DISCONNECTING": estado de transición antes de que la llamada pase al estado "Desconectado".
* "DISCONNECTED": estado final de la llamada.
* "IN_LOBBY": en busca de interoperabilidad de una reunión de Teams.

Para saber por qué ha finalizado una llamada, revise la propiedad `callEndReason`. Contiene código o subcódigo: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Para ver si la llamada actual es una llamada entrante o saliente, revise la propiedad `callDirection`:

```java
CallDirection callDirection = call.getCallDirection(); 
// callDirection == CallDirection.INCOMING for incoming call
// callDirection == CallDirection.OUTGOING for outgoing call
```

Para ver si el micrófono actual está silenciado, revise la propiedad `muted`:

```java
boolean muted = call.isMuted();
```

Para revisar las secuencias de vídeo activas, compruebe la colección `localVideoStreams`:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

## <a name="mute-and-unmute"></a>Silencio y reactivación del sonido

Para silenciar o reactivar el sonido del punto de conexión local, puede usar las API asincrónicas `mute` y `unmute`:

```java
Context appContext = this.getApplicationContext();
call.mute(appContext).get();
call.unmute(appContext).get();
```
## <a name="change-the-volume-of-the-call"></a>Cambio del volumen de la llamada

Mientras se encuentra en una llamada, las teclas de volumen de hardware en el teléfono deben permitir al usuario cambiar el volumen de llamada.
Esto se consigue mediante el método `setVolumeControlStream` con el tipo de secuencia `AudioManager.STREAM_VOICE_CALL` en la actividad donde se realiza la llamada.
Esto permite que las teclas de volumen de hardware cambien el volumen de la llamada (que se indica mediante un icono de teléfono o algo similar en el control deslizante del volumen) e impide que se cambie el volumen de otros perfiles de sonido, como alarmas, multimedia o el volumen de todo el sistema. Para obtener más información, puede consultar [Cómo controlar cambios en la salida de audio | Desarrolladores de Android](https://developer.android.com/guide/topics/media-apps/volume-and-earphones).

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    ...
    setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);
}
```

## <a name="remote-participants-management"></a>Administración de participantes remotos

El tipo `RemoteParticipant` representa a todos los participantes remotos y estos están disponibles a través de la colección `remoteParticipants` en una instancia de llamada.

### <a name="list-participants-in-a-call"></a>Enumeración de participantes en una llamada
La colección `remoteParticipants` devuelve una lista de los participantes remotos en una llamada determinada:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="add-a-participant-to-a-call"></a>Incorporación de un participante a una llamada

Para agregar un participante a una llamada (ya sea un usuario o un número de teléfono), puede invocar `addParticipant`. Esto devolverá de manera sincrónica la instancia del participante remoto.

```java
const acsUser = new CommunicationUserIdentifier("<acs user id>");
const acsPhone = new PhoneNumberIdentifier("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
AddPhoneNumberOptions addPhoneNumberOptions = new AddPhoneNumberOptions(new PhoneNumberIdentifier("<alternate phone number>"));
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone, addPhoneNumberOptions);
```

### <a name="remove-participant-from-a-call"></a>Eliminación del participante de una llamada
Para quitar un participante de una llamada (ya sea un usuario o un número de teléfono), puede invocar `removeParticipant`.
Esto se resolverá de manera asincrónica una vez que el participante se quite de la llamada.
El participante también se quitará de la colección `remoteParticipants`.
```java
RemoteParticipant acsUserRemoteParticipant = call.getParticipants().get(0);
RemoteParticipant acsPhoneRemoteParticipant = call.getParticipants().get(1);
call.removeParticipant(acsUserRemoteParticipant).get();
call.removeParticipant(acsPhoneRemoteParticipant).get();
```

### <a name="remote-participant-properties"></a>Propiedades de los participantes remotos
Todo participante remoto especificado tiene un conjunto de propiedades y colecciones asociadas:

* Obtenga el identificador de este participante remoto.
"Identity" es uno de los tipos de "Identifier":
    ```java
    CommunicationIdentifier participantIdentifier = remoteParticipant.getIdentifier();
    ```

* Obtenga el estado de este participante remoto.
    ```java
    ParticipantState state = remoteParticipant.getState();
    ```
El estado puede ser uno de los siguientes:
* "IDLE": estado inicial.
* "EARLY_MEDIA": se reproduce un anuncio antes de que el participante se conecte a la llamada.
* "RINGING": la llamada del participante está sonando.
* "CONNECTING": estado de transición mientras el participante se conecta a la llamada.
* "CONNECTED": el participante se conecta a la llamada.
* "HOLD": el participante está en espera.
* "IN_LOBBY": el participante está esperando que se admita en la sala. Actualmente solo se usa en el escenario de interoperabilidad de Teams
* "DISCONNECTED": estado final; el participante se desconecta de la llamada.

* Para saber por qué el participante dejó la llamada, revise la propiedad `callEndReason`:
    ```java
    CallEndReason callEndReason = remoteParticipant.getCallEndReason();
    ```

* Para comprobar si este participante remoto está silenciado o no, revise la propiedad `isMuted`:
    ```java
    boolean isParticipantMuted = remoteParticipant.isMuted();
    ```

* Para comprobar si este participante remoto está hablando o no, revise la propiedad `isSpeaking`:
    ```java
    boolean isParticipantSpeaking = remoteParticipant.isSpeaking();
    ```

* Para revisar todas las secuencias de vídeo que un participante determinado envía en esta llamada, compruebe la colección `videoStreams`:
    ```java
    List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
    ```