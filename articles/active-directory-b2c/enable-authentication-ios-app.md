---
title: 'Habilitación de la autenticación en una aplicación Swift para iOS: Azure AD B2C'
description: Habilitación de la autenticación en una aplicación Swift para iOS mediante los bloques de creación de Azure Active Directory B2C Obtenga información sobre cómo usar Azure AD B2C para iniciar sesión y registrar usuarios en una aplicación Swift para iOS.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/29/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: d9efe9b3d7810eccb94906c236b34534861db092
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779869"
---
# <a name="enable-authentication-in-your-own-ios-swift-application-using-azure-active-directory-b2c"></a>Habilitación de la autenticación en su propia aplicación Swift para iOS mediante Azure Active Directory B2C

En este artículo se muestra cómo agregar la autenticación de Azure Active Directory B2C (Azure AD B2C) a su propia aplicación móvil Swift para iOS. Más información sobre cómo integrar una aplicación Swift para iOS con la biblioteca de autenticación [MSAL para iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc). 

Use este artículo junto con el artículo [Configuración de la autenticación en una aplicación Swift para iOS de ejemplo](./configure-authentication-sample-ios-app.md) cuando sustituya la aplicación Swift para iOS de ejemplo por la suya propia. Después de completar los pasos de este artículo, la aplicación aceptará inicios de sesión mediante Azure AD B2C.

## <a name="prerequisites"></a>Requisitos previos

Revise los requisitos previos y los pasos de integración de [Configuración de la autenticación en una aplicación Swift para iOS de ejemplo](configure-authentication-sample-ios-app.md).

## <a name="create-an-ios-swift-app-project"></a>Creación de un proyecto de aplicación Swift para iOS

Si aún no tiene una aplicación Swift para iOS, siga estos pasos para configurar un nuevo proyecto.

1. Abra [Xcode](https://developer.apple.com/xcode/) y seleccione **File** > **New** > **Project** (Archivo > Nuevo > Proyecto).
1. En el caso de las aplicaciones para iOS, seleccione **iOS** > **Aplicación** y seleccione **Siguiente**.
1. Para **Choose options for your new project** (Elegir opciones para el nuevo proyecto), indique lo siguiente:
    1. **Product name** (Nombre de producto), como `MSALiOS`.
    1. **Organization identifier** (Identificador de la organización), como `contoso.com`.
    1. En **Interface** (Interfaz), seleccione **Storyboard** (Guion gráfico).
    1. En **Life cycle** (Ciclo de vida), seleccione **UIKit App Delegate** (Delegar aplicación UIKit).
    1. En **Language** (Lenguaje), seleccione **Swift**. 
1. Seleccione **Siguiente**.
1. Seleccione una carpeta para crear la aplicación y elija **Crear**.


## <a name="install-the-msal-library"></a>Instalación de la biblioteca MSAL

1. Ejecute [CocoaPods](https://cocoapods.org/) para instalar la biblioteca MSAL.   En la misma carpeta que el archivo `.xcodeproj` del proyecto, si el archivo `podfile` no existe, cree un archivo vacío llamado `podfile`. Agregue el siguiente código al archivo `podfile`:

   ```
   use_frameworks!

   target '<your-target-here>' do
      pod 'MSAL'
   end
   ```

1. Reemplace `<your-target-here>` por el nombre del proyecto. Por ejemplo, `MSALiOS`. Para más información, consulte la [referencia de la sintaxis de Podfile](https://guides.cocoapods.org/syntax/podfile.html#podfile).
1. En una ventana del termina, desplácese a la carpeta que contiene el archivo `podfile`. Ejecute el archivo `pod install` para instalar la biblioteca MSAL.
1. Después de ejecutar el comando `pod install`, se crea un archivo `<your project name>.xcworkspace`. Para recargar el proyecto en Xcoce, cierre Xcode y abra `<your project name>.xcworkspace`.

## <a name="set-the-app-url-scheme"></a>Establecimiento del esquema de dirección URL de la aplicación

Cuando un usuario se autentica, Azure AD B2C envía un código de autorización a la aplicación mediante el URI de redirección configurado en el registro de aplicación de Azure AD B2C. 

El formato de URI de redirección predeterminado de MSAL es `msauth.[Your_Bundle_Id]://auth`. Por ejemplo, `msauth.com.microsoft.identitysample.MSALiOS://auth`, donde `msauth.com.microsoft.identitysample.MSALiOS` es el esquema de la dirección URL.

En este paso, registre el esquema de la dirección URL mediante la matriz `CFBundleURLSchemes`. La aplicación escucha en el esquema de dirección URL para la devolución de llamada de Azure AD B2C. 

En Xcode, abra el [archivo Info.plist](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html) como archivo de código fuente. Agregue el siguiente fragmento de código XML en la sección `<dict>`. 

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msauth.com.microsoft.identitysample.MSALiOS</string>
        </array>
    </dict>
</array>
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>msauthv2</string>
    <string>msauthv3</string>
</array>
```

## <a name="add-the-authentication-code"></a>Adición del código de autenticación

El [código de ejemplo](configure-authentication-sample-ios-app.md#step-4-get-the-ios-mobile-app-sample) se compone de una clase `UIViewController`. La clase:

- Define la estructura de una interfaz de usuario.
- Contiene información sobre el proveedor de identidades de Azure AD B2C. La aplicación usa esta información para establecer una relación de confianza con Azure AD B2C. 
- Contiene el código de autenticación para autenticar a los usuarios, adquirir tokens y validarlos.

Elija un `UIViewController` donde los usuarios se puedan autenticar. Combine el código con el [proporcionado aquí](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal/blob/vNext/MSALiOS/ViewController.swift) en el `UIViewController`. 

## <a name="configure-your-ios-swift-app"></a>Configuración de la aplicación Swift para iOS

Después de [agregar el código de autenticación](#add-the-authentication-code), configure la aplicación Swift para iOS con los ajustes de Azure AD B2C. Los ajustes del proveedor de identidades de Azure AD B2C se configuran en la clase `UIViewController` elegida en la sección anterior. 

Siga las instrucciones para [configurar la aplicación móvil de ejemplo](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app).

## <a name="run-and-test-the-mobile-app"></a>Ejecución y prueba de la aplicación móvil

1. Compile y ejecute el proyecto con un [simulador de un dispositivo iOS conectado](https://developer.apple.com/documentation/xcode/running-your-app-in-the-simulator-or-on-a-device).
1. Seleccione **Iniciar sesión**. A continuación, regístrese o inicie sesión con su cuenta Azure AD B2C local o social.
1. Después de la autenticación correcta, verá el nombre para mostrar en la barra de navegación.

## <a name="how-it-works"></a>¿Cómo funciona?

En esta sección se describen los bloques de creación de código que habilitan la autenticación para la aplicación Swift para iOS. Enumera los métodos de UIViewController y cómo personalizar el código. 

### <a name="instantiate-a-public-client-application"></a>Creación de instancias de una aplicación cliente pública

Las aplicaciones cliente públicas no son de confianza para mantener los secretos de aplicación de forma segura y no tienen secretos de cliente. En [viewDidLoad](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621495-viewdidload), cree una instancia de MSAL mediante el objeto de aplicación cliente pública.

El siguiente fragmento de código muestra cómo inicializar la biblioteca MSAL con un objeto de configuración `MSALPublicClientApplicationConfig`. 

El objeto de configuración proporciona información sobre el entorno de Azure AD B2C, por ejemplo, el identificador de cliente, el URI de redirección y la autoridad para compilar solicitudes de autenticación para Azure AD B2C. Para obtener información sobre el objeto de configuración, consulte [Configuración de la aplicación móvil de ejemplo](configure-authentication-sample-ios-app.md#step-5-configure-the-sample-mobile-app).

```swift
do {

    let siginPolicyAuthority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)
    let editProfileAuthority = try self.getAuthority(forPolicy: self.kEditProfilePolicy)
    
    let pcaConfig = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: kRedirectUri, authority: siginPolicyAuthority)
    pcaConfig.knownAuthorities = [siginPolicyAuthority, editProfileAuthority]
    
    self.applicationContext = try MSALPublicClientApplication(configuration: pcaConfig)
    self.initWebViewParams()
    
    } catch {
        self.updateLoggingText(text: "Unable to create application \(error)")
    }
```

El método `initWebViewParams` configura la experiencia de la [autenticación interactiva](../active-directory/develop/customize-webviews.md). 

El siguiente fragmento de código de Swift inicializa el miembro de clase `webViewParamaters` con la vista web del sistema. Para más información, consulte el artículo [Personalización de exploradores y vistas web para iOS/macOS](../active-directory/develop/customize-webviews.md).

```swift
func initWebViewParams() {
    self.webViewParamaters = MSALWebviewParameters(authPresentationViewController: self)
    self.webViewParamaters?.webviewType = .default
}
```

### <a name="interactive-authorization-request"></a>Solicitud de autorización interactiva

Una solicitud de autorización interactiva es un flujo en el que se solicita al usuario el registro o el inicio de sesión mediante la vista web del sistema. Cuando el usuario hace clic en el botón **Sign In** (Iniciar sesión), se llama el método `authorizationButton`.

El método `authorizationButton` prepara el objeto `MSALInteractiveTokenParameters` con datos relevantes sobre la solicitud de autorización. El método `acquireToken` usa el objeto `MSALInteractiveTokenParameters` para autenticar al usuario mediante la vista web del sistema.

El siguiente fragmento de código muestra cómo iniciar la solicitud de autorización interactiva. 

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
parameters.promptType = .selectAccount
parameters.authority = authority

applicationContext.acquireToken(with: parameters) { (result, error) in

// On error code    
guard let result = result else {
    self.updateLoggingText(text: "Could not acquire token: \(error ?? "No error informarion" as! Error)")
    return
}

// On success code
self.accessToken = result.accessToken
self.updateLoggingText(text: "Access token is \(self.accessToken ?? "Empty")")
}
```
 
Cuando el usuario finaliza el flujo de autorización (de forma satisfactoria o no), el resultado se devuelve a la [clausura](https://docs.swift.org/swift-book/LanguageGuide/Closures.html) del método `acquireToken`. 

El método acquireToken devuelve los objetos `result` y `error`. Use esta clausura para:

- Actualice la interfaz de usuario de la aplicación móvil con información una vez completada la autenticación.
- Llame a un servicio de API web con un token de acceso.
- Controle los errores de autenticación, por ejemplo, cuando un usuario cancela el flujo de inicio de sesión.
 
### <a name="call-a-web-api"></a>Llamada a una API de web

Para llamar a una [API web de autorización basada en tokens](enable-authentication-web-api.md), la aplicación necesita un token de acceso válido. La aplicación realiza los pasos siguientes:

1. Adquiere un token de acceso con los permisos necesarios (ámbitos) para el punto de conexión de API web.
1. Pasa el token de acceso como token de portador en el encabezado de autorización de la solicitud HTTP con este formato:

```http
Authorization: Bearer <access-token>
```

Cuando los usuarios [se autentican interactivamente](#interactive-authorization-request), la aplicación obtiene un token de acceso en la clausura de `acquireToken`. Para las llamadas API web posteriores, use el método acquire token silent (`acquireTokenSilent`), como se describe en esta sección. 

El método `acquireTokenSilent` realiza los pasos siguientes:

1. Intenta capturar un token de acceso con los ámbitos solicitados de la caché de tokens. Si el token está presente y no ha expirado, se devuelve el token. 
1. Si el token no está presente en la caché de tokens o ha expirado, la biblioteca MSAL intenta usar el token de actualización para adquirir un nuevo token de acceso. 
1. Si el token de actualización no existe o ha expirado, se devuelve una excepción. En este caso, debe pedir al usuario que [inicie sesión de forma interactiva](#interactive-authorization-request).  

El fragmento de código siguiente muestra cómo adquirir un token de acceso:

```swift
do {

// Get the authority using the sign-in or sign-up user flow
let authority = try self.getAuthority(forPolicy: self.kSignupOrSigninPolicy)

// Get the current account from the application context
guard let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy) else {
    self.updateLoggingText(text: "There is no account available!")
    return
}

// Configure the acquire token silent parameters
let parameters = MSALSilentTokenParameters(scopes: kScopes, account:thisAccount)
parameters.authority = authority
parameters.loginHint = "username"

// Acquire token silent
self.applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        
        let nsError = error as NSError
        
        // interactionRequired means we need to ask the user to sign in. This usually happens
        // when the user's Refresh Token is expired or if the user has changed their password
        // among other possible reasons.
        
        if (nsError.domain == MSALErrorDomain) {
            
            if (nsError.code == MSALError.interactionRequired.rawValue) {
                
                // Start an interactive authorization code
                // Notice we supply the account here. This ensures we acquire token for the same account
                // as we originally authenticated.
                
                ...
            }
        }
        
        self.updateLoggingText(text: "Could not acquire token: \(error)")
        return
    }
    
    guard let result = result else {
        
        self.updateLoggingText(text: "Could not acquire token: No result returned")
        return
    }
    
    // On success, set the access token to the accessToken class member. 
    // The callGraphAPI method uses the access token to call a web API  
    self.accessToken = result.accessToken
    ...
}
} catch {
self.updateLoggingText(text: "Unable to construct parameters before calling acquire token \(error)")
}
```

El método `callGraphAPI` recupera el token de acceso y llama a la API web.

```swift
@objc func callGraphAPI(_ sender: UIButton) {
    guard let accessToken = self.accessToken else {
        self.updateLoggingText(text: "Operation failed because could not find an access token!")
        return
    }
    
    let sessionConfig = URLSessionConfiguration.default
    sessionConfig.timeoutIntervalForRequest = 30
    let url = URL(string: self.kGraphURI)
    var request = URLRequest(url: url!)
    request.setValue("Bearer \(accessToken)", forHTTPHeaderField: "Authorization")
    let urlSession = URLSession(configuration: sessionConfig, delegate: self, delegateQueue: OperationQueue.main)
    
    self.updateLoggingText(text: "Calling the API....")
    
    urlSession.dataTask(with: request) { data, response, error in
        guard let validData = data else {
            self.updateLoggingText(text: "Could not call API: \(error ?? "No error informarion" as! Error)")
            return
        }
        
        let result = try? JSONSerialization.jsonObject(with: validData, options: [])
        
        guard let validResult = result as? [String: Any] else {
            self.updateLoggingText(text: "Nothing returned from API")
            return
        }
        
        self.updateLoggingText(text: "API response: \(validResult.debugDescription)")
        }.resume()
}
```

### <a name="sign-out"></a>Cierre de sesión

El cierre de sesión con MSAL quita toda la información conocida sobre un usuario de la aplicación. Use el método de cierre de sesión para cerrar la sesión de los usuarios y actualizar la interfaz de usuario. Por ejemplo, oculte los elementos protegidos de la interfaz de usuario y el botón de cierre de sesión y muestre el botón de inicio de sesión.

El siguiente fragmento de código muestra cómo cerrar la sesión de un usuario:

```swift
@objc func signoutButton(_ sender: UIButton) {
do {
    
    
    let thisAccount = try self.getAccountByPolicy(withAccounts: applicationContext.allAccounts(), policy: kSignupOrSigninPolicy)
    
    if let accountToRemove = thisAccount {
        try applicationContext.remove(accountToRemove)
    } else {
        self.updateLoggingText(text: "There is no account to signing out!")
    }
    
    ...
    
} catch  {
    self.updateLoggingText(text: "Received error signing out: \(error)")
}
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de opciones de autenticación en una aplicación Swift para iOS](enable-authentication-ios-app-options.md)
* [Habilitación de la autenticación en la API web](enable-authentication-web-api.md)
