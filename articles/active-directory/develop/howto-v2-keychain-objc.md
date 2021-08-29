---
title: Configuración del llavero
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo configurar el llavero para que su aplicación pueda almacenar en caché los tokens en el llavero.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: dd8ad7f8c07a164d8c8bdac56d7ea3b57ef3aecc
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122419092"
---
# <a name="configure-keychain"></a>Configuración del llavero

Cuando la [biblioteca de autenticación de Microsoft para iOS y macOS (MSAL)](msal-overview.md) inicia la sesión de un usuario o actualiza un token, intenta almacenar en caché los tokens en el llavero. Al almacenar en caché los tokens en el llavero, permite a MSAL proporcionar un inicio de sesión único (SSO) silencioso entre varias aplicaciones distribuidas por el mismo desarrollador de Apple. SSO se consigue mediante la funcionalidad de grupos de acceso al llavero. Para más información, consulte la [documentación de los elementos del llavero](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) de Apple.

En este artículo se explica cómo configurar los derechos de la aplicación para que MSAL pueda escribir tokens en caché en el llavero de iOS y macOS.

## <a name="default-keychain-access-group"></a>Grupo de acceso al llavero predeterminado

### <a name="ios"></a>iOS

MSAL en iOS usa el grupo de acceso `com.microsoft.adalcache` de forma predeterminada. Este es el grupo de acceso compartido que usan los SDK de MSAL y de la biblioteca de autenticación de Azure AD (ADAL), y garantiza la mejor experiencia de inicio de sesión único (SSO) entre varias aplicaciones del mismo editor.

En iOS, agregue el grupo del llavero `com.microsoft.adalcache` a los derechos de su aplicación en XCode, en **Project settings** > **Capabilities** > **Keychain sharing** (Configuración del proyecto > Funcionalidad > Uso compartido del llavero).

### <a name="macos"></a>macOS

MSAL en macOS usa el grupo de acceso `com.microsoft.identity.universalstorage` de forma predeterminada.

Debido a las limitaciones del llavero de macOS, `access group` de MSAL no se traduce directamente al atributo de grupo de acceso al llavero (consulte [kSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) en macOS 10.14 y versiones anteriores. Sin embargo, se comporta de forma similar desde una perspectiva del SSO, ya que garantiza que varias aplicaciones distribuidas por el mismo desarrollador de Apple puedan tener un inicio de sesión único en modo silencioso.

En macOS 10.15 (macOS Catalina), MSAL usa el atributo de grupo de acceso al llavero para lograr el inicio de sesión único en modo silencioso, de forma similar a iOS.

## <a name="custom-keychain-access-group"></a>Grupo de acceso al llavero personalizado

Si desea usar un grupo de acceso al llavero diferente, puede pasar el grupo personalizado al crear `MSALPublicClientApplicationConfig`, antes de crear `MSALPublicClientApplication`, de la siguiente manera:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Deshabilitar uso compartido del llavero

Si no desea compartir el estado de SSO entre varias aplicaciones ni usar ningún grupo de acceso al llavero, deshabilite el uso compartido del llavero pasando el identificador del paquete de aplicación como keychainGroup:

# <a name="objective-c"></a>[Objective-C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Identificador: error 34018 (no se pudo establecer el elemento en el llavero)

El error 34018 normalmente significa que el llavero no se ha configurado correctamente. Asegúrese de que el grupo de acceso al llavero que se ha configurado en MSAL coincide con el que se ha configurado en los derechos.

## <a name="ensure-your-application-is-properly-signed"></a>Asegúrese de que la aplicación está firmada correctamente.

En macOS, las aplicaciones pueden ejecutarse sin que el desarrollador las firme. Aunque la mayoría de las funcionalidades de MSAL seguirán funcionando, para realizar el SSO mediante acceso al llavero es necesario que la aplicación esté firmada. Si recibe varios mensajes del llavero, asegúrese de que la firma de la aplicación es válida.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre los grupos de acceso al llavero en el artículo de Apple sobre cómo [compartir el acceso los elementos del llavero entre una colección de aplicaciones](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc).
