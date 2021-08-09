---
title: Uso de la inserción de Teams de Azure Communication Services para iOS
description: En esta introducción, aprenderá a usar la biblioteca de inserción de Teams de Azure Communication Services para iOS.
author: palatter
ms.author: palatter
ms.date: 24/02/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1a6c8d05da04dc0f32fb278baf946ea363010903
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111546603"
---
## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../create-communication-resource.md).
- `User Access Token` para habilitar el cliente de llamada. Para más información sobre [cómo obtener `User Access Token`](../../access-tokens.md)
- Complete el inicio rápido para [empezar a agregar la inserción de Teams a la aplicación](../getting-started-with-teams-embed.md).

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
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
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

## <a name="bring-your-own-identity-from-the-app-to-the-participants-in-the-sdk-call"></a>Traiga su propia identidad de la aplicación a los participantes en la llamada del SDK

La aplicación puede asignar los valores de identidad de sus usuarios a los participantes en la llamada o reunión, e invalidar los valores predeterminados. Los valores incluyen avatar, nombre, subtítulo y rol.  

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
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
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
    
    func roleFor(identifier: CommunicationIdentifier, completionHandler: @escaping (MeetingUIClientUserRole) -> Void) {
    }
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

## <a name="receive-information-about-user-actions-in-the-ui-and-add-your-own-custom-functionalities"></a>Recepción de información sobre las acciones del usuario en la interfaz de usuario y adición de sus propias funcionalidades personalizadas

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
    let meetingJoinOptions = MeetingUIClientMeetingJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true)
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
## <a name="user-experience-customization"></a>Personalización de la experiencia del usuario

La experiencia del usuario en el SDK se puede personalizar mediante iconos específicos de la aplicación o reemplazando las barras de controles de llamada. 

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

### <a name="customize-main-call-screen"></a>Personalización de la pantalla principal de la llamada

`MeetingUIClient` proporciona compatibilidad para personalizar la interfaz de usuario de la pantalla principal de la llamada. Actualmente admite la personalización de la interfaz de usuario mediante los métodos de protocolo `MeetingUIClientInCallScreenDelegate`.
Las acciones de control de la pantalla de llamada se exponen a través de los métodos presentes en `MeetingUIClientCall`.

Agregue `MeetingUIClientInCallScreenDelegate` a la clase.

```swift
class ViewController: UIViewController, MeetingUIClientInCallScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Establezca `meetingUIClient?.meetingUIClientInCallScreenDelegate` en `self` antes de unirse a la llamada o reunión.

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientInCallScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
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

Agregue e implemente el método de protocolo `provideControlTopBar` para proporcionar la barra de información superior de la pantalla principal de la llamada.

```swift
func provideControlTopBar() -> UIView? {
    let topView = UIStackView.init()
    // add your customization here
    return topView
}
```

Agregue otros métodos de protocolo `MeetingUIClientInCallScreenDelegate` obligatorios a la clase, que se pueden dejar con implementaciones vacías para devolver valores nulos.
```swift
func provideControlBottomBar() -> UIView? {
    return nil
}

func provideScreenBackgroudColor() -> UIColor? {
    return nil
}
```

## <a name="customize-on-staging-call-screen"></a>Personalización en la pantalla provisional de la llamada

`MeetingUIClient` proporciona compatibilidad para personalizar la interfaz de usuario de la pantalla provisional de la llamada. Actualmente admite la personalización de la interfaz de usuario mediante los métodos de protocolo `MeetingUIClientStagingScreenDelegate`.
Al unirse a la llamada o reunión, establezca la propiedad de opciones de unión `enableCallStagingScreen` en `true` para mostrar la pantalla provisional.

Agregue `MeetingUIClientStagingScreenDelegate` a la clase.

```swift
class ViewController: UIViewController, MeetingUIClientStagingScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Establezca `meetingUIClient?.meetingUIClientStagingScreenDelegate` en `self` antes de unirse a la llamada o reunión.

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientStagingScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
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

Agregue e implemente el método de protocolo `provideJoinButtonCornerRadius` para modificar las esquinas del botón de unión para que tenga un aspecto redondeado.

```swift
func provideJoinButtonCornerRadius() -> CGFloat {
    return 24
}
```

Agregue otros métodos de protocolo `MeetingUIClientStagingScreenDelegate` obligatorios a la clase, que se pueden dejar con implementaciones vacías para devolver valores nulos.
```swift
func provideJoinButtonBackgroundColor() -> UIColor? {
    return nil
}

func provideStagingScreenBackgroundColor() -> UIColor? {
    return nil
}
```

## <a name="customize-on-connecting-call-screen"></a>Personalización en la pantalla conexión de la llamada

`MeetingUIClient` proporciona compatibilidad para personalizar la interfaz de usuario de la pantalla de conexión de la llamada. Actualmente admite la personalización de la interfaz de usuario mediante los métodos de protocolo `MeetingUIClientConnectingScreenDelegate`.
Use el método de configuración de iconos `set(iconConfig: Dictionary<MeetingUIClientIconType, String>)` expuesto en `MeetingUIClient` para cambiar solo los iconos que aparecen y usar la funcionalidad proporcionada por `MeetingUIClient`.


Agregue `MeetingUIClientConnectingScreenDelegate` a la clase.

```swift
class ViewController: UIViewController, MeetingUIClientConnectingScreenDelegate {

    private var meetingUIClient: MeetingUIClient?
    private var meetingUIClientCall: MeetingUIClientCall?
```

Establezca `meetingUIClient?.meetingUIClientConnectingScreenDelegate` en `self` antes de unirse a la llamada o reunión.

```swift
private func joinGroupCall() {
    meetingUIClient?.meetingUIClientConnectingScreenDelegate = self
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
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

Agregue e implemente el método de protocolo `provideConnectingScreenBackgroundColor` para modificar el color de fondo de la pantalla de conexión.

```swift
func provideConnectingScreenBackgroundColor() -> UIColor?
    return 24
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
    let groupJoinOptions = MeetingUIClientGroupCallJoinOptions(displayName: "John Smith", enablePhotoSharing: true, enableNamePlateOptionsClickDelegate: true, enableCallStagingScreen: true)
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
        print("Mute call failed: \(error!)")
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

// Lower the hand of user provided in the identifier for an active call.
// public func lowerHand(identifier: CommunicationIdentifier, completionHandler: @escaping (Error?) -> Void)

// Show the call roster for an active call.
public func showCallRoster(completionHandler: @escaping (Error?) -> Void)

// Change the layout in the call for self user.
public func getSupportedLayoutModes() -> [MeetingUIClientLayoutMode]
public func changeLayout(mode: MeetingUIClientLayoutMode, completionHandler: @escaping (Error?) -> Void)

// Hangs up the call or leaves the meeting.
public func hangUp(completionHandler: @escaping (Error?) -> Void)

// Set the user role for an active call.
public func setRoleFor(identifier: CommunicationIdentifier, userRole: MeetingUIClientUserRole, completionHandler: @escaping (Error?) -> Void)
```
