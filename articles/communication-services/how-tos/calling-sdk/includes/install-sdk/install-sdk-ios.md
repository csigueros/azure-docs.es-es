---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0ca40e760e34be8ef4299e042949f7a36b54f36f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837943"
---
## <a name="set-up-your-system"></a>Configuración del sistema

### <a name="create-the-xcode-project"></a>Creación del proyecto de Xcode

En Xcode, cree un proyecto de iOS nuevo y seleccione la plantilla **Aplicación de vista única**. En este inicio rápido se usa el [marco SwiftUI](https://developer.apple.com/xcode/swiftui/), por lo que debe establecer el **lenguaje** en **Swift** y la **interfaz de usuario** en **SwiftUI**. 

No va a crear pruebas unitarias ni pruebas de interfaz de usuario durante este inicio rápido. No dude en borrar los cuadros de texto para **incluir pruebas unitarias** y para **incluir pruebas de IU**.

:::image type="content" source="../../../../quickstarts/voice-video-calling/media/ios/xcode-new-ios-project.png" alt-text="Captura de pantalla que muestra la ventana para crear un proyecto en Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instalación del paquete y las dependencias con CocoaPods

1. Cree un podfile para la aplicación, de la siguiente manera:

    ```
    platform :ios, '13.0'
    use_frameworks!
    target 'AzureCommunicationCallingSample' do
        pod 'AzureCommunicationCalling', '~> 1.0.0'
    end
    ```
2. Ejecute `pod install`.
3. Abra el archivo `.xcworkspace` con Xcode.

### <a name="request-access-to-the-microphone"></a>Solicitud de acceso al micrófono

Para acceder al micrófono del dispositivo, debe actualizar la lista de propiedades de información de la aplicación con el elemento `NSMicrophoneUsageDescription`. Establezca el valor asociado al elemento `string` que se incluirá en el cuadro de diálogo que el sistema usa para solicitar acceso al usuario.

Haga clic con el botón derecho en la entrada `Info.plist` del árbol del proyecto y seleccione **Abrir como** > **Código fuente**. Agregue las líneas siguientes a la sección `<dict>` de nivel superior y guarde el archivo.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Instalación del marco de la aplicación

Abra el archivo *ContentView.swift* del proyecto y agregue una declaración `import` en la parte superior del archivo para importar la biblioteca `AzureCommunicationCalling`. Además, importe `AVFoundation`. Lo necesitará para las solicitudes de permiso de audio en el código.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="initialize-callagent"></a>Inicialización de CallAgent

Para crear una instancia de `CallAgent` a partir de `CallClient`, debe usar el método `callClient.createCallAgent`, que devuelve de manera asincrónica un objeto `CallAgent` después de que se inicializa.

Para crear un cliente de llamada, debe pasar un objeto `CommunicationTokenCredential`.

```swift
import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
do {
    let options = CommunicationTokenRefreshOptions(initialToken: token, refreshProactively: true, tokenRefresher: self.fetchTokenSync)
    userCredential = try CommunicationTokenCredential(withOptions: options)
} catch {
    updates("Couldn't created Credential object", false)
    initializationDispatchGroup!.leave()
    return
}

// tokenProvider needs to be implemented by Contoso, which fetches a new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Pase el objeto `CommunicationTokenCredential` que ha creado a `CallClient` y establezca el nombre para mostrar.

```swift
self.callClient = CallClient()
let callAgentOptions = CallAgentOptions()
options.displayName = " iOS ACS User"

self.callClient!.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})
```