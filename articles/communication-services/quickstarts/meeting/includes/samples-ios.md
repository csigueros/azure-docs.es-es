---
title: Uso de la inserción de Teams de Azure Communication Services para iOS
description: En esta introducción, aprenderá a usar la biblioteca de inserción de Teams de Azure Communication Services para iOS.
author: palatter
ms.author: palatter
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 246c62ec25a788c7767da0c8fdf23b42db321f0b
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215154"
---
## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../create-communication-resource.md).
- `User Access Token` para habilitar el cliente de llamada. Para más información sobre [cómo obtener `User Access Token`](../../access-tokens.md)
- Complete el inicio rápido para [empezar a agregar la inserción de Teams a la aplicación](../getting-started-with-teams-embed.md).

## <a name="joining-a-group-call"></a>Unión a una llamada grupal

Para unirse a una llamada grupal, proporcione `MeetingUIClientGroupCallLocator` y `MeetingUIClientGroupCallJoinOptions` a la API `meetingUIClient?.join`. La llamada grupal no sonará para otros participantes. El usuario se unirá a la llamada de forma silenciosa.

Cree un botón en la devolución de llamada `viewDidLoad` en **ViewController.swift**.

```swift
class ViewController: UIViewController, MeetingUIClientCallDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let joinGroupCallButton = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
        joinGroupCallButton.backgroundColor = .black
        joinGroupCallButton.setTitle("Join Group Call", for: .normal)
        joinGroupCallButton.addTarget(self, action: #selector(joinGroupCallTapped), for: .touchUpInside)
        
        joinGroupCallButton.translatesAutoresizingMaskIntoConstraints = false
        self.view.addSubview(joinGroupCallButton)
        joinGroupCallButton.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        joinGroupCallButton.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
    }
```

Cree una salida para el botón en **ViewController.swift** a la clase `ViewController`.

```swift
@IBAction func joinGroupCallTapped(_ sender: UIButton) {
    joinGroupCall()
}

private func joinGroupCall() {
    // Add join meeting logic
}
```

Para configurar el cliente y proporcionar el token, se siguen los mismos pasos que para la API de unión a una reunión, que se describen en el [inicio rápido](../getting-started-with-teams-embed.md). 

El método `joinGroupCall` se establece como la acción que se llevará a cabo cuando se toque el botón *Join Group Call* (Unirse a la llamada grupal).
Cree un elemento `MeetingUIClientGroupCallLocator` y configure las opciones de unión mediante `MeetingUIClientGroupCallJoinOptions`.
Tenga en cuenta que debe reemplazar `<GROUP_ID>` por una cadena UUID. La cadena de identificador de grupo debe tener el formato GUID o UUID.
```swift
private func joinGroupCall() {
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: <GROUP_ID>)
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall = meetingUIClientCall
            } else {
                print("Join meeting failed: \(error!)")
            }
        }
    })
}
```

## <a name="teams-embed-call-or-meeting-status-events-capturing"></a>Captura de eventos de estado de llamada o reunión de inserción de Teams

El estado de una llamada o reunión de grupo unido se puede capturar desde el delegado `MeetingUIClientCallDelegate`. El estado incluye los estados de conexión, el número de participantes y las modalidades, como el estado del micrófono o la cámara.   

Agregue `MeetingUIClientCallDelegate` a la clase y agregue las variables necesarias.

```swift
class ViewController: UIViewController, MeetingUIClientCallDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
    
    //Rest of the UIViewController code
}
```

Establezca `self.meetingUIClientCall?.meetingUIClientCallDelegate` en `self` después de que la unión a la llamada o reunión se haya iniciado correctamente.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```

Implemente los métodos de protocolo `MeetingUIClientCallDelegate` que la aplicación necesite y agregue código auxiliar para los que no son necesarios.

```swift
    func meetingUIClient(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
            print("Call state has changed to 'Connecting'")
        case .connected:
            print("Call state has changed to 'Connected'")
        case .waitingInLobby:
            print("Call state has changed to 'Waiting in Lobby'")
        case .ended:
            print("Call state has changed to 'Ended'")
        }
    }
    
    func meetingUIClient(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }
    
    func onIsMutedChanged() {
    }
    
    func onIsSendingVideoChanged() {
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
    }
```

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-call"></a>Traiga su propia identidad de la aplicación a los participantes en la llamada.

La aplicación puede asignar los valores de identidad de sus usuarios a los participantes en la llamada o reunión, e invalidar los valores predeterminados. Los valores incluyen avatar, nombre y subtítulo.  

### <a name="assigning-avatars-for-call-participants"></a>Asignación de avatares para los participantes de la llamada

Agregue `MeetingUIClientCallIdentityProviderDelegate` a la clase.

```swift
class ViewController: UIViewController, MeetingUIClientCallIdentityProviderDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Establezca `self.meetingUIClientCall?.MeetingUIClientIdentityProviderDelegate` en `self` después de que la unión a la llamada o reunión se haya iniciado correctamente.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallIdentityProviderDelegate = self
            }
        }
    })
}
```

Agregue e implemente el método de protocolo `avatarFor` y asigne cada `identifier` al avatar correspondiente.

```swift
    func avatarFor(identifier: CommunicationIdentifier, size: MeetingUIClientAvatarSize, completionHandler: @escaping (UIImage?) -> Void) {
        if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:teamsvisitor:")) {
                // Anonymous teams user will start with prefix 8:teamsvistor:
                let image = UIImage (named: "avatarPink")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:orgid:")) {
                // OrgID user will start with prefix 8:orgid:
                let image = UIImage (named: "avatarDoctor")
                completionHandler(image!)
            }
            else if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // ACS user will start with prefix 8:acs:
                let image = UIImage (named: "avatarGreen")
                completionHandler(image!)
            } else {
                completionHandler(nil)
            }
        } else {
            completionHandler(nil)
        }
    }

```

Agregue otros métodos de protocolo MeetingUIClientCallIdentityProviderDelegate obligatorios a la clase, y es posible que se queden con implementaciones vacías.
```swift
    func displayNameFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
    
    func subTitleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (String?) -> Void) {
    }
```

## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>Recepción de información sobre las acciones del usuario en la interfaz de usuario y adición de sus propias funcionalidades personalizadas

### <a name="call-screen"></a>Pantalla de llamada 
Se llama a los métodos delegados `MeetingUIClientCallUserEventDelegate` tras las acciones del usuario en el perfil del participante remoto.
Al unirse a la llamada o reunión, establezca la propiedad de opciones de unión `enableNamePlateOptionsClickDelegate` en `true`.
Establecer esta propiedad habilitaría las opciones de placa de nombre en el perfil del participante remoto y habilitaría `MeetingUIClientCallUserEventDelegate`.

Agregue `MeetingUIClientCallUserEventDelegate` a la clase.

```swift
class ViewController: UIViewController, MeetingUIClientCallUserEventDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Establezca `self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate` en `self` después de que la unión a la llamada o reunión se haya iniciado correctamente.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: false, enableCallRosterDelegate: false)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallUserEventDelegate = self
            }
        }
    })
}
```

Agregue e implemente el método de protocolo `onNamePlateOptionsClicked` y asigne cada `identifier` al usuario correspondiente participante de la llamada.
Se llama a este método al pulsar una vez el mosaico del usuario o el texto del título del usuario desde la pantalla principal de la llamada.

```swift
func onNamePlateOptionsClicked(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```

Agregue e implemente el método de protocolo `onParticipantViewLongPressed` y asigne cada `identifier` al usuario correspondiente participante de la llamada.
Se llama a este método mediante una presión larga en el mosaico del usuario desde la pantalla principal de la llamada.

```swift
func onParticipantViewLongPressed(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```
### <a name="call-roster"></a>Lista de llamadas 
Se llama a los métodos delegados `MeetingUIClientCallRosterDelegate` tras las acciones del usuario en un participante de la lista.
Al unirse a la llamada o reunión, establezca la propiedad de opciones de unión `enableCallRosterDelegate` en `true`.
Establecer esta propiedad habilitaría las opciones de placa de nombre en el perfil del participante remoto y habilitaría `MeetingUIClientCallRosterDelegate`.

Agregue `MeetingUIClientCallRosterDelegate` a la clase.

```swift
class ViewController: UIViewController, MeetingUIClientCallRosterDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Establezca `self.meetingUIClientCall?.meetingUIClientCallRosterDelegate` en `self` después de que la unión a la llamada o reunión se haya iniciado correctamente.

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: false, enableNamePlateOptionsClickDelegate: false, enableCallStagingScreen: false, enableCallRosterDelegate: true)
    let meetingLocator = MeetingUIClientTeamsMeetingLinkLocator(meetingLink: "<MEETING_URL>")
    meetingUIClient?.join(meetingLocator: meetingLocator, joinCallOptions: meetingJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallRosterDelegate = self
            }
        }
    })
}
```

Agregue e implemente el método de protocolo `onCallParticipantCellTapped` y asigne cada `identifier` al usuario correspondiente participante de la llamada.
Se llama a este método con una sola pulsación en un participante de la lista de llamadas.

```swift
func onCallParticipantCellTapped(identifier: CommunicationIdentifier) {
    if let userIdentifier = identifier as? CommunicationUserIdentifier
        {
            if (userIdentifier.identifier.starts(with: "8:acs:")) {
                // Custom behavior based on the user here.
                print("Acs user tile clicked")
            }
        }
}
```
## <a name="user-experience-customization"></a>Personalización de la experiencia del usuario

La experiencia de usuario en el SDK se puede personalizar mediante iconos específicos de la aplicación. 

### <a name="customize-ui-icons-in-a-call-or-meeting"></a>Personalización de los iconos de la UI en una llamada o reunión

Los iconos que se muestran en la llamada o reunión se pueden personalizar mediante el método `public func set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` expuesto en `MeetingUIClient`.
La lista de iconos posibles que se podrían personalizar está disponible en `MeetingUIClientIconType`.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Después de inicializar meetingUIClient, establezca la configuración de iconos `meetingUIClient?.set(iconConfig: self.getIconConfig())` para los iconos de llamada admitidos en `MeetingUIClientIconType`.

```swift
private func initMeetingUIClient() {
    meetingUIClient = MeetingUIClient(with: credential)
    meetingUIClient?.set(iconConfig: self.getIconConfig())
}

func getIconConfig() -> Dictionary<MeetingUIClientIconType, String> {
    var iconConfig = Dictionary<MeetingUIClientIconType, String>()
    iconConfig.updateValue("camera_fill", forKey: MeetingUIClientIconType.VideoOn)
    iconConfig.updateValue("camera_off", forKey: MeetingUIClientIconType.VideoOff)
    iconConfig.updateValue("microphone_fill", forKey: MeetingUIClientIconType.MicOn)
    iconConfig.updateValue("microphone_off", forKey: MeetingUIClientIconType.MicOff)
    iconConfig.updateValue("speaker_fill", forKey: MeetingUIClientIconType.Speaker)
    return iconConfig
}
```

## <a name="perform-operations-with-the-call"></a>Realización de operaciones con la llamada

Las acciones de control de las llamadas se exponen a través de los métodos presentes en `MeetingUIClientCall`.
Estos métodos son útiles para controlar las acciones de llamada si la interfaz de usuario se había personalizado mediante los delegados de personalización `MeetingUIClient`.

Agregue las variables necesarias a las llamadas.
```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Asigne a la variable `self.meetingUIClientCall` el valor `meetingUIClientCall` del método `completionHandler` de `join`.
```swift
private func joinGroupCall() {
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true, enableCallRosterDelegate: false)
    let groupLocator = MeetingUIClientGroupCallLocator(groupId: "<GROUP_ID>")
    meetingUIClient?.join(meetingLocator: groupLocator, joinCallOptions: groupJoinOptions, completionHandler: { (meetingUIClientCall: MeetingUIClientCall?, error: Error?) in
        if (error != nil) {
            print("Join call failed: \(error!)")
        }
        else {
            if (meetingUIClientCall != nil) {
                self.meetingUIClientCall? = meetingUIClientCall
                self.meetingUIClientCall?.meetingUIClientCallDelegate = self
            }
        }
    })
}
```
### <a name="mute-and-unmute"></a>Silencio y reactivación del sonido

Invoque el método `mute` para silenciar el micrófono de una llamada activa, si existe.
Los cambios en el estado del micrófono se notifican en el método `onIsMutedChanged` de `MeetingUIClientCallDelegate`.

```swift
// Mute the microphone for an active call.
public func mute(completionHandler: @escaping (Error?) -> Void)

    meetingUIClientCall?.mute { [weak self] (error) in
        if error != nil {
            print("Mute call failed: \(error!)")
        }
}
```

Invoque el método `unmute` para reactivar el audio del micrófono de una llamada activa, si existe.

```swift
// Unmute the microphone for an active call.
public func unmute(completionHandler: @escaping (Error?) -> Void)

meetingUIClientCall?.unmute { [weak self] (error) in
    if error != nil {
        print("Unmute call failed: \(error!)")
    }
}
```

### <a name="other-operations-available-in-from-the--meetinguiclientcall-class"></a>Otras operaciones disponibles de la clase `MeetingUIClientCall`

```swift
// Start the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Stop the video for an active call.
public func startVideo(completionHandler: @escaping (Error?) -> Void)

// Set the preferred audio route in the call for self user.
public func setAudio(route: MeetingUIClientAudioRoute, completionHandler: @escaping (Error?) -> Void)

// Raise the hand of current user for an active call.
public func raiseHand(completionHandler: @escaping (Error?) -> Void)

// Lower the hand of current user for an active call.
// public func lowerHand(completionHandler: @escaping (Error?) -> Void)

// Show the call roster for an active call.
public func showCallRoster(completionHandler: @escaping (Error?) -> Void)

// Change the layout in the call for self user.
public func getSupportedLayoutModes() -> [MeetingUIClientLayoutMode]
public func changeLayout(mode: MeetingUIClientLayoutMode, completionHandler: @escaping (Error?) -> Void)

// Hang up the call or leave the meeting.
public func hangUp(completionHandler: @escaping (Error?) -> Void)
```

## <a name="use-teams-embed-sdk-and-azure-communication-calling-sdk-in-the-same-app"></a>Uso del SDK de inserción de Teams y Calling SDK de Azure Communication en la misma aplicación

El SDK de inserción de Teams también incluye Calling SDK de Azure Communication (ACS), que permite usar las características de ambos SDK en la misma aplicación. Solo se puede inicializar y usar un SDK en el mismo momento. Inicializar y usar ambos SDK al mismo tiempo dará lugar a un comportamiento inesperado. 

Importe `TeamsAppSDK` para acceder a Calling SDK de Azure Communication para el SDK de inserción de Teams. 
```swift
import TeamsAppSDK
```

Declaración de variables para el uso de ACS
```swift
class ViewController: UIViewController {

    private var callClient: CallClient?
    private var callAgent: CallAgent?
    private var call: Call?
    
    //Rest of the UIViewController code
}
    
```

La inicialización se realiza mediante la creación de un nuevo `CallClient`. Agregue la creación a `viewDidLoad` o a cualquier otro método.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    self.callClient = CallClient()
}
```
Use todas las API de ACS como se describen en la documentación. El uso de las API no se describe en esta documentación. 

Elimine el SDK de ACS y establezca `nil` en sus variables después de que el uso ya no sea necesario o que la aplicación necesite usar el SDK de inserción de Teams.
```swift
    public func disposeAcsSdk()
    {
        self.call = nil
        self.callAgent?.dispose()
        self.callClient?.dispose()
        self.callAgent = nil
        self.callClient = nil
    }

```

La inicialización del SDK de inserción de Teams también se realiza durante la creación de `MeetingUIClient`. Agregue la creación a `viewDidLoad` o a cualquier otro método.
```swift
override func viewDidLoad() {
    super.viewDidLoad()
    do {
        let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
        let credential = try CommunicationTokenCredential(withOptions: communicationTokenRefreshOptions)
        meetingUIClient = MeetingUIClient(with: credential)
    }
    catch {
        print("Failed to create communication token credential")
    }
}

private func fetchTokenAsync(completionHandler: @escaping TokenRefreshHandler) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}

```
Use las API del SDK de inserción de Teams como se describe en la documentación. El uso de las API no se describe en esta documentación. 

Elimine el SDK de inserción de Teams y establezca `nil` en sus variables después de que el uso ya no sea necesario o que la aplicación necesite usar el SDK de ACS.
```swift
    private func disposeTeamsSdk() {
        self.meetingUIClient?.dispose(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("Dispose failed: \(error!)")
            } else {
                self.meetingUIClient = nil
                self.meetingUIClientCall = nil
            }
        })        
    }

```

La eliminación del SDK de inserción de Teams solo es posible si no hay llamadas activas. `meetingUIClient?.dispose` devolverá un error en su controlador de finalización si hay una llamada activa. Cuelgue la llamada activa y llame a `self.disposeTeamsSdk()` si no queda ninguna llamada activa.
```swift
    
    private var shouldDispose: Bool = false

    public func endMeeting() {
        meetingUIClientCall?.hangUp(completionHandler: { (error: Error?) in
            if (error != nil) {
                print("End meeting failed: \(error!)")
                // There are no active calls
                self.disposeTeamsSdk()
            } else {
                // Ending active call is in progress.
                self.shouldDispose = true;
            }
        })
    }

```

Implemente el método `meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState)` de protocolo `MeetingUIClientCallDelegate` para obtener la actualización de estado sobre la llamada activa que finaliza y elimine el SDK de inserción de Teams después de esto.
```swift
    func meetingUIClientCall(didUpdateCallState callState: MeetingUIClientCallState) {
        switch callState {
        case .connecting:
        case .connected:
        case .waitingInLobby:
        case .ended:
            if (self.shouldDispose) {
                self.disposeTeamsSdk()
            }
        @unknown default:
            print("Unsupported state")
        }
    }
```

Agregue otros métodos de protocolo `MeetingUIClientCallDelegate` obligatorios a la clase.

```swift
    func meetingUIClientCall(didUpdateRemoteParticipantCount remoteParticipantCount: UInt) {
        print("Remote participant count has changed to: \(remoteParticipantCount)")
    }

    func onIsMutedChanged() {
        print("Mute state changed to: \(meetingUIClientCall?.isMuted ?? false)")
    }
    
    func onIsSendingVideoChanged() {
        print("Sending video state changed to: \(meetingUIClientCall?.isSendingVideo ?? false)")
    }
    
    func onIsHandRaisedChanged(_ participantIds: [Any]) {
        print("Self participant raise hand status changed to: \(meetingUIClientCall?.isHandRaised ?? false)")
    }
```
