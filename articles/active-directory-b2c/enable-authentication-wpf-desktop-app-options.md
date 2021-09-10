---
title: Habilitación de las opciones de una aplicación de escritorio de WPF mediante Azure Active Directory B2C
description: Habilite el uso de las opciones de una aplicación de escritorio de WPF de varias maneras.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: bbb0b9d29b2551a6c733d0df8b62505efe68514f
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220263"
---
# <a name="enable-authentication-options-in-a-wpf-desktop-app-by-using-azure-ad-b2c"></a>Habilitación de las opciones de autenticación en una aplicación de escritorio para WPF mediante Azure AD B2C 

En este artículo se describen formas para personalizar y mejorar la experiencia de autenticación de Azure Active Directory B2C (Azure AD B2C) para la aplicación de escritorio para Windows Presentation Foundation (WPF). 

Antes de empezar, familiarícese con el artículo [Configuración de la autenticación en una aplicación de escritorio para WPF de ejemplo mediante Azure AD B2C](configure-authentication-sample-wpf-desktop-app.md).


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si usa una directiva personalizada, agregue la notificación de entrada necesaria, como se describe en [Configuración del inicio de sesión directo](direct-signin.md#prepopulate-the-sign-in-name). 
1. Busque el objeto de configuración de la Biblioteca de autenticación de Microsoft (MSAL) y agregue el método `withLoginHint()` con la sugerencia de inicio de sesión.

```csharp
authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithLoginHint("bob@contoso.com")
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Compruebe el nombre de dominio del proveedor de identidades externo. Para más información, consulte [Redirección del inicio de sesión a un proveedor social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Cree un objeto `Dictionary` o use uno existente para almacenar parámetros de consulta adicionales.
1. Agregue el parámetro `domain_hint` con el nombre de dominio correspondiente al diccionario (por ejemplo, `facebook.com`).
1. Pase el objeto de parámetros de consulta adicionales al método `WithExtraQueryParameters` del objeto de configuración de MSAL.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("domain_hint", "facebook.com");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configure la personalización de idioma](language-customization.md).
1. Cree un objeto `Dictionary` o use uno existente para almacenar parámetros de consulta adicionales.
1. Agregue el parámetro `ui_locales` con el código de lenguaje correspondiente al diccionario (por ejemplo, `en-us`).
1. Pase el objeto de parámetros de consulta adicionales al método `WithExtraQueryParameters` del objeto de configuración de MSAL.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("ui_locales", "en-us");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configure el elemento [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Cree un objeto `Dictionary` o use uno existente para almacenar parámetros de consulta adicionales.
1. Agregue el parámetro de cadena de consulta personalizado, como `campaignId`. Establezca el valor del parámetro (por ejemplo, `germany-promotion`).
1. Pase el objeto de parámetros de consulta adicionales al método `WithExtraQueryParameters` del objeto de configuración de MSAL.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("campaignId", "germany-promotion");

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```

[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. En la directiva personalizada, defina un [perfil técnico de sugerencias de token de identificador](id-token-hint.md).
1. En el código, genere o adquiera un token de identificador y, después, establezca el token en una variable (por ejemplo, `idToken`). 
1. Cree un objeto `Dictionary` o use uno existente para almacenar parámetros de consulta adicionales.
1. Agregue el parámetro `id_token_hint` con la variable correspondiente que almacena el token de identificador.
1. Pase el objeto de parámetros de consulta adicionales al atributo `extraQueryParameters` del objeto de configuración de MSAL.

```csharp
Dictionary<string, string> extraQueryParameters = new Dictionary<string, string>();
extraQueryParameters.Add("id_token_hint", idToken);

authResult = await app.AcquireTokenInteractive(App.ApiScopes)
    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
    .WithExtraQueryParameters(extraQueryParameters)
    .ExecuteAsync();
```


[!INCLUDE [active-directory-b2c-app-integration-logging](../../includes/active-directory-b2c-app-integration-logging.md)]


En el siguiente fragmento de código se muestra cómo configurar el registro de MSAL:

```csharp
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
    .WithB2CAuthority(AuthoritySignUpSignIn)
    .WithRedirectUri(RedirectUri)
    .WithLogging(Log, LogLevel.Info, false) // don't log P(ersonally) I(dentifiable) I(nformation) details on a regular basis
    .Build();
```

## <a name="configure-the-redirect-uri"></a>Configuración del URI de redirección

Durante el proceso de [registro de aplicaciones de escritorio](configure-authentication-sample-wpf-desktop-app.md#step-23-register-the-desktop-app), al elegir un URI de redirección, tenga en cuenta las siguientes consideraciones importantes:

* **Desarrollo**: para uso en el desarrollo de aplicaciones de escritorio, puede establecer el URI de redirección en `http://localhost`, y Azure AD B2C respetará todos los puertos de la solicitud. Si el URI registrado contiene un puerto, Azure AD B2C usará solo ese. Por ejemplo, si el URI de redirección registrado es `http://localhost`, el URI de redirección de la solicitud puede ser `http://localhost:<randomport>`. Si el URI de redirección registrado es `http://localhost:8080`, el URI de redirección de la solicitud debe ser `http://localhost:8080`.
* **Único**: el esquema del identificador URI de redirección debe ser único para todas las aplicaciones. En el ejemplo `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`, `com.onmicrosoft.contosob2c.exampleapp` es el esquema. Se debe seguir este patrón. Si dos aplicaciones comparten el mismo esquema, a los usuarios se les permite elegir una. Si los usuarios eligen incorrectamente, se produce un error en el inicio de sesión.
* **Completo**: el URI de redirección debe tener un esquema y una ruta de acceso. La ruta debe contener al menos un carácter de barra diagonal después del dominio. Por ejemplo, `//oauth/` funciona y `//oauth` no funciona. No incluya caracteres especiales en el URI. Por ejemplo, no se permite el carácter de subrayado (_).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información, vea [Opciones de configuración de MSAL para .NET, UWP, NetCore y Xamarin](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki).
