---
title: Personalización de los exploradores y las vistas web (MSAL para iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo personalizar la experiencia del explorador de MSAL para iOS y macOS cuando los usuarios inician sesión.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: a4523634daee427fb86ef288ab68cdc158cfeb0f
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039969"
---
# <a name="customize-browsers-and-webviews-for-iosmacos"></a>Personalizar exploradores y vistas web para iOS/macOS

Se requiere un explorador web para la autenticación no interactiva. En iOS y macOS 10.15+, la biblioteca de autenticación de Microsoft (MSAL) usa el explorador web del sistema de forma predeterminada (que puede aparecer en la parte superior de la aplicación) para realizar la autenticación interactiva cuando los usuarios inician sesión. El uso del explorador del sistema tiene la ventaja de compartir el estado de inicio de sesión único (SSO) con otras aplicaciones y con aplicaciones web.

Puede cambiar la experiencia personalizando la configuración de otras opciones para mostrar el contenido web, como:

Solo para iOS:

- [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession?language=objc) 
- [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller?language=objc)

Para iOS y macOS:

- [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc)
- [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview?language=objc).

MSAL para macOS solo admite `WKWebView` en versiones anteriores del sistema operativo. `ASWebAuthenticationSession` solo se admite en macOS 10.15 y versiones posteriores. 

## <a name="system-browsers"></a>Exploradores del sistema

Para iOS, `ASWebAuthenticationSession`, `SFAuthenticationSession` y `SFSafariViewController` se consideran exploradores del sistema. Para macOS, solo está disponible `ASWebAuthenticationSession`. En general, los exploradores del sistema comparten cookies y otros datos de sitios web con la aplicación de explorador Safari.

De forma predeterminada, MSAL detectará dinámicamente la versión de iOS y seleccionará el explorador del sistema recomendado disponible en esa versión. En iOS 12+, será `ASWebAuthenticationSession`. 

### <a name="default-configuration-for-ios"></a>Configuración predeterminada para iOS

| Versión | Explorador web |
|:-------------:|:-------------:|
| iOS 12+ | ASWebAuthenticationSession |
| iOS 11 | SFAuthenticationSession |
| iOS 10 | SFSafariViewController |

### <a name="default-configuration-for-macos"></a>Configuración predeterminada para macOS

| Versión | Explorador web |
|:-------------:|:-------------:|
| macOS 10.15+ | ASWebAuthenticationSession |
| otras versiones | WKWebView |

Los desarrolladores también pueden seleccionar otro explorador del sistema para las aplicaciones MSAL:

- `SFAuthenticationSession` es la versión de iOS 11 de `ASWebAuthenticationSession`.
- `SFSafariViewController` es una finalidad más general y proporciona una interfaz para explorar la web y también se puede usar para el inicio de sesión. En iOS 9 y 10, las cookies y otros datos del sitio web se comparten con Safari, pero no en iOS 11 y versiones posteriores.

## <a name="in-app-browser"></a>Explorador en aplicación

[WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) es un explorador en aplicación que muestra contenido Web. No comparte cookies ni datos de sitios web con otras instancias de **WKWebView** o con el explorador Safari. WKWebView es un explorador multiplataforma que está disponible para iOS y macOS.

## <a name="cookie-sharing-and-single-sign-on-sso-implications"></a>Implicaciones de uso compartido de cookies e inicio de sesión único (SSO)

El explorador que utiliza afecta a la experiencia de SSO debido a cómo comparten las cookies. En las tablas siguientes se resumen las experiencias de SSO por explorador.

| Technology    | Tipo de explorador  | Disponibilidad de iOS | Disponibilidad de macOS | Comparte cookies y otros datos  | Disponibilidad de MSAL | SSO |
|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|:-------------:|-------------:|
| [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession) | Sistema | iOS12 y versiones posteriores | macOS 10.15 y versiones posteriores | Sí | iOS y macOS 10.15+ | Instancias de w/ Safari
| [SFAuthenticationSession](https://developer.apple.com/documentation/safariservices/sfauthenticationsession) | Sistema | iOS11 y versiones posteriores | N/D | Sí | Únicamente iOS |  Instancias de w/ Safari
| [SFSafariViewController](https://developer.apple.com/documentation/safariservices/sfsafariviewcontroller) | Sistema | iOS11 y versiones posteriores | N/D | No | Únicamente iOS | No**
| **SFSafariViewController** | Sistema | iOS10 | N/D | Sí | Únicamente iOS |  Instancias de w/ Safari
| **WKWebView**  | En aplicación | iOS8 y versiones posteriores | macOS 10.10 y versiones posteriores | No | iOS y macOS | No**

** Para que el inicio de sesión único funcione, los tokens deben compartirse entre aplicaciones. Esto requiere una memoria caché de tokens o una aplicación de agente, como Microsoft Authenticator para iOS.

## <a name="change-the-default-browser-for-the-request"></a>Cambio del explorador predeterminado para la solicitud

Puede usar un explorador en aplicación o un explorador del sistema específico según los requisitos de la experiencia de usuario; para ello, cambie la siguiente propiedad en `MSALWebviewParameters`:

```objc
@property (nonatomic) MSALWebviewType webviewType;
```

## <a name="change-per-interactive-request"></a>Cambio por solicitud interactiva

Cada solicitud puede configurarse para invalidar el explorador predeterminado si cambia la propiedad `MSALInteractiveTokenParameters.webviewParameters.webviewType` antes de pasarla a la API `acquireTokenWithParameters:completionBlock:`.

Además, MSAL admite el paso de un `WKWebView` personalizado estableciendo la propiedad `MSALInteractiveTokenParameters.webviewParameters.customWebView`.

Por ejemplo:

Objective-C
```objc
UIViewController *myParentController = ...;
WKWebView *myCustomWebView = ...;
MSALWebviewParameters *webViewParameters = [[MSALWebviewParameters alloc] initWithAuthPresentationViewController:myParentController];
webViewParameters.webviewType = MSALWebviewTypeWKWebView;
webViewParameters.customWebview = myCustomWebView;
MSALInteractiveTokenParameters *interactiveParameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"myscope"] webviewParameters:webViewParameters];
    
[app acquireTokenWithParameters:interactiveParameters completionBlock:completionBlock];
```
Swift
```swift
let myParentController: UIViewController = ...
let myCustomWebView: WKWebView = ...
let webViewParameters = MSALWebviewParameters(authPresentationViewController: myParentController)
webViewParameters.webviewType = MSALWebviewType.wkWebView
webViewParameters.customWebview = myCustomWebView
let interactiveParameters = MSALInteractiveTokenParameters(scopes: ["myscope"], webviewParameters: webViewParameters)

app.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
```

Si usa una vista web personalizada, las notificaciones se usan para indicar el estado del contenido web que se muestra, por ejemplo:

```objc
/*! Fired at the start of a resource load in the webview. The URL of the load, if available, will be in the @"url" key in the userInfo dictionary */
extern NSString *MSALWebAuthDidStartLoadNotification;

/*! Fired when a resource finishes loading in the webview. */
extern NSString *MSALWebAuthDidFinishLoadNotification;

/*! Fired when web authentication fails due to reasons originating from the network. Look at the @"error" key in the userInfo dictionary for more details.*/
extern NSString *MSALWebAuthDidFailNotification;

/*! Fired when authentication finishes */
extern NSString *MSALWebAuthDidCompleteNotification;

/*! Fired before ADAL invokes the broker app */
extern NSString *MSALWebAuthWillSwitchToBrokerApp;
```

### <a name="options"></a>Opciones

Todos los tipos de explorador web compatibles con MSAL se declaran en la [enumeración MSALWebviewType](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALDefinitions.h#L47)

```objc
typedef NS_ENUM(NSInteger, MSALWebviewType)
{
    /**
     For iOS 11 and up, uses AuthenticationSession (ASWebAuthenticationSession or SFAuthenticationSession).
     For older versions, with AuthenticationSession not being available, uses SafariViewController.
     For macOS 10.15 and above uses ASWebAuthenticationSession
     For older macOS versions uses WKWebView
     */
    MSALWebviewTypeDefault,
    
    /** Use ASWebAuthenticationSession where available.
     On older iOS versions uses SFAuthenticationSession
     Doesn't allow any other webview type, so if either of these are not present, fails the request*/
    MSALWebviewTypeAuthenticationSession,
    
#if TARGET_OS_IPHONE
    
    /** Use SFSafariViewController for all versions. */
    MSALWebviewTypeSafariViewController,
    
#endif
    /** Use WKWebView */
    MSALWebviewTypeWKWebView,
};
```

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [flujos de autenticación y escenarios de aplicaciones](authentication-flows-app-scenarios.md).
