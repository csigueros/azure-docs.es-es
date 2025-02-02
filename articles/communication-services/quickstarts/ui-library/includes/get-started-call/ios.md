---
description: En este tutorial, aprenderá a usar el compuesto de llamadas en iOS
author: palatter
ms.author: palatter
ms.date: 10/10/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: d87849dc85264f9a4066acabc55d613e1b47e127
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132133976"
---
## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Mac con [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), junto con un certificado de desarrollador válido instalado en el Llavero. [CocoaPods](https://cocoapods.org/) también debe instalarse para capturar dependencias.
- Un recurso de Communication Services implementado. [Cree un recurso de Communication Services](../../../create-communication-resource.md).
- Token de Azure Communication Services. [Vea el ejemplo.](../../../identity/quick-create-identity.md)

## <a name="setting-up"></a>Instalación

### <a name="creating-the-xcode-project"></a>Creación del proyecto de Xcode

En Xcode, cree un proyecto de iOS y seleccione la plantilla **App** (Aplicación). Usaremos guiones gráficos de UIKit. Durante este inicio rápido, no se van a crear pruebas. No dude en desactivar **Incluir pruebas**.

![Captura de pantalla que muestra la selección de la plantilla New Project (Nuevo proyecto) en Xcode.](../../media/xcode-new-project-template-select.png)

Asigne al proyecto el nombre `UILibraryQuickStart` y seleccione `Storyboard` en la lista desplegable `Interface`.

![Captura de pantalla que muestra los detalles del nuevo proyecto en Xcode.](../../media/xcode-new-project-details.png)

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instalación del paquete y las dependencias con CocoaPods

1. Cree un archivo podfile en el directorio raíz del proyecto mediante la ejecución de `pod init`.
2. Agregue lo siguiente al archivo Podfile:

```
source 'https://github.com/CocoaPods/Specs.git'
source 'https://github.com/Azure/AzurePrivatePodspecs.git'

platform :ios, '13.0'

target 'UILibraryQuickStart' do
    use_frameworks!
    pod 'AzureCommunicationUI', '1.0.0-alpha.1'
end
```

3. Ejecute `pod install --repo-update`. (Este proceso puede tardar entre 10 y 15 minutos).
4. Abra el archivo `.xcworkspace` generado con Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Solicite acceso al micrófono, la cámara, etc.

Para acceder al hardware del dispositivo, actualice la lista de propiedades de información de la aplicación. Establezca el valor asociado al elemento `string` que se incluirá en el cuadro de diálogo que el sistema usa para solicitar al usuario acceso.

Haga clic con el botón derecho en la entrada `Info.plist` del árbol del proyecto y seleccione **Abrir como** > **Código fuente**. Agregue las líneas siguientes a la sección `<dict>` de nivel superior y guarde el archivo.

```xml
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

Para comprobar que la solicitud del permiso se ha agregado correctamente, consulte la `Info.plist` como **Abrir como** > **Lista de propiedades** y debe esperar ver lo siguiente:

![Captura de pantalla que muestra la privacidad de la cámara y el micrófono en Xcode.](../../media/xcode-info-plist.png)

### <a name="turn-off-bitcode"></a>Desactivación `Bitcode`
Establezca la opción `Enable Bitcode` en `No` en la opción `Build Settings` del proyecto. Para encontrar la configuración, debe cambiar el filtro de `Basic` a `All`; también puede usar la barra de búsqueda de la derecha.

![Captura de pantalla que muestra la opción Bitcode en Xcode.](../../media/xcode-bitcode-option.png)

## <a name="initialize-composite"></a>Inicialización de compuesto

Vaya a "ViewController". Aquí vamos a insertar el siguiente código para inicializar los componentes compuestos de llamada. Reemplace `<GROUP_CALL_ID>` por el identificador de la llamada de grupo o por `UUID()` para generar uno. Reemplace también `<DISPLAY_NAME>` por su nombre y `<USER_ACCESS_TOKEN>` por el token.

```swift
import UIKit
import AzureCommunicationCalling
import CallingComposite

class ViewController: UIViewController {

    private var callComposite: CallComposite?

    override func viewDidLoad() {
        super.viewDidLoad()

        let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
        button.contentEdgeInsets = UIEdgeInsets(top: 10.0, left: 20.0, bottom: 10.0, right: 20.0)
        button.layer.cornerRadius = 10
        button.backgroundColor = .systemBlue
        button.setTitle("Start Experience", for: .normal)
        button.addTarget(self, action: #selector(startCallComposite), for: .touchUpInside)

        button.translatesAutoresizingMaskIntoConstraints = false
        self.view.addSubview(button)
        button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
        button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
    }

    @objc private func startCallComposite() {
        let callCompositeOptions = CallCompositeOptions()

        callComposite = CallComposite(withOptions: callCompositeOptions)

        let communicationTokenCredential = try! CommunicationTokenCredential(token: "<USER_ACCESS_TOKEN>")

        let options = GroupCallOptions(communicationTokenCredential: communicationTokenCredential,
                                       displayName: "<DISPLAY_NAME>",
                                       groupId: UUID(uuidString: "<GROUP_CALL_ID>")!)
        callComposite?.launch(with: options)
    }
}
```

## <a name="run-the-code"></a>Ejecución del código

Para compilar y ejecutar la aplicación en el simulador de iOS, seleccione **Producto** > **Ejecutar** o use el método abreviado de teclado (&#8984;-R).

1) Pulse `Start Experience`.
2) Acepte los permisos de audio y seleccione la configuración de dispositivo, micrófono y vídeo.
3) Pulse `Start Call`.

![Aspecto final de la aplicación de inicio rápido](../../media/quick-start-calling-composite-running-ios.gif)

## <a name="sample-application-code-can-be-found-here"></a>El código de la aplicación de ejemplo se puede encontrar [aquí](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/ui-library-quickstart).

## <a name="object-model"></a>Modelo de objetos

Las siguientes clases e interfaces controlan algunas de las características principales de la biblioteca cliente de la interfaz de usuario de Azure Communication Services:

| Nombre                                                                        | Descripción                                                                                  |
| --------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| [CallComposite](#create-call-composite) | El compuesto representa una experiencia de llamada con galería de participantes y controles. |
| [CallCompositeOptions](#create-call-composite) | Incluye opciones como la configuración del tema y el controlador de eventos. |
| [CallCompositeEventsHandler](#subscribe-to-events-from-callcomposite) | Permite recibir eventos de un compuesto. |
| [GroupCallOptions](#group-call) | Las opciones para unirse a una llamada de grupo, como groupId. |
| [TeamsMeetingOptions](#teams-meeting) | Opciones para unirse a la reunión de un equipo, como el vínculo de la reunión. |
| [ThemeConfiguration](#apply-theme-configuration) | Permite personalizar el tema. |

## <a name="ui-library-functionality"></a>Funcionalidad de la biblioteca de interfaz de usuario

### <a name="create-call-composite"></a>Creación de un compuesto de llamada

Inicialice una instancia de `CallCompositeOptions` y una instancia de `CallComposite` dentro de la función `startCallComposite`.

```swift
@objc private func startCallComposite() {
    let callCompositeOptions = CallCompositeOptions()

    callComposite = CallComposite(withOptions: callCompositeOptions)
}
```

### <a name="setup-authentication"></a>Configuración de la autenticación

Inicialice una instancia de `CommunicationTokenCredential` dentro de la función `startCallComposite`. Reemplace `<USER_ACCESS_TOKEN>` por su token.

```swift
let communicationTokenCredential = try! CommunicationTokenCredential(token: "<USER_ACCESS_TOKEN>")
```

Consulte la documentación relativa al [token de acceso de usuario](../../../identity/quick-create-identity.md) si aún no tiene ningún token disponible.

### <a name="setup-group-call-or-teams-meeting-options"></a>Configuración de las opciones de reunión en Teams o de una llamada grupal

En función del tipo de llamada o reunión que quiera configurar, use el objeto de opciones adecuado.

#### <a name="group-call"></a>Llamada grupal

Inicialice una instancia de `GroupCallOptions` dentro de la función `startCallComposite`. Reemplace `<GROUP_CALL_ID>` por el identificador de grupo de la llamada y `<DISPLAY_NAME>` por su nombre.

```swift
// let uuid = UUID() to create a new call
let uuid = UUID(uuidString: "<GROUP_CALL_ID>")!
let options = GroupCallOptions(communicationTokenCredential: communicationTokenCredential,
                               displayName: "<DISPLAY_NAME>",
                               groupId: uuid)
```

#### <a name="teams-meeting"></a>Reuniones en Teams

Inicialice una instancia de `TeamsMeetingOptions` dentro de la función `startCallComposite`. Reemplace `<TEAMS_MEETING_LINK>` por el identificador de grupo de la llamada y `<DISPLAY_NAME>` por su nombre.

```swift
let options = TeamsMeetingOptions(communicationTokenCredential: communicationTokenCredential,
                                  displayName: "<DISPLAY_NAME>",
                                  meetingLink: "<TEAMS_MEETING_LINK>")
```

#### <a name="get-a-microsoft-teams-meeting-link"></a>Obtención de un vínculo de reunión de Microsoft Teams

Se puede recuperar un vínculo a la reunión de Teams mediante instancias de Graph API. Este proceso se detalla en la [documentación de Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
El SDK de llamadas de Communication Services acepta un vínculo completo a la reunión de Microsoft Teams. Este vínculo se devuelve como parte del recurso `onlineMeeting`, al que se accede bajo la propiedad [`joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). También puede obtener la información necesaria de la reunión de la dirección URL **Unirse a la reunión** de la propia invitación a la reunión de Teams.

### <a name="launch"></a>Launch

Llamada a `launch` en la instancia de `CallComposite` dentro de la función `startCallComposite`

```swift
callComposite?.launch(with: options)
```

### <a name="subscribe-to-events-from-callcomposite"></a>Suscripción a eventos de `CallComposite`

Puede implementar los cierres de `CallCompositeEventsHandler` para actuar en los eventos y pasar la implementación a `CallCompositeOptions`. Un ejemplo es un evento para cuando el compuesto ha finalizado con un error.

```swift
let handler = CallCompositeEventsHandler(didFail: { error in
            print("didFail with error:\(error)")
        })
```

```swift
let callCompositeOptions = CallCompositeOptions(callCompositeEventsHandler: handler)
```

### <a name="apply-theme-configuration"></a>Aplicación de la configuración de tema

Para personalizar el tema, cree una configuración de tema personalizada que implemente el protocolo ThemeConfiguration. Luego, incluya una instancia de esa nueva clase en CallCompositeOptions.

```swift
class CustomThemeConfiguration: ThemeConfiguration {
   var primaryColor: UIColor {
       return UIColor.red
   }
}
```

```swift
let callCompositeOptions = CallCompositeOptions(themeConfiguration: CustomThemeConfiguration())
```
