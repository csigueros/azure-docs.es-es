---
title: Adquisición de un token para llamar a una API web mediante la autenticación integrada de Windows (aplicación de escritorio) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a crear una aplicación de escritorio que llame a las API web con el fin de adquirir un token para la aplicación con la autenticación integrada de Windows.
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 182bed366dd59b99232608042bbe96aefaa6a8fa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837921"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-using-integrated-windows-authentication"></a>Aplicación de escritorio que llama a las API web: adquisición de un token mediante la autenticación integrada de Windows

Para iniciar la sesión de un usuario de dominio en un equipo unido a un dominio o a Azure AD, use la autenticación integrada de Windows (IWA).

## <a name="constraints"></a>Restricciones

- La autenticación integrada de Windows solo es útil para usuarios *federados*, es decir, los usuarios creados en Active Directory y respaldados por Azure AD. Los usuarios creados directamente en Azure AD sin el respaldo de Active Directory, conocidos como usuarios *administrados*, no pueden usar este flujo de autenticación. Esta limitación no afecta al flujo de nombre de usuario y contraseña.
- IWA no omite [la autenticación multifactor (MFA)](../authentication/concept-mfa-howitworks.md). Si se ha configurado MFA, IWA podría producir un error si es necesario un desafío de MFA, porque MFA necesita interacción del usuario.

    IWA no es interactiva, pero MFA requiere interactividad del usuario. El usuario no es quien controla si el proveedor de identidades solicita que se realice MFA, sino el administrador de inquilinos. Por lo que se ha observado, MFA se requiere al iniciar sesión desde otro país o región, cuando no se está conectado a través de VPN a una red corporativa y, a veces, incluso cuando se está. No espere un conjunto determinista de reglas. Azure AD usa inteligencia artificial para aprender continuamente si se requiere MFA. Si se produce un error de IWA, recurra a un mensaje de usuario como la autenticación interactiva o el flujo de código de dispositivo.

- La autoridad pasada en `PublicClientApplicationBuilder` debe ser:
  - Con inquilino con formato `https://login.microsoftonline.com/{tenant}/`, donde `tenant` es el GUID que representa el identificador de inquilino o un dominio asociado al inquilino.
  - Para cualquier cuenta profesional y educativa: `https://login.microsoftonline.com/organizations/`.
  - No se admiten cuentas personales de Microsoft. No se pueden usar inquilinos /common ni /consumers.

- Debido a que la autenticación integrada de Windows es un flujo silencioso:
  - El usuario de la aplicación debe haber consentido anteriormente para usar la aplicación.
  - O bien, el administrador de inquilinos debe haber consentido anteriormente que todos los usuarios del inquilino usen la aplicación.
  - En otras palabras:
    - Usted como desarrollador ha hecho clic en el botón **Conceder** de Azure Portal.
    - O bien, un administrador de inquilinos ha hecho clic en el botón **Conceder o revocar consentimiento del administrador para {dominio del inquilino}** de la pestaña **Permisos de API** del registro de la aplicación. Para más información, consulte [Adición de permisos para acceder a la API web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).
    - O bien, ha proporcionado una manera para que los usuarios den su consentimiento a la aplicación. Para más información, consulte [Solicitud de consentimiento de usuario individual](./v2-permissions-and-consent.md#requesting-individual-user-consent).
    - O bien, ha proporcionado una forma para que el administrador de inquilinos otorgue su consentimiento a la aplicación. Para más información, consulte [Consentimiento del administrador](./v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

- Este flujo está habilitado para aplicaciones de escritorio de .NET, .NET Core y UWP.

Para más información sobre el consentimiento, consulte [Permisos y consentimiento de la Plataforma de identidad de Microsoft](./v2-permissions-and-consent.md).

## <a name="learn-how-to-use-it"></a>Aprenda a usarlo

# <a name="net"></a>[.NET](#tab/dotnet)

En MSAL.NET, use:

```csharp
AcquireTokenByIntegratedWindowsAuth(IEnumerable<string> scopes)
```

Normalmente solo necesita un parámetro (`scopes`). Según la forma en que el administrador de Windows configure las directivas, es posible que no se permita que las aplicaciones de la máquina Windows busquen al usuario que ha iniciado sesión. En ese caso, use un segundo método, `.WithUsername()`, y pase el nombre de usuario del usuario que ha iniciado sesión con un formato UPN, por ejemplo, `joe@contoso.com`.

El ejemplo siguiente presenta el caso más reciente, con explicaciones de la clase de excepciones que se pueden obtener y sus mitigaciones.

```csharp
static async Task GetATokenForGraph()
{
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app = PublicClientApplicationBuilder
      .Create(clientId)
      .WithAuthority(authority)
      .Build();

 var accounts = await app.GetAccountsAsync();

 AuthenticationResult result = null;
 if (accounts.Any())
 {
  result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
      .ExecuteAsync();
 }
 else
 {
  try
  {
   result = await app.AcquireTokenByIntegratedWindowsAuth(scopes)
      .ExecuteAsync(CancellationToken.None);
  }
  catch (MsalUiRequiredException ex)
  {
   // MsalUiRequiredException: AADSTS65001: The user or administrator has not consented to use the application
   // with ID '{appId}' named '{appName}'.Send an interactive authorization request for this user and resource.

   // you need to get user consent first. This can be done, if you are not using .NET Core (which does not have any Web UI)
   // by doing (once only) an AcquireToken interactive.

   // If you are using .NET core or don't want to do an AcquireTokenInteractive, you might want to suggest the user to navigate
   // to a URL to consent: https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id={clientId}&response_type=code&scope=user.read

   // AADSTS50079: The user is required to use multi-factor authentication.
   // There is no mitigation - if MFA is configured for your tenant and AAD decides to enforce it,
   // you need to fallback to an interactive flows such as AcquireTokenInteractive or AcquireTokenByDeviceCode
   }
   catch (MsalServiceException ex)
   {
    // Kind of errors you could have (in ex.Message)

    // MsalServiceException: AADSTS90010: The grant type is not supported over the /common or /consumers endpoints. Please use the /organizations or tenant-specific endpoint.
    // you used common.
    // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted or otherwise organizations

    // MsalServiceException: AADSTS70002: The request body must contain the following parameter: 'client_secret or client_assertion'.
    // Explanation: this can happen if your application was not registered as a public client application in Azure AD
    // Mitigation: in the Azure portal, edit the manifest for your application and set the `allowPublicClient` to `true`
   }
   catch (MsalClientException ex)
   {
      // Error Code: unknown_user Message: Could not identify logged in user
      // Explanation: the library was unable to query the current Windows logged-in user or this user is not AD or AAD
      // joined (work-place joined users are not supported).

      // Mitigation 1: on UWP, check that the application has the following capabilities: Enterprise Authentication,
      // Private Networks (Client and Server), User Account Information

      // Mitigation 2: Implement your own logic to fetch the username (e.g. john@contoso.com) and use the
      // AcquireTokenByIntegratedWindowsAuth form that takes in the username

      // Error Code: integrated_windows_auth_not_supported_managed_user
      // Explanation: This method relies on an a protocol exposed by Active Directory (AD). If a user was created in Azure
      // Active Directory without AD backing ("managed" user), this method will fail. Users created in AD and backed by
      // AAD ("federated" users) can benefit from this non-interactive method of authentication.
      // Mitigation: Use interactive authentication
   }
 }

 Console.WriteLine(result.Account.Username);
}
```

Para obtener la lista de posibles modificadores en AcquireTokenByIntegratedWindowsAuthentication, consulte [AcquireTokenByIntegratedWindowsAuthParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokenbyintegratedwindowsauthparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

Este extracto procede de los [ejemplos de desarrollo de Java de MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/).

```java
private static IAuthenticationResult acquireTokenIwa() throws Exception {

    // Load token cache from file and initialize token cache aspect. The token cache will have
    // dummy data, so the acquireTokenSilently call will fail.
    TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

    PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
            .authority(AUTHORITY)
            .setTokenCacheAccessAspect(tokenCacheAspect)
            .build();

    Set<IAccount> accountsInCache = pca.getAccounts().join();
    // Take first account in the cache. In a production application, you would filter
    // accountsInCache to get the right account for the user authenticating.
    IAccount account = accountsInCache.iterator().next();

    IAuthenticationResult result;
    try {
        SilentParameters silentParameters =
                SilentParameters
                        .builder(SCOPE, account)
                        .build();

        // try to acquire token silently. This call will fail since the token cache
        // does not have any data for the user you are trying to acquire a token for
        result = pca.acquireTokenSilently(silentParameters).join();
    } catch (Exception ex) {
        if (ex.getCause() instanceof MsalException) {

            IntegratedWindowsAuthenticationParameters parameters =
                    IntegratedWindowsAuthenticationParameters
                            .builder(SCOPE, USER_NAME)
                            .build();

            // Try to acquire a IWA. You will need to generate a Kerberos ticket.
            // If successful, you should see the token and account information printed out to
            // console
            result = pca.acquireToken(parameters).join();
        } else {
            // Handle other exceptions accordingly
            throw ex;
        }
    }
    return result;
}
```

# <a name="macos"></a>[macOS](#tab/macOS)

Este flujo no se aplica a macOS.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Este flujo aún no se admite en el nodo de MSAL.

# <a name="python"></a>[Python](#tab/python)

Este flujo aún no se admite en Python de MSAL.

---
## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo de este escenario, [Llamada a una API web desde la aplicación de escritorio](scenario-desktop-call-api.md).
