---
title: Migración de aplicaciones y API a b2clogin.com
titleSuffix: Azure AD B2C
description: Aprenda a usar b2clogin.com en las direcciones URL de redireccionamiento para Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 12c7b70b2c1128cac943c8d1d61ad84113eb87f5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128570133"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Establecimiento de direcciones URL de redireccionamiento en b2clogin.com para Azure Active Directory B2C

Cuando se configura un proveedor de identidades para registrarse e iniciar sesión en la aplicación Azure Active Directory B2C (Azure AD B2C), debe especificar una dirección URL de redireccionamiento. Ya no debe hacer referencia a *login.microsoftonline.com* en las aplicaciones y API para autenticar a los usuarios con Azure AD B2C. En su lugar, use *b2clogin.com* para todas las aplicaciones nuevas y migre las aplicaciones existentes de *login.microsoftonline.com* a *b2clogin.com*.

## <a name="what-endpoints-does-this-apply-to"></a>¿A qué puntos de conexión se aplica esto?
La transición a b2clogin.com solo se aplica a los puntos de conexión de autenticación que usan directivas de Azure AD B2C (flujos de usuario o directivas personalizadas) para autenticar a los usuarios. Estos puntos de conexión tienen un parámetro `<policy-name>` que especifica la directiva que Azure AD B2C debe usar. [Obtenga más información sobre las directivas de Azure AD B2C](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 

Estos puntos de conexión pueden tener este aspecto:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Como alternativa, `<policy-name>` se puede pasar como un parámetro de consulta:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> Los puntos de conexión que usan el parámetro "policy" deben actualizarse, así como las [direcciones URL de redireccionamiento del proveedor de identidades](#change-identity-provider-redirect-urls).

Algunos clientes de Azure AD B2C usan las funcionalidades compartidas de los inquilinos empresariales de Azure AD, como el flujo de concesión de credenciales de cliente de OAuth 2.0. Se puede acceder a estas características mediante los puntos de conexión de login.microsoftonline.com de Azure AD, *que no contienen un parámetro de directiva*. __Estos puntos de conexión no se ven afectados__.

## <a name="benefits-of-b2clogincom"></a>Ventajas de b2clogin.com

Cuando use *b2clogin.com* como la dirección URL de redireccionamiento:

* Se reduce el espacio que los servicios de Microsoft consumen en el encabezado de las cookies.
* Las direcciones URL ya no incluyen una referencia a Microsoft.
* El [código de cliente de JavaScript](javascript-and-page-layout.md) se admite en páginas personalizadas. Debido a las restricciones de seguridad, el código JavaScript y los elementos de formulario HTML se quitan de las páginas personalizadas si se usa *login.microsoftonline.com*.

## <a name="overview-of-required-changes"></a>Información general de los cambios necesarios

Puede que tenga que realizar varias modificaciones para migrar las aplicaciones a *b2clogin.com*:

* Cambie las direcciones URL de redireccionamiento en las aplicaciones del proveedor de identidades para hacer referencia a *b2clogin.com*.
* Actualice la aplicación Azure AD B2C para que utilice *b2clogin.com* como referencias de flujos de usuario y puntos de conexión de token. Esto puede incluir la actualización del uso de una biblioteca de autenticación, como la biblioteca de autenticación de Microsoft (MSAL).
* Actualice cualquier **origen permitido** que haya definido en la configuración de CORS para la [personalización de la interfaz de usuario](customize-ui-with-html.md).

Un punto de conexión anterior puede tener el siguiente aspecto:
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

Un punto de conexión actualizado correspondiente tendría el siguiente aspecto:
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>Cambie las direcciones URL de redireccionamiento del proveedor de identidades

En el sitio web de cada proveedor de identidades en el que ha creado una aplicación, cambie todas las direcciones URL de confianza para se redirijan a `your-tenant-name.b2clogin.com` en lugar de a *login.microsoftonline.com*.

Hay dos formatos que puede usar para las direcciones URL de redireccionamiento de b2clogin.com. La primera proporciona la ventaja de no tener "Microsoft" en ninguna parte de la dirección URL mediante el identificador de inquilino (un GUID) en lugar del nombre de dominio del inquilino:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

La segunda opción usa el nombre de dominio del inquilino con el formato `your-tenant-name.onmicrosoft.com`. Por ejemplo:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

En ambos formatos:

* Reemplace `{your-tenant-name}` por el nombre del inquilino de Azure AD B2C.
* Quite `/te` si existe en la dirección URL.

## <a name="update-your-applications-and-apis"></a>Actualización de las aplicaciones y de las API

El código de las aplicaciones habilitadas para Azure AD B2C y para las API puede hacer referencia a `login.microsoftonline.com` en varios lugares. Por ejemplo, el código podría tener referencias a los flujos de usuario y los puntos de conexión de token. Actualice lo siguiente para hacer referencia a `your-tenant-name.b2clogin.com`:

* Punto de conexión de autorización
* Punto de conexión de token
* Emisor de tokens

Por ejemplo, el punto de conexión de la autoridad de la directiva de registro o de inicio de sesión de Contoso ahora sería:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Para obtener información sobre la migración de aplicaciones web basadas en OWIN a b2clogin.com, consulte [Migración de una API web basada en OWIN a b2clogin.com](multiple-token-endpoints.md).

Para migrar las API de Azure API Management protegidas por Azure AD B2C, consulte la sección [Migración a b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) en [Protección de una API de Azure API Management con Azure AD B2C](secure-api-management.md).

## <a name="microsoft-authentication-library-msal"></a>Biblioteca de autenticación de Microsoft (MSAL)

### <a name="msalnet-validateauthority-property"></a>Propiedad ValidateAuthority de MSAL.NET

Si usa [MSAL.NET][msal-dotnet] v2 o versiones anteriores, establezca la propiedad **ValidateAuthority** en `false` en la creación de instancias del cliente para permitir redireccionamientos a *b2clogin.com*. No es necesario establecer este valor en `false` para MSAL.NET v3 y superior.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>Propiedad ValidateAuthority de MSAL para JavaScript

Si usa [MSAL para JavaScript][msal-js] v1.2.2 o versiones anteriores, establezca la propiedad **ValidateAuthority** en `false`.

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

Si establece `validateAuthority: true` de MSAL.js 1.3.0+ (el valor predeterminado), debe especificar también un emisor de tokens válido con `knownAuthorities`:

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre la migración de aplicaciones web basadas en OWIN a b2clogin.com, consulte [Migración de una API web basada en OWIN a b2clogin.com](multiple-token-endpoints.md).

Para migrar las API de Azure API Management protegidas por Azure AD B2C, consulte la sección [Migración a b2clogin.com](secure-api-management.md#migrate-to-b2clogincom) en [Protección de una API de Azure API Management con Azure AD B2C](secure-api-management.md).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
