---
title: Configuración del inicio de sesión único en macOS e iOS
titleSuffix: Microsoft identity platform
description: Aprenda a configurar el inicio de sesión único (SSO) en macOS e iOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 94174eeb943f4016fde8baf329fa6ba27768e486
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039939"
---
# <a name="configure-sso-on-macos-and-ios"></a>Configuración del inicio de sesión único en macOS e iOS

La biblioteca de autenticación de Microsoft (MSAL) para macOS e iOS admite el inicio de sesión único (SSO) entre aplicaciones de macOS e iOS y los exploradores. En este artículo se tratan los siguientes escenarios de SSO:

- [SSO silencioso entre varias aplicaciones](#silent-sso-between-apps)

Este tipo de SSO funciona en varias aplicaciones que haya distribuido el mismo desarrollador de Apple. Asimismo, proporciona un SSO silencioso (es decir, no se solicitan credenciales al usuario) mediante la lectura de tokens de actualización que han escrito otras aplicaciones desde la cadena de claves e intercambiando tokens de acceso de forma silenciosa.  

- [SSO a través del agente de autenticación](#sso-through-authentication-broker-on-ios)

> [!IMPORTANT]
> Este flujo no está disponible en macOS.

Microsoft proporciona aplicaciones, denominadas agentes, que habilitan el inicio de sesión único entre aplicaciones de distintos proveedores, siempre que el dispositivo móvil se registre con Azure Active Directory (AAD). Este tipo de SSO requiere la instalación de una aplicación de agente en el dispositivo del usuario.

- **SSO entre MSAL y Safari**

El SSO se logra a través de la clase [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc). Usa el estado de inicio de sesión existente en otras aplicaciones y el explorador Safari. Por ello, no se limita a las aplicaciones que distribuye el mismo desarrollador de Apple, pero sí necesita cierta interacción del usuario.

Si usa la vista web predeterminada en la aplicación para iniciar la sesión de los usuarios, obtendrá un SSO automático entre las aplicaciones basadas en MSAL y Safari. Para obtener más información sobre las vistas web que admite MSAL, consulte [Personalizar exploradores y vistas web](customize-webviews.md).

> [!IMPORTANT]
> Este tipo de SSO no está disponible actualmente en macOS. MSAL en macOS solo admite un elemento WKWebView que no admita SSO con Safari. 

- **SSO silencioso entre ADAL y MSAL para aplicaciones de macOS e iOS**

Objective-C de MSAL admite la migración y el inicio de sesión único con aplicaciones basadas en Objective-C de ADAL. Las aplicaciones deben distribuirse mediante el mismo desarrollador de Apple.

Consulte [SSO entre las aplicaciones ADAL y MSAL en macOS e iOS](sso-between-adal-msal-apps-macos-ios.md) para obtener instrucciones sobre el inicio de sesión único entre aplicaciones de ADAL y aquellas basadas en MSAL.

## <a name="silent-sso-between-apps"></a>SSO silencioso entre aplicaciones

MSAL admite el uso compartido de SSO a través de grupos de acceso a cadenas de claves de iOS.

Para habilitar el inicio de sesión único en todas las aplicaciones, deberá realizar los pasos siguientes, que se explican con más detalle a continuación:

1. Asegúrese de que todas las aplicaciones usan el mismo id. de cliente o de aplicación.
1. Garantizar que todas las aplicaciones comparten el mismo certificado de firma de Apple para que sea posible compartir las cadenas de claves.
1. Solicitar los mismos permisos de llaveros para cada una de las aplicaciones.
1. Indique a los SDK de MSAL la cadena de claves compartida que quiere usar si es diferente de la predeterminada.

### <a name="use-the-same-client-id-and-application-id"></a>Usar el mismo id. de cliente y de aplicación

Para que la plataforma de identidad de Microsoft sepa qué aplicaciones pueden compartir tokens, esas aplicaciones deben compartir el mismo id. de cliente o de aplicación. Se trata del identificador único que se le suministró al registrar su primera aplicación en el portal.

El modo en que la plataforma de identidad de Microsoft indica a las aplicaciones que usen el mismo id. de aplicación son los **URI de redirección**. Cada aplicación puede tener varios URI de redirección registrados en el portal de incorporación. Cada una de las aplicaciones de su conjunto tendrá diferentes URI de redirección. Por ejemplo:

URI de redirección de la aplicación 1: `msauth.com.contoso.mytestapp1://auth`  
URI de redirección de la aplicación 2: `msauth.com.contoso.mytestapp2://auth`  
URI de redirección de la aplicación 3: `msauth.com.contoso.mytestapp3://auth`  

> [!IMPORTANT]
> El formato de los URI de redirección debe ser compatible con el formato de MSAL, que se puede consultar en [Requisitos de formato de las URI de redireccionamiento de MSAL](redirect-uris-ios.md#msal-redirect-uri-format-requirements).

### <a name="setup-keychain-sharing-between-applications"></a>Configurar el uso compartido de cadenas de claves entre aplicaciones

Para habilitar el uso compartido de cadenas de claves, consulte el artículo [Agregar capacidades](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html) de Apple. Lo importante es que decida cómo quiere que se llame a la cadena de claves y agregue esa capacidad a todas las aplicaciones que vayan a participar en el inicio de sesión único.

Cuando tenga los derechos configurados correctamente, verá un archivo `entitlements.plist` en el directorio del proyecto que contiene algo parecido a este ejemplo:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

#### <a name="add-a-new-keychain-group"></a>Agregar un nuevo grupo de cadena de claves

Agregue un nuevo grupo de cadena de claves al proyecto **Capacidades**. El grupo de cadena de claves debe ser:
* `com.microsoft.adalcache` en iOS. 
* `com.microsoft.identity.universalstorage` en macOS.

![Ejemplo de cadena de claves](media/single-sign-on-macos-ios/keychain-example.png)

Para más información, consulte [Grupos de cadena de claves](howto-v2-keychain-objc.md).

## <a name="configure-the-application-object"></a>Configuración del objeto de la aplicación

Una vez que tenga habilitado el derecho de la cadena de claves en cada una de sus aplicaciones y esté listo para usar SSO, configure `MSALPublicClientApplication` con el grupo de acceso a la cadena de claves, tal como se indica en el ejemplo siguiente:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<my-client-id>"];
configuration.cacheConfig.keychainSharingGroup = @"my.keychain.group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:configuration error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<my-client-id>")
config.cacheConfig.keychainSharingGroup = "my.keychain.group"

do {
   let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
} catch let error as NSError {
  // handle error here
}
```

> [!WARNING]
> Cuando se comparte una cadena de claves entre aplicaciones, cualquiera de ellas puede eliminar usuarios o eliminar todos los tokens de la aplicación.
> Esto puede resultar especialmente problemático si tiene aplicaciones que dependen de los tokens para hacer el trabajo en segundo plano.
> Compartir una cadena de claves significa que debe tener muchísimo cuidado cuando la aplicación use las operaciones de eliminación del SDK de Microsoft Identity.

Eso es todo. El SDK de Microsoft Identity ahora compartirá las credenciales entre todas sus aplicaciones. La lista de cuentas también se compartirá entre las instancias de la aplicación.

## <a name="sso-through-authentication-broker-on-ios"></a>SSO a través del agente de autenticación en iOS

MSAL proporciona compatibilidad para la autenticación asíncrona con Microsoft Authenticator. Asimismo, Microsoft Authenticator proporciona SSO para dispositivos registrados de AAD y también ayuda a la aplicación a seguir las directivas de acceso condicional.

En los pasos siguientes se describe cómo habilitar el inicio de sesión único mediante un agente de autenticación para la aplicación:

1. Registre un formato de URI de redireccionamiento compatible con el agente en el archivo info.plist de la aplicación. El formato de URI de redireccionamiento compatible con el agente es `msauth.<app.bundle.id>://auth`. Reemplace "<app.bundle.id>" con el id. de agrupación de la aplicación. Por ejemplo:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

1. Agregue los siguientes esquemas al archivo Info.plist de la aplicación en `LSApplicationQueriesSchemes`:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

1. Agregue lo siguiente al archivo `AppDelegate.m` para controlar las devoluciones de llamada:

    Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```
    
**Si usa Xcode 11**, debe colocar la devolución de llamada de MSAL en el archivo `SceneDelegate` en su lugar.
Si admite UISceneDelegate y UIApplicationDelegate para la compatibilidad con sistemas operativos iOS anteriores, la devolución de llamada de MSAL debe colocarse en ambos archivos.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [flujos de autenticación y escenarios de aplicaciones](authentication-flows-app-scenarios.md).