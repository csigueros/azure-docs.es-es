---
title: Configuración de proveedores de identidades (MSAL para iOS y macOS) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a usar diferentes entidades, como B2C, nubes independientes y usuarios invitados, con MSAL para iOS y macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: af729767467d2dc950fb7a559fec4d0a81d07cab
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029189"
---
# <a name="configure-msal-for-ios-and-macos-to-use-different-identity-providers"></a>Configuración de MSAL para iOS y macOS para el uso de diferentes proveedores de identidades

En este artículo se muestra cómo configurar la aplicación de biblioteca de autenticación de Microsoft para iOS y macOS (MSAL) para diferentes entidades, como Azure Active Directory (Azure AD), de negocio a consumidor (B2C), nubes independientes y usuarios invitados.  En este artículo, por lo general, puede pensar en una autoridad como proveedor de identidades.

## <a name="default-authority-configuration"></a>Configuración de autoridad predeterminada

`MSALPublicClientApplication` está configurado con una dirección URL de autoridad predeterminada de `https://login.microsoftonline.com/common`, que es adecuada para la mayoría de los escenarios de Azure Active Directory (AAD). A menos que esté implementando escenarios avanzados, como nubes nacionales, o trabajando con B2C, no tendrá que cambiarlo.

> [!NOTE]
> No se admite la autenticación moderna con Servicios de federación de Active Directory (AD FS) como proveedor de identidades (ADFS) (consulte [ADFS para desarrolladores](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios) para más información). ADFS se admite mediante la federación.

## <a name="change-the-default-authority"></a>Cambio de la autoridad predeterminada

En algunos escenarios, como de negocio a consumidor (B2C), es posible que tenga que cambiar la autoridad predeterminada.

### <a name="b2c"></a>B2C

Para trabajar con B2C, la [biblioteca de autenticación de Microsoft (MSAL)](reference-v2-libraries.md) requiere una configuración de autoridad diferente. MSAL reconoce el formato de la dirección URL de una entidad como B2C. El formato de autoridad B2C reconocido es `https://<host>/tfp/<tenant>/<policy>`, por ejemplo, `https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy`. Sin embargo, también puede usar cualquier otra dirección URL de entidad B2C compatible al declarar la entidad como B2C explícitamente.

Para admitir un formato de dirección URL arbitrario para B2C, `MSALB2CAuthority` se puede establecer con una dirección URL arbitraria, como la siguiente:

Objective-C
```objc
NSURL *authorityURL = [NSURL URLWithString:@"arbitrary URL"];
MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
```
Swift
```swift
guard let authorityURL = URL(string: "arbitrary URL") else {
    // Handle error
    return
}
let b2cAuthority = try MSALB2CAuthority(url: authorityURL)
```

Todas las entidades B2C que no usen el formato de entidad B2C predeterminado deben declararse como entidades conocidas.

Agregue cada entidad B2C diferente a la lista de entidades de certificación conocidas aunque solo difieran en la directiva.

Objective-C
```objc
MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:b2cAuthority];
b2cApplicationConfig.knownAuthorities = @[b2cAuthority];
```
Swift
```swift
let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)
b2cApplicationConfig.knownAuthorities = [b2cAuthority]
```

Cuando la aplicación solicite una nueva directiva, la dirección URL de la entidad debe cambiarse, ya que la dirección URL de la entidad es diferente para cada directiva. 

Para configurar una aplicación B2C, establezca `@property MSALAuthority *authority` con una instancia de `MSALB2CAuthority` en `MSALPublicClientApplicationConfig` antes de crear `MSALPublicClientApplication`, de la siguiente manera:

Objective-C
```ObjC
    // Create B2C authority URL
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy"];
    
    MSALB2CAuthority *b2cAuthority = [[MSALB2CAuthority alloc] initWithURL:authorityURL
                                                                     error:&b2cAuthorityError];
    if (!b2cAuthority)
    {
        // Handle error
        return;
    }
    
    // Create MSALPublicClientApplication configuration
    MSALPublicClientApplicationConfig *b2cApplicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                                   initWithClientId:@"your-client-id"
                                                                   redirectUri:@"your-redirect-uri"
                                                                   authority:b2cAuthority];

    // Initialize MSALPublicClientApplication
    MSALPublicClientApplication *b2cApplication =
    [[MSALPublicClientApplication alloc] initWithConfiguration:b2cApplicationConfig error:&error];
    
    if (!b2cApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    // Create B2C authority URL
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/B2C_1_SignInPolicy") else {
        // Handle error
        return
    }
    let b2cAuthority = try MSALB2CAuthority(url: authorityURL)

    // Create MSALPublicClientApplication configuration
    let b2cApplicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: b2cAuthority)

    // Initialize MSALPublicClientApplication
    let b2cApplication = try MSALPublicClientApplication(configuration: b2cApplicationConfig)
} catch {
    // Handle error
}
```

### <a name="sovereign-clouds"></a>Nubes soberanas

Si la aplicación se ejecuta en una nube soberana, puede que tenga que cambiar la dirección URL de la entidad en `MSALPublicClientApplication`. En el ejemplo siguiente se establece la dirección URL de la entidad para que funcione con la nube de AAD alemana:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.de/common"];
    MSALAuthority *sovereignAuthority = [MSALAuthority authorityWithURL:authorityURL error:&authorityError];
    
    if (!sovereignAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:sovereignAuthority];
    
    
    MSALPublicClientApplication *sovereignApplication = [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    
    if (!sovereignApplication)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.de/common") else {
        //Handle error
        return
    }
    let sovereignAuthority = try MSALAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: sovereignAuthority)
            
    let sovereignApplication = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

Es posible que deba pasar ámbitos diferentes a cada nube soberana. Los ámbitos que se envían dependen del recurso que se esté usando. Por ejemplo, puede usar `"https://graph.microsoft.com/user.read"` en la nube global y `"https://graph.microsoft.de/user.read"` en la nube alemana.

### <a name="signing-a-user-into-a-specific-tenant"></a>Firma de un usuario en un inquilino específico

Cuando la dirección URL de la entidad se establece en `"login.microsoftonline.com/common"`, el usuario inicia sesión en su inquilino de inicio. Sin embargo, es posible que algunas aplicaciones necesiten iniciar la sesión del usuario en un inquilino diferente y que algunas aplicaciones solo funcionen con un solo inquilino.

Para iniciar la sesión del usuario en un inquilino específico, configure `MSALPublicClientApplication` con una entidad específica. Por ejemplo:

`https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4`

A continuación se muestra cómo iniciar la sesión de un usuario en un inquilino específico:

Objective-C
```objc
    NSURL *authorityURL = [NSURL URLWithString:@"https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4"];
    MSALAADAuthority *tenantedAuthority = [[MSALAADAuthority alloc] initWithURL:authorityURL error:&authorityError];
    
    if (!tenantedAuthority)
    {
        // Handle error
        return;
    }
    
    MSALPublicClientApplicationConfig *applicationConfig = [[MSALPublicClientApplicationConfig alloc]
                                                               initWithClientId:@"your-client-id"
                                                               redirectUri:@"your-redirect-uri"
                                                               authority:tenantedAuthority];
    
    MSALPublicClientApplication *application =
    [[MSALPublicClientApplication alloc] initWithConfiguration:applicationConfig error:&error];
    
    if (!application)
    {
        // Handle error
        return;
    }
```
Swift
```swift
do{
    guard let authorityURL = URL(string: "https://login.microsoftonline.com/469fdeb4-d4fd-4fde-991e-308a78e4bea4") else {
        //Handle error
        return
    }    
    let tenantedAuthority = try MSALAADAuthority(url: authorityURL)
            
    let applicationConfig = MSALPublicClientApplicationConfig(clientId: "your-client-id", redirectUri: "your-redirect-uri", authority: tenantedAuthority)
            
    let application = try MSALPublicClientApplication(configuration: applicationConfig)
} catch {
    // Handle error
}
```

## <a name="supported-authorities"></a>Entidades admitidas

### <a name="msalauthority"></a>MSALAuthority

La clase `MSALAuthority` es la clase abstracta básica para las clases de entidad de MSAL. No intente crear una instancia suya mediante `alloc` o `new`. En su lugar, cree una de sus subclases directamente (`MSALAADAuthority`, `MSALB2CAuthority`) o use el método de fábrica `authorityWithURL:error:` para crear subclases mediante una dirección URL de entidad.

Use la propiedad `url` para obtener una dirección URL de entidad normalizada. Los parámetros adicionales y los componentes de la ruta de acceso o los fragmentos que no forman parte de la entidad no estarán en la dirección URL de la entidad normalizada devuelta.

A continuación se muestran las subclases de `MSALAuthority` de las que puede crear instancias en función de la entidad que desee usar.

### <a name="msalaadauthority"></a>MSALAADAuthority

`MSALAADAuthority` representa una entidad de AAD. La dirección URL de la entidad debe tener el formato siguiente, donde `<port>` es opcional: `https://<host>:<port>/<tenant>`.

### <a name="msalb2cauthority"></a>MSALB2CAuthority

`MSALB2CAuthority` representa una entidad B2C. De forma predeterminada, la dirección URL de la entidad B2C debe tener el formato siguiente, donde `<port>` es opcional: `https://<host>:<port>/tfp/<tenant>/<policy>`. Sin embargo, MSAL también admite otros formatos de entidad B2C arbitrarios.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [flujos de autenticación y escenarios de aplicaciones](authentication-flows-app-scenarios.md).