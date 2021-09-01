---
title: Habilitación de las opciones de una aplicación web mediante Azure Active Directory B2C
description: Habilite el uso de las opciones de una aplicación web de varias maneras.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/12/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 126bdd850d29d716433b7854c71d02269b95ec2e
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067980"
---
# <a name="configure-authentication-options-in-a-web-application-using-azure-active-directory-b2c"></a>Configuración de las opciones de autenticación de una aplicación web mediante Azure Active Directory B2C 

En este artículo se describen métodos para personalizar y mejorar la experiencia de autenticación de Azure Active Directory B2C (Azure AD B2C) para una aplicación web. Antes de empezar, es importante familiarizarse con los siguientes artículos: [Configuración de la autenticación en una aplicación web de ejemplo](configure-authentication-sample-web-app.md) o [Habilitación de la autenticación en su propia aplicación web](enable-authentication-web-application.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Para usar un dominio personalizado y el identificador de inquilino en la dirección URL de autenticación, siga las instrucciones que se indican en [Habilitación de dominios personalizados](custom-domain.md). En la carpeta raíz del proyecto, abra el archivo `appsettings.json`. Este archivo contiene información sobre el proveedor de identidades de Azure AD B2C. 

- Actualice la entrada `Instance` con el dominio personalizado.
- Actualice la entrada `Domain` con el [identificador del inquilino](tenant-management.md#get-your-tenant-id). Para más información, consulte [Uso del identificador de inquilino](custom-domain.md#optional-use-tenant-id).

El siguiente JSON muestra la configuración de la aplicación antes del cambio: 

```JSon
"AzureAdB2C": {
  "Instance": "https://contoso.b2clogin.com",
  "Domain": "tenant-name.onmicrosoft.com",
  ...
}
```  

El siguiente JSON muestra la configuración de la aplicación después del cambio: 

```JSon
"AzureAdB2C": {
  "Instance": "https://login.contoso.com",
  "Domain": "00000000-0000-0000-0000-000000000000",
  ...
}
``` 

## <a name="support-advanced-scenarios"></a>Compatibilidad con escenarios avanzados

El método `AddMicrosoftIdentityWebAppAuthentication` de la API de la plataforma de identidad de Microsoft permite a los desarrolladores agregar código para escenarios de autenticación avanzada o suscribirse a eventos de OpenIDConnect. Por ejemplo, puede suscribirse a OnRedirectToIdentityProvider, que le permite personalizar la solicitud de autenticación que la aplicación envía a Azure AD B2C.

Para admitir escenarios avanzados, abra el archivo `Startup.cs` y, en la función `ConfigureServices`, reemplace `AddMicrosoftIdentityWebAppAuthentication` por el siguiente fragmento de código: 

```csharp
// Configuration to sign-in users with Azure AD B2C

//services.AddMicrosoftIdentityWebAppAuthentication(Configuration, "AzureAdB2C");

services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
{
    Configuration.Bind("AzureAdB2C", options);
    options.Events ??= new OpenIdConnectEvents();
    options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
});
```

El código anterior agrega el evento OnRedirectToIdentityProvider con una referencia al método *OnRedirectToIdentityProviderFunc*. Agregue el siguiente fragmento de código a la clase `Startup.cs`.

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Custom code here
    
    // Don't remove this line
    await Task.CompletedTask.ConfigureAwait(false);
}
```

Puede pasar parámetros entre el controlador y la función *OnRedirectToIdentityProvider* mediante parámetros de contexto. 


[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si usa una directiva personalizada, agregue la notificación de entrada necesaria como se describe en [Configuración del inicio de sesión directo](direct-signin.md#prepopulate-the-sign-in-name). 
1. Complete el procedimiento [Compatibilidad con escenarios avanzados](#support-advanced-scenarios).
1. Agregue la siguiente línea de código a la función *OnRedirectToIdentityProvider*:
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.LoginHint = "emily@contoso.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Compruebe el nombre de dominio del proveedor de identidades externo. Para más información, consulte [Redirección del inicio de sesión a un proveedor social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Complete el procedimiento [Compatibilidad con escenarios avanzados](#support-advanced-scenarios).
1. En la función *OnRedirectToIdentityProviderFunc*, agregue la siguiente línea de código a la función *OnRedirectToIdentityProvider*:
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.DomainHint = "facebook.com";
      
      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```


[!INCLUDE [active-directory-b2c-app-integration-ui-locales](../../includes/active-directory-b2c-app-integration-ui-locales.md)]

1. [Configure la personalización de idioma](language-customization.md).
1. Complete el procedimiento [Compatibilidad con escenarios avanzados](#support-advanced-scenarios).
1. Agregue la siguiente línea de código a la función *OnRedirectToIdentityProvider*:

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.UiLocales = "es";

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

[!INCLUDE [active-directory-b2c-app-integration-custom-parameters](../../includes/active-directory-b2c-app-integration-custom-parameters.md)]

1. Configure el elemento [ContentDefinitionParameters](customize-ui-with-html.md#configure-dynamic-custom-page-content-uri).
1. Complete el procedimiento [Compatibilidad con escenarios avanzados](#support-advanced-scenarios).
1. Agregue la siguiente línea de código a la función *OnRedirectToIdentityProvider*:
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      context.ProtocolMessage.Parameters.Add("campaignId", "123");

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```


[!INCLUDE [active-directory-b2c-app-integration-id-token-hint](../../includes/active-directory-b2c-app-integration-id-token-hint.md)]

1. Complete el procedimiento [Compatibilidad con escenarios avanzados](#support-advanced-scenarios).
1. En la directiva personalizada, defina un [perfil técnico de sugerencias de token de identificador](id-token-hint.md).
1. Agregue la siguiente línea de código a la función *OnRedirectToIdentityProvider*:
    
    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
      // The idTokenHint variable holds your ID token 
      context.ProtocolMessage.IdTokenHint = idTokenHint

      // More code
      await Task.CompletedTask.ConfigureAwait(false);
    }
    ```
    
## <a name="account-controller"></a>Controlador de la cuenta

Si quiere personalizar las acciones de **inicio de sesión**, **registro** o **cierre de sesión**, se recomienda crear su propio controlador. Tener su propio controlador le permite pasar parámetros entre el controlador y la biblioteca de autenticación. `AccountController` forma parte del paquete NuGet `Microsoft.Identity.Web.UI`, que controla las acciones de inicio y cierre de sesión. Puede encontrar su implementación en la [biblioteca web de identidad de Microsoft](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web.UI/Areas/MicrosoftIdentity/Controllers/AccountController.cs). 

### <a name="add-the-account-controller"></a>Incorporación del controlador de la cuenta

En el proyecto de Visual Studio, haga clic con el botón derecho en la carpeta **Controladores** y agregue un nuevo **controlador**. Seleccione **Controlador de MVC en blanco** y proporcione el nombre **MyAccountController.cs**.

El siguiente fragmento de código muestra un `MyAccountController` personalizado con la acción **SignIn**.

```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authentication;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;


namespace mywebapp.Controllers
{
    [AllowAnonymous]
    [Area("MicrosoftIdentity")]
    [Route("[area]/[controller]/[action]")]
    public class MyAccountController : Controller
    {

        [HttpGet("{scheme?}")]
        public IActionResult SignIn([FromRoute] string scheme)
        {
            scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
            var redirectUrl = Url.Content("~/");
            var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
            return Challenge(properties, scheme);
        }

    }
}
```

En la vista `_LoginPartial.cshtml`, cambie el vínculo de inicio de sesión al controlador.

```html
<form method="get" asp-area="MicrosoftIdentity" asp-controller="MyAccount" asp-action="SignIn">
```

### <a name="pass-the-azure-ad-b2c-policy-id"></a>Pasar el identificador de la directiva de Azure AD B2C

El siguiente fragmento de código muestra un `MyAccountController` personalizado con la acción **SignIn** y **SignUp**. La acción pasa un parámetro denominado `policy` a la biblioteca de autenticación. Esto le permite proporcionar el identificador de la directiva de Azure AD B2C correcto para la acción específica.

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    return Challenge(properties, scheme);
}

public IActionResult SignUp([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignUp";
    return Challenge(properties, scheme);
}
```

En la vista `_LoginPartial.cshtml`, cambie el valor `asp-controller` a `MyAccountController` para cualquier otro vínculo de autenticación, como registrarse o editar perfil.

### <a name="pass-custom-parameters"></a>Pasar parámetros personalizados

El siguiente fragmento de código muestra un `MyAccountController` personalizado con la acción **SignIn**. La acción pasa un parámetro denominado `campaign_id` a la biblioteca de autenticación.

```csharp
public IActionResult SignIn([FromRoute] string scheme)
{
    scheme ??= OpenIdConnectDefaults.AuthenticationScheme;
    var redirectUrl = Url.Content("~/");
    var properties = new AuthenticationProperties { RedirectUri = redirectUrl };
    properties.Items["policy"] = "B2C_1_SignIn";
    properties.Items["campaign_id"] = "1234";
    return Challenge(properties, scheme);
}
```

Complete el procedimiento [Compatibilidad con escenarios avanzados](#support-advanced-scenarios). A continuación, en el método `OnRedirectToIdentityProvider`, lea el parámetro personalizado:

```csharp
private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
{
    // Read the custom parameter
    var campaign_id = context.Properties.Items.FirstOrDefault(x => x.Key == "campaign_id").Value;

    // Add your custom code here
    if (campaign_id != null)
    {
        // Send parameter to authentication request
        context.ProtocolMessage.SetParameter("campaign_id", campaign_id);
    }
    
    await Task.CompletedTask.ConfigureAwait(false);
}
```

## <a name="secure-your-logout-redirect"></a>Protección de la redirección de cierre de sesión

Después del cierre de sesión, se redirige al usuario al URI especificado en el parámetro `post_logout_redirect_uri`, independientemente de las direcciones URL de respuesta que se hayan especificado para la aplicación. Sin embargo, si se pasa un valor de `id_token_hint` válido y la opción [Requerir token de identificador en solicitudes de cierre de sesión](session-behavior.md#secure-your-logout-redirect) está activada, Azure AD B2C comprueba que el valor de `post_logout_redirect_uri` coincida con uno de los URI de redirección configurados de la aplicación antes de realizar la redirección. Si no se configuró ninguna dirección URL de respuesta coincidente para la aplicación, se muestra un mensaje de error y no se redirige al usuario.

Para que la aplicación admita un redireccionamiento de cierre de sesión, primero siga los pasos que se indican en las secciones [Controlador de la cuenta](enable-authentication-web-application-options.md#add-the-account-controller) y [Compatibilidad con escenarios avanzados](#support-advanced-scenarios). A continuación, realice los pasos que se indican a continuación:

1. En el controlador `MyAccountController.cs`, agregue una acción **SignOut** mediante el siguiente fragmento de código:

    ```csharp
    [HttpGet("{scheme?}")]
    public async Task<IActionResult> SignOutAsync([FromRoute] string scheme)
    {
        scheme ??= OpenIdConnectDefaults.AuthenticationScheme;

        //obtain the id_token
        var idToken = await HttpContext.GetTokenAsync("id_token");
        //send the id_token value to the authentication middleware
        properties.Items["id_token_hint"] = idToken;            

        return SignOut(properties,CookieAuthenticationDefaults.AuthenticationScheme,scheme);
    }
    ```

1. En la clase **Startup.cs**, analice el valor `id_token_hint` y anexe el valor a la solicitud de autenticación. El fragmento de código siguiente muestra cómo pasar el valor `id_token_hint` a la solicitud de autenticación:

    ```csharp
    private async Task OnRedirectToIdentityProviderFunc(RedirectContext context)
    {
        var id_token_hint = context.Properties.Items.FirstOrDefault(x => x.Key == "id_token_hint").Value;
        if (id_token_hint != null)
        {
            // Send parameter to authentication request
            context.ProtocolMessage.SetParameter("id_token_hint", id_token_hint);
        }

        await Task.CompletedTask.ConfigureAwait(false);
    }
    ```

1. En la función `ConfigureServices`, agregue la opción `SaveTokens` para que los **controladores** tengan acceso al valor `id_token`: 

    ```csharp
    services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApp(options =>
        {
            Configuration.Bind("AzureAdB2C", options);
            options.Events ??= new OpenIdConnectEvents();        
            options.Events.OnRedirectToIdentityProvider += OnRedirectToIdentityProviderFunc;
            options.SaveTokens = true;
        });
    ```

1. En el archivo de configuración **appsettings.json**, agregue la ruta de acceso al identificador URI de redireccionamiento de cierre de sesión a la clave `SignedOutCallbackPath`.

    ```json
    "AzureAdB2C": {
      "Instance": "https://<your-tenant-name>.b2clogin.com",
      "ClientId": "<web-app-application-id>",
      "Domain": "<your-b2c-domain>",
      "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
      "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
    }
    ```

En el ejemplo anterior, el elemento **post_logout_redirect_uri** pasado a la solicitud de cierre de sesión tendrá el formato `https://your-app.com/signout/<your-sign-up-in-policy>`. Esta dirección URL debe agregarse a la dirección URL de respuesta del registro de aplicación.

## <a name="role-based-access-control"></a>Control de acceso basado en rol

Con la [autorización en ASP.NET Core](/aspnet/core/security/authorization/introduction), puede usar la [autorización basada en roles](/aspnet/core/security/authorization/roles), la [autorización basada en notificaciones](/aspnet/core/security/authorization/claims) o la [autorización basada en directivas](/aspnet/core/security/authorization/policies) para comprobar si el usuario está autorizado para acceder a un recurso protegido.

En el método *ConfigureServices*, agregue el método *AddAuthorization*, que agrega el modelo de autorización. En el ejemplo siguiente se crea una directiva llamada `EmployeeOnly`. La directiva comprueba que existe una notificación `EmployeeNumber`. El valor de la notificación debe ser uno de los siguientes identificadores: 1, 2, 3, 4 o 5.

```csharp
services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy =>
              policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
```

La autorización en ASP.NET Core se controla con [AuthorizeAttribute](/aspnet/core/security/authorization/simple) y sus distintos parámetros. En su forma más básica, la aplicación del atributo `[Authorize]` a un controlador, una acción o una Razor Page limita el acceso para los usuarios autenticados de ese componente.

Las directivas se aplican a los controladores mediante el atributo `[Authorize]` con el nombre de directiva. El código siguiente limita el acceso a la acción `Claims` para los usuarios autorizados por la directiva `EmployeeOnly`:

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult Claims()
{
    return View();
}
```

## <a name="next-steps"></a>Pasos siguientes

- Más información: [Introducción a la autorización en ASP.NET Core](/aspnet/core/security/authorization/introduction)
