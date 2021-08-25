---
title: Inicio de sesión único entre aplicaciones de ADAL y MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Obtener información sobre cómo compartir el inicio de sesión único entre aplicaciones de ADAL y de MSAL
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 548a6ff767b6dd11428b8c1a147534da26c05b61
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122428969"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Procedimiento: Inicio de sesión único entre aplicaciones de ADAL y de MSAL en macOS e iOS

La Biblioteca de autenticación de Microsoft (MSAL) para iOS puede compartir el estado del inicio de sesión único con [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) entre aplicaciones. Puede migrar sus aplicaciones a MSAL a su propio ritmo, asegurándose así de que los usuarios seguirán disfrutando del inicio de sesión único entre aplicaciones, incluso con una combinación de aplicaciones basadas en ADAL y MSAL.

Si está buscando orientación para configurar el inicio de sesión único entre aplicaciones mediante el SDK de MSAL, consulte [Inicio de sesión único silencioso entre varias aplicaciones](single-sign-on-macos-ios.md#silent-sso-between-apps). Este artículo se centra en el inicio de sesión único entre ADAL y MSAL.

Los detalles para implementar el inicio de sesión único dependen de la versión de ADAL que esté usando.

## <a name="adal-27x"></a>ADAL 2.7. x

En esta sección se describen las diferencias del inicio de sesión único entre MSAL 2.7 y ADAL 2.7. x.

### <a name="cache-format"></a>Formato de la caché

ADAL 2.7. x puede leer el formato de la caché de MSAL. No es necesario hacer nada especial en el inicio de sesión único entre aplicaciones con la versión 2.7. x de ADAL. Sin embargo, debe tener en cuenta las diferencias en los identificadores de cuenta que admiten esas dos bibliotecas.

### <a name="account-identifier-differences"></a>Diferencias de los identificadores de cuenta

MSAL y ADAL usan identificadores de cuenta diferentes. ADAL usa UPN como su identificador de cuenta principal. MSAL, en cambio, usa un identificador de cuenta que no se puede mostrar y que se basa en un id. de objeto y un id. de inquilino para las cuentas de AAD, así como en una notificación `sub` para otros tipos de cuentas.

Cuando recibe un objeto `MSALAccount` en el resultado de MSAL, este contiene un identificador de cuenta en la propiedad `identifier`. La aplicación debe usar este identificador para realizar las solicitudes silenciosas posteriores.

Además del objeto `identifier`, el objeto `MSALAccount` contiene un identificador que se puede mostrar llamado `username`. Esto se traduce en `userId` en ADAL. `username` no se considera un identificador único y puede cambiar en cualquier momento, por lo que solo se debe usar en escenarios de compatibilidad con versiones anteriores de ADAL. MSAL admite consultas de la caché mediante `username` o `identifier`, donde se recomienda realizar consultas mediante `identifier`.

En la tabla siguiente se resumen las diferencias de identificador de cuenta entre ADAL y MSAL:

| Identificador de cuenta                | MSAL                                                         | ADAL 2.7. x      | Versión de ADAL anterior (antes de ADAL 2.7. x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| identificador que se puede mostrar            | `username`                                                   | `userId`        | `userId`                       |
| identificador único que no se puede mostrar | `identifier`                                                 | `homeAccountId` | N/D                            |
| No se conoce el id. de cuenta               | Consultar todas las cuentas a través de la API de `allAccounts:` en `MSALPublicClientApplication` | N/D             | N/D                            |

Esta es la interfaz `MSALAccount` que proporciona esos identificadores:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO de MSAL a ADAL

Si tiene una aplicación MSAL y una aplicación ADAL y el usuario inicia sesión primero en la aplicación basada en MSAL, puede obtener el inicio de sesión único en la aplicación ADAL si guarda el `username` del objeto `MSALAccount` y pasándolo a su aplicación basada en ADAL como `userId`. ADAL puede buscar la información de la cuenta de forma silenciosa gracias a la API `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:`.

### <a name="sso-from-adal-to-msal"></a>Inicio de sesión único de ADAL a MSAL

Si tiene una aplicación MSAL y una aplicación ADAL, y el usuario inicia sesión primero en la aplicación basada en ADAL, puede usar los identificadores de usuario de ADAL para realizar las búsquedas de cuentas en MSAL. Esto también se aplica al migrar de ADAL a MSAL.

#### <a name="adals-homeaccountid"></a>Elemento HomeAccountId de ADAL

ADAL 2.7. x devuelve el `homeAccountId` del objeto `ADUserInformation` en el resultado a través de esta propiedad:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` en ADAL es equivalente a `identifier` en MSAL. Puede guardar este identificador para usarlo en MSAL para realizar búsquedas de cuenta con la API `accountForIdentifier:error:`.

#### <a name="adals-userid"></a>`userId` de ADAL

Si `homeAccountId` no está disponible o solo tiene el identificador que se puede mostrar, puede usar el `userId` de ADAL para buscar la cuenta en MSAL.

En MSAL, primero debe buscar una cuenta mediante `username` o `identifier`. Use siempre `identifier` para realizar consultas, si lo tiene, y use `username` como reserva. Si encuentra la cuenta, úsela en las llamadas a `acquireTokenSilent`.

Objective-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



API de búsqueda de cuentas que admite MSAL:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

En esta sección se describen las diferencias del inicio de sesión único entre MSAL y ADAL 2.x-2.6.6.

Las versiones anteriores de ADAL no admiten de forma nativa el formato de la caché de MSAL. Sin embargo, para garantizar una migración fluida de ADAL a MSAL, MSAL puede leer el formato anterior de la caché de ADAL sin tener preguntar de nuevo las credenciales de usuario.

Como `homeAccountId` no está disponible en las versiones anteriores de ADAL, debe buscar cuentas con `username`:

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Por ejemplo:

Objective-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Como alternativa, puede leer todas las cuentas, que a su vez leerán la información de la cuenta de ADAL:

Objective-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Pasos siguientes

Más información sobre [flujos de autenticación y escenarios de aplicaciones](authentication-flows-app-scenarios.md).
