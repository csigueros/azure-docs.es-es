---
title: Uso de identificadores URI de redirección con MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Conozca las diferencias entre la Biblioteca de autenticación de Microsoft para ObjectiveC (MSAL para iOS y macOS) y la Biblioteca de autenticación de Azure AD para ObjectiveC (ADAL.ObjC) y cómo migrar entre ellas.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: jak
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 6c70bb76b348fd6c2f4d950423d756ae700de576
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429016"
---
# <a name="using-redirect-uris-with-the-microsoft-authentication-library-for-ios-and-macos"></a>Uso de los URI de redirección con la biblioteca de autenticación de Microsoft para iOS y macOS

Cuando un usuario se autentica, Azure Active Directory (Azure AD) envía el token a la aplicación mediante el URI de redirección registrado en la aplicación de Azure AD.

La biblioteca de autenticación de Microsoft (MSAL) requiere que el URI de redirección se registre en la aplicación de Azure AD en un formato específico. MSAL usa un URI de redirección predeterminado, si no se especifica uno. El formato es `msauth.[Your_Bundle_Id]://auth`.

El formato del URI de redirección predeterminado funciona con la mayoría de las aplicaciones y escenarios, como la autenticación asincrónica y la vista web del sistema. Use el formato predeterminado siempre que sea posible.

Sin embargo, puede que necesite cambiar el URI de redirección en escenarios avanzados, como se describe a continuación.

## <a name="scenarios-that-require-a-different-redirect-uri"></a>Escenarios que requieren un URI de redirección diferente

### <a name="cross-app-single-sign-on-sso"></a>Inicio de sesión único (SSO) entre aplicaciones

Para que la plataforma de identidad de Microsoft comparta tokens entre aplicaciones, cada aplicación debe tener el mismo identificador de cliente o de aplicación. Este es el identificador único que se proporciona cuando se registra la aplicación en el portal (no el identificador de agrupación de aplicaciones que se registra por aplicación en Apple).

Los URI de redirección deben ser diferentes en cada aplicación de iOS. Esto permite al servicio de identidad de Microsoft identificar de forma exclusiva las distintas aplicaciones que comparten un identificador de aplicación. Cada aplicación puede tener varios URI de redirección registrados en Azure Portal. Cada una de las aplicaciones de su conjunto tendrá diferentes URI de redirección. Por ejemplo:

Dado el siguiente registro de aplicación en Azure Portal:

* Identificador de cliente: `ABCDE-12345` (es un identificador de cliente único)
* RedirectUris: `msauth.com.contoso.app1://auth`, `msauth.com.contoso.app2://auth`, `msauth.com.contoso.app3://auth`

App1 usa `msauth.com.contoso.app1://auth` de redireccionamiento.\
App2 usa `msauth.com.contoso.app2://auth`.\
App3 usa `msauth.com.contoso.app1://auth`.

### <a name="migrating-from-adal-to-msal"></a>Migración de ADAL a MSAL

Al migrar el código que se usó en la biblioteca de autenticación de Azure AD (ADAL) a MSAL, es posible que ya tenga configurado un URI de redirección para la aplicación. Puede seguir usando el mismo siempre y cuando la aplicación de ADAL esté configurada para admitir escenarios asincrónicos y dicho URI satisfaga los requisitos de formato de los URI de redirección de MSAL.

## <a name="msal-redirect-uri-format-requirements"></a>Requisitos de formato del URI de redirección de MSAL

* El URI de redirección de MSAL debe tener el formato `<scheme>://host`.

    Donde `<scheme>` es una cadena única que identifica la aplicación. Se basa principalmente en el identificador de agrupación de la aplicación para garantizar la exclusividad. Por ejemplo, si el identificador de agrupación de la aplicación es `com.contoso.myapp`, el URI de redirección tendrá el formato: `msauth.com.contoso.myapp://auth`.

    Si va a migrar desde ADAL, es probable que el URI de redirección tenga este formato: `<scheme>://[Your_Bundle_Id]`, donde `scheme` es una cadena única. Este formato seguirá funcionando cuando use MSAL.

* `<scheme>` debe estar registrado en el archivo Info.plist de la aplicación en `CFBundleURLTypes > CFBundleURLSchemes`.  En este ejemplo, se ha abierto Info.plist como código fuente:

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>msauth.[BUNDLE_ID]</string>
            </array>
        </dict>
    </array>
    ```

MSAL comprobará si el URI de redirección se registra correctamente y, si no es así, devolverá un error.
    
* Si quiere usar vínculos universales como URI de redirección, `<scheme>` debe ser `https` y no es necesario declararlo en `CFBundleURLSchemes`. En su lugar, configure la aplicación y el dominio según las instrucciones de Apple en [Universal Links for Developers](https://developer.apple.com/ios/universal-links/) (Vínculos universales para desarrolladores) y llame al método `handleMSALResponse:sourceApplication:` de `MSALPublicClientApplication` cuando la aplicación se abra mediante un vínculo universal.

## <a name="use-a-custom-redirect-uri"></a>Uso del URI de redirección personalizado

Para usar un URI de redirección personalizado, pase el parámetro `redirectUri` a `MSALPublicClientApplicationConfig` y pase ese objeto a `MSALPublicClientApplication` al inicializar el objeto. Si el URI de redirección no es válido, el inicializador devolverá `nil` y establecerá la información adicional de `redirectURIError`.  Por ejemplo:

Objective-C:

```objc
MSALPublicClientApplicationConfig *config =
        [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                        redirectUri:@"your-redirect-uri"
                                                        authority:authority];
NSError *redirectURIError;
MSALPublicClientApplication *application =
        [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&redirectURIError];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: authority)
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```



## <a name="handle-the-url-opened-event"></a>Administración de los eventos abiertos con la dirección URL

La aplicación debe llamar a MSAL cuando reciba cualquier respuesta mediante esquemas de dirección URL o vínculos universales. Llame al método `handleMSALResponse:sourceApplication:` de `MSALPublicClientApplication` cuando se abra la aplicación. Este es un ejemplo de esquemas personalizados:

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```



## <a name="next-steps"></a>Pasos siguientes

Más información sobre [flujos de autenticación y escenarios de aplicaciones](authentication-flows-app-scenarios.md).
