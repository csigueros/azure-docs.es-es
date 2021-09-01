---
title: Solicitud de notificaciones personalizadas (MSAL para iOS y macOS) | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo solicitar notificaciones personalizadas.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/26/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 70a883fc1c2cd78124c9efe44a54cc528ee5426c
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039924"
---
# <a name="request-custom-claims-using-msal-for-ios-and-macos"></a>solicitar notificaciones personalizadas mediante MSAL para iOS y macOS

OpenID Connect le permite solicitar opcionalmente la devolución de notificaciones individuales desde el punto de conexión UserInfo o en el token de identificación. Una solicitud de notificaciones se representa como un objeto JSON que contiene una lista de notificaciones solicitadas. Consulte [OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1_0-final.html#ClaimsParameter) para obtener más detalles.

La Biblioteca de autenticación de Microsoft (MSAL) para iOS y macOS permite solicitar notificaciones específicas en escenarios de adquisición de tokens interactivos y silenciosos. Asimismo, lo hace mediante el parámetro `claimsRequest`.

Existen varios escenarios en los que esta opción necesaria. Por ejemplo:

- Solicitud de notificaciones fuera del conjunto estándar de la aplicación.
- Solicitud de combinaciones específicas de las notificaciones estándar que no se pueden especificar mediante ámbitos para la aplicación. Por ejemplo, si se rechaza un token de acceso debido a que faltan notificaciones, la aplicación puede solicitar esas notificaciones que faltan mediante MSAL.

> [!NOTE]
> MSAL omite la caché del token de acceso cada vez que se especifica una solicitud de notificaciones. Es importante proporcionar solo el parámetro `claimsRequest` cuando se necesitan notificaciones adicionales (en lugar de proporcionar siempre el mismo parámetro `claimsRequest` en cada llamada a la API de MSAL).

`claimsRequest` se puede especificar en `MSALSilentTokenParameters` y `MSALInteractiveTokenParameters`:

```objc
/*!
 MSALTokenParameters is the base abstract class for all types of token parameters (silent and interactive).
 */
@interface MSALTokenParameters : NSObject

/*!
 The claims parameter that needs to be sent to authorization or token endpoint.
 If claims parameter is passed in silent flow, access token will be skipped and refresh token will be tried.
 */
@property (nonatomic, nullable) MSALClaimsRequest *claimsRequest;

@end
```
`MSALClaimsRequest` se puede crear a partir de una representación NSString de la solicitud de notificaciones JSON. 

Objective-C:

```objc
NSError *claimsError = nil;
MSALClaimsRequest *request = [[MSALClaimsRequest alloc] initWithJsonString:@"{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}" error:&claimsError];
```

Swift:

```swift
var requestError: NSError? = nil
let request = MSALClaimsRequest(jsonString: "{\"id_token\":{\"auth_time\":{\"essential\":true},\"acr\":{\"values\":[\"urn:mace:incommon:iap:silver\"]}}}",
                                        error: &requestError)
```



También se puede modificar solicitando notificaciones específicas adicionales:

Objective-C:

```objc
MSALIndividualClaimRequest *individualClaimRequest = [[MSALIndividualClaimRequest alloc] initWithName:@"custom_claim"];
individualClaimRequest.additionalInfo = [MSALIndividualClaimRequestAdditionalInfo new];
individualClaimRequest.additionalInfo.essential = @1;
individualClaimRequest.additionalInfo.value = @"myvalue";
[request requestClaim:individualClaimRequest forTarget:MSALClaimsRequestTargetIdToken error:&claimsError];
```

Swift:

```swift
let individualClaimRequest = MSALIndividualClaimRequest(name: "custom-claim")
let additionalInfo = MSALIndividualClaimRequestAdditionalInfo()
additionalInfo.essential = 1
additionalInfo.value = "myvalue"
individualClaimRequest.additionalInfo = additionalInfo
do {
  try request.requestClaim(individualClaimRequest, for: .idToken)
} catch let error as NSError {
  // handle error here  
}
        
```



A continuación, `MSALClaimsRequest` debe establecerse en los parámetros del token y proporcionarse a una de las API de las adquisiciones de tokens de MSAL:

Objective-C:

```objc
MSALPublicClientApplication *application = ...;
MSALWebviewParameters *webParameters = ...;

MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"user.read"]
                                                                                  webviewParameters:webParameters];
parameters.claimsRequest = request;
    
[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let webParameters: MSALWebviewParameters!
        
let parameters = MSALInteractiveTokenParameters(scopes: ["user.read"], webviewParameters: webParameters)
parameters.claimsRequest = request
        
application.acquireToken(with: parameters) { (result: MSALResult?, error: Error?) in            ...

```



## <a name="next-steps"></a>Pasos siguientes

Más información sobre [flujos de autenticación y escenarios de aplicaciones](authentication-flows-app-scenarios.md).
