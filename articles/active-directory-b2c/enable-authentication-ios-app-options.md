---
title: Habilitación de las opciones de aplicaciones móviles de iOS Swift mediante Azure Active Directory B2C
description: En este artículo se tratan diversas maneras de habilitar las opciones de aplicaciones móviles de iOS Swift mediante Azure Active Directory B2C.
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
ms.openlocfilehash: 128c5000e6614e769100a92303007317eadaed96
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220709"
---
# <a name="enable-authentication-options-in-an-ios-swift-app-by-using-azure-ad-b2c"></a>Habilitación de las opciones de autenticación en aplicaciones de iOS Swift mediante Azure AD B2C 

En este artículo se describen métodos para habilitar, personalizar y mejorar la experiencia de autenticación de Azure Active Directory B2C (Azure AD B2C) para una aplicación de iOS Swift. 

Antes de empezar, familiarícese con los siguientes artículos: 
* [Configuración de la autenticación en una aplicación de iOS Swift de ejemplo mediante Azure AD B2C](configure-authentication-sample-ios-app.md)
* [Habilitación de la autenticación en una aplicación de iOS Swift propia mediante Azure AD B2C](enable-authentication-ios-app.md)

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Para usar un dominio personalizado y el identificador de inquilino en la URL de autenticación, siga estos pasos: 

1. Siga las instrucciones de [Habilitación de dominios personalizados](custom-domain.md).
1. Actualice el miembro de la clase `kAuthorityHostName` con su dominio personalizado.
1. Actualice el miembro de la clase `kTenantName` con su [identificador de inquilino](tenant-management.md#get-your-tenant-id).

En el siguiente código de Swift se muestra la configuración de la aplicación antes del cambio:

```swift
let kTenantName = "contoso.onmicrosoft.com" 
let kAuthorityHostName = "contoso.b2clogin.com" 
```

En el siguiente código de Swift se muestra la configuración de la aplicación después del cambio:

```swift
let kTenantName = "00000000-0000-0000-0000-000000000000" 
let kAuthorityHostName = "login.contoso.com" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si usa una directiva personalizada, agregue la notificación de entrada necesaria, como se describe en [Configuración del inicio de sesión directo](direct-signin.md#prepopulate-the-sign-in-name). 
1. Busque el objeto de configuración de la Biblioteca de autenticación de Microsoft (MSAL) y agregue el método `withLoginHint()` con la sugerencia de inicio de sesión.

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.loginHint = "bob@contoso.com"
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Compruebe el nombre de dominio del proveedor de identidades externo. Para más información, consulte [Redirección del inicio de sesión a un proveedor social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Cree o use un objeto de lista existente para almacenar parámetros de consulta adicionales.
1. Agregue a la lista el parámetro `domain_hint` con el nombre de dominio correspondiente (por ejemplo, `facebook.com`).
1. Pase la lista de parámetros de consulta adicionales al atributo `extraQueryParameters` del objeto de configuración de MSAL.

```swift
let extraQueryParameters: [String: String] = ["domain_hint": "facebook.com"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configure la personalización de idioma](language-customization.md).
1. Cree o use un objeto de lista existente para almacenar parámetros de consulta adicionales.
1. Agregue a la lista el parámetro `ui_locales` con el código de idioma correspondiente (por ejemplo, `en-us`).
1. Pase la lista de parámetros de consulta adicionales al atributo `extraQueryParameters` del objeto de configuración de MSAL.

```swift
let extraQueryParameters: [String: String] = ["ui_locales": "en-us"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configure el elemento [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Cree o use un objeto de lista existente para almacenar parámetros de consulta adicionales.
1. Agregue el parámetro de cadena de consulta personalizado, por ejemplo, `campaignId`. Establezca el valor del parámetro (por ejemplo, `germany-promotion`).
1. Pase la lista de parámetros de consulta adicionales al atributo `extraQueryParameters` del objeto de configuración de MSAL.

```swift
let extraQueryParameters: [String: String] = ["campaignId": "germany-promotion"]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. En la directiva personalizada, defina un [perfil técnico de sugerencias de token de identificador](id-token-hint.md).
1. En el código, genere o adquiera un token de identificador y, después, establezca el token en una variable (por ejemplo, `idToken`). 
1. Cree o use un objeto de lista existente para almacenar parámetros de consulta adicionales.
1. Agregue el parámetro `id_token_hint` con la variable correspondiente que almacena el token de identificador.
1. Pase la lista de parámetros de consulta adicionales al atributo `extraQueryParameters` del objeto de configuración de MSAL.

```swift
let extraQueryParameters: [String: String] = ["id_token_hint": idToken]

let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParameters!)
parameters.promptType = .selectAccount
parameters.authority = authority
parameters.extraQueryParameters = extraQueryParameters
// More settings here

applicationContext.acquireToken(with: parameters) { (result, error) in
...
```

[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


El registrador de MSAL debe establecerse lo antes posible en la secuencia de inicio de la aplicación, antes de realizar cualquier solicitud de MSAL. Configure el [registro](../active-directory/develop/msal-logging-ios.md) de MSAL en el método `application` *AppDelegate.swift*.

En el siguiente fragmento de código se muestra cómo configurar el registro de MSAL:

```swift
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        
        MSALGlobalConfig.loggerConfig.logLevel = .verbose
        MSALGlobalConfig.loggerConfig.setLogCallback { (logLevel, message, containsPII) in
            
            // If PiiLoggingEnabled is set YES, this block will potentially contain sensitive information (Personally Identifiable Information), but not all messages will contain it.
            // containsPII == YES indicates if a particular message contains PII.
            // You might want to capture PII only in debug builds, or only if you take necessary actions to handle PII properly according to legal requirements of the region
            if let displayableMessage = message {
                if (!containsPII) {
                    #if DEBUG
                    // NB! This sample uses print just for testing purposes
                    // You should only ever log to NSLog in debug mode to prevent leaking potentially sensitive information
                    print(displayableMessage)
                    #endif
                }
            }
        }
        return true
    }
```

## <a name="embedded-web-view-experience"></a>Experiencia de vista web insertada

Se requieren exploradores web para la autenticación no interactiva. De forma predeterminada, la biblioteca MSAL usa la vista web del sistema. Durante el inicio de sesión, la biblioteca MSAL muestra la vista web del sistema de iOS con la interfaz de usuario de Azure AD B2C.  

Para más información, consulte el artículo [Personalización de exploradores y vistas web para iOS/macOS](../active-directory/develop/customize-webviews.md).

En función de sus requisitos, puede usar la vista web insertada. Hay diferencias de comportamiento visual y de inicio de sesión único entre la vista web insertada y la vista web del sistema en MSAL.

![Captura de pantalla que muestra la diferencia entre la experiencia de vista web del sistema y la experiencia de vista web insertada.](./media/enable-authentication-ios-app-options/system-web-browser-vs-embedded-view.png)

> [!IMPORTANT]
> Se recomienda usar el valor predeterminado de la plataforma, que normalmente es el explorador del sistema. El explorador del sistema la mejor opción para recordar a los usuarios que han iniciado sesión antes. Algunos proveedores de identidades, como Google, no admiten una experiencia de vista insertada.

Para cambiar este comportamiento, cambie el atributo `webviewType` de `MSALWebviewParameters` a `wkWebView`. En el ejemplo siguiente se muestra cómo cambiar el tipo de vista web a vista insertada: 

```swift
func initWebViewParams() {
    self.webViewParameters = MSALWebviewParameters(authPresentationViewController: self)
    
    // Use embedded view experience
    self.webViewParameters?.webviewType = .wkWebView
}
```

## <a name="next-steps"></a>Pasos siguientes

- Para más información, consulte [Opciones de configuración de MSAL para iOS Swift](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki).
