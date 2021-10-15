---
title: Migración de aplicaciones cliente públicas a MSAL.NET
titleSuffix: Microsoft identity platform
description: Aprenda a migrar una aplicación cliente pública de la Biblioteca de autenticación de Azure Active Directory para .NET a la Biblioteca de autenticación de Microsoft para .NET.
services: active-directory
author: sahmalik
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2021
ms.author: sahmalik
ms.reviewer: saeeda, shermanouko, jmprieur
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 1ccd2acaaec8c49de761511ebd3d4a5ad86f4095
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233661"
---
# <a name="migrate-public-client-applications-from-adalnet-to-msalnet"></a>Migración de aplicaciones cliente públicas de ADAL.NET a MSAL.NET

En este artículo se describe cómo migrar una aplicación cliente pública de la Biblioteca de autenticación de Azure Active Directory para .NET (ADAL.NET) a la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET). Las aplicaciones cliente públicas son aplicaciones de escritorio (incluidas las aplicaciones Win32, WPF y UWP) y aplicaciones móviles que llaman a otro servicio en nombre del usuario. Para más información sobre las aplicaciones cliente públicas, consulte [Flujos de autenticación y escenarios de aplicaciones](authentication-flows-app-scenarios.md).

## <a name="migration-steps"></a>Pasos de migración

1. Busque el código mediante ADAL.NET en la aplicación.

   El código que usa ADAL en una aplicación cliente pública crea instancias de `AuthenticationContext` y llama a una invalidación de `AcquireTokenAsync` con los parámetros siguientes:

   - Cadena de `resourceId`. Esta variable es el URI del identificador de la aplicación de la API web a la que desea llamar.
   - `clientId`, que es el identificador de la aplicación, también conocido como Id. de aplicación.

2. Tras haber identificado que tiene aplicaciones que usan ADAL.NET, instale el paquete NuGet de MSAL.NET [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) y actualice sus referencias de la biblioteca de proyectos. Para obtener más información, consulte cómo [instalar un paquete NuGet](https://www.bing.com/search?q=install+nuget+package).

3. Actualice el código según el escenario de la aplicación cliente pública. Algunos pasos son comunes y se aplican en todos los escenarios de cliente público. Otros pasos son únicos para cada escenario. 

   Los escenarios de cliente público son:

   - [Administrador de autenticación web](scenario-desktop-acquire-token-wam.md), la autenticación basada en agente preferida en Windows.
   - [Autenticación interactiva](scenario-desktop-acquire-token-interactive.md), donde se muestra al usuario una interfaz basada en web para completar el proceso de inicio de sesión.
   - [Autenticación integrada de Windows](scenario-desktop-acquire-token-integrated-windows-authentication.md), donde un usuario firma con la misma identidad que usó para iniciar sesión en un dominio de Windows (para máquinas unidas a un dominio o unidas a Azure AD).
   - [Nombre de usuario y contraseña](scenario-desktop-acquire-token-username-password.md), donde se produce el inicio de sesión al proporcionar unas credenciales de nombre de usuario y contraseña.
   - [Flujo de código de dispositivo](scenario-desktop-acquire-token-device-code-flow.md), donde un dispositivo con experiencia de usuario limitada muestra un código de dispositivo para completar el flujo de autenticación en un dispositivo alternativo.


## <a name="interactive"></a>[Interactivo](#tab/interactive)

Los escenarios interactivos son donde la aplicación cliente pública muestra una interfaz de usuario de inicio de sesión hospedada en un explorador y el usuario debe iniciar sesión de forma interactiva.

#### <a name="find-out-if-your-code-uses-interactive-scenarios"></a>Averiguar si el código usa escenarios interactivos

El código ADAL de la aplicación en una aplicación cliente pública que usa la autenticación interactiva crea instancias de `AuthenticationContext` e incluye una llamada `AcquireTokenAsync`, con los parámetros siguientes.
 - `clientId`, que es un GUID que representa el registro de la aplicación.
 - `resourceUrl`, que indica el recurso para el que pide un token.
 - Uri, que es la dirección URL de respuesta.
 - Un objeto `PlatformParameters`. 

 #### <a name="update-the-code-for-interactive-scenarios"></a>Actualizar el código en escenarios interactivos

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

En este caso, reemplazamos la llamada a `AuthenticationContext.AcquireTokenAsync` por una llamada a `IPublicClientApplication.AcquireTokenInteractive`.

Esta es una comparación del código de ADAL.NET y MSAL.NET en escenarios interactivos:

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await ac.AcquireTokenAsync("<clientId>",
                                    "https://resourceUrl",
                                    new Uri("https://ClientReplyUrl"),
                                    new PlatformParameters(PromptBehavior.Auto));
```
:::column-end:::   
:::column span="":::
```csharp
// 1. Configuration - read below about redirect URI
var pca = PublicClientApplicationBuilder.Create("client_id")
              .WithBroker()
              .Build();

// Add a token cache, see https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-net-token-cache-serialization?tabs=desktop

// 2. GetAccounts
var accounts = await pca.GetAccountsAsync();
var accountToLogin = // choose an account, or null, or use PublicClientApplication.OperatingSystemAccount for the default OS account

try
{
    // 3. AcquireTokenSilent 
    var authResult = await pca.AcquireTokenSilent(new[] { "User.Read" }, accountToLogin)
                              .ExecuteAsync();
}
catch (MsalUiRequiredException) // no change in the pattern
{
    // 4. Specific: Switch to the UI thread for next call . Not required for console apps.
    await SwitchToUiThreadAsync(); // not actual code, this is different on each platform / tech

    // 5. AcquireTokenInteractive
    var authResult = await pca.AcquireTokenInteractive(new[] { "User.Read" })
                              .WithAccount(accountToLogin)  // this already exists in MSAL, but it is more important for WAM
                              .WithParentActivityOrWindow(myWindowHandle) // to be able to parent WAM's windows to your app (optional, but highly recommended; not needed on UWP)
                              .ExecuteAsync();
}
```
   :::column-end:::
:::row-end:::

El código de MSAL mostrado anteriormente usa WAM (administrador de autenticación web), que es la estrategia recomendada. Si quiere usar la autenticación interactiva sin WAM, consulte [Autenticación interactiva](scenario-desktop-acquire-token-interactive.md).

## <a name="integrated-windows-authentication"></a>[Autenticación integrada de Windows](#tab/iwa)

La autenticación integrada de Windows es el lugar en que la aplicación cliente pública inicia sesión mediante la misma identidad que usó para iniciar sesión en un dominio de Windows (para máquinas unidas a un dominio o unidas a Azure AD).

#### <a name="find-out-if-your-code-uses-integrated-windows-authentication"></a>Averiguar si el código usa la autenticación integrada de Windows

El código ADAL de la aplicación usa escenarios de autenticación integrada de Windows si contiene una llamada a `AcquireTokenAsync` disponible como método de extensión de la clase `AuthenticationContextIntegratedAuthExtensions`, con los parámetros siguientes:

- `resource`, que representa el recurso para el que pide un token.
- `clientId`, que es un GUID que representa el registro de la aplicación.
- `UserCredential`, que representa el usuario para el que intenta solicitar el token.

#### <a name="update-the-code-for-integrated-windows-authentication-scenarios"></a>Actualización del código en escenarios de autenticación integrada de Windows

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

En este caso, reemplazamos la llamada a `AuthenticationContext.AcquireTokenAsync` por una llamada a `IPublicClientApplication.AcquireTokenByIntegratedWindowsAuth`.

Esta es una comparación del código de ADAL.NET y MSAL.NET en escenarios de autenticación integrada de Windows:

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(resource, clientId,
                                         new UserCredential("john@contoso.com"));
```
:::column-end:::   
:::column span="":::
```csharp
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
   :::column-end:::
:::row-end:::

## <a name="username-password"></a>[Nombre de usuario/contraseña](#tab/up)

En la autenticación con nombre de usuario y contraseña, el inicio de sesión se produce cuando se proporciona una credencial de nombre de usuario y contraseña.
#### <a name="find-out-if-your-code-uses-username-password-authentication"></a>Averiguar si el código usa la autenticación de nombre de usuario y contraseña

El código de ADAL de la aplicación usa escenarios de autenticación de nombre de usuario y contraseña si contiene una llamada a `AcquireTokenAsync` disponible como método de extensión de la clase `AuthenticationContextIntegratedAuthExtensions`, con los parámetros siguientes:

- `resource`, que representa el recurso para el que pide un token.
- `clientId`, que es un GUID que representa el registro de la aplicación.
- Objeto `UserPasswordCredential` que contiene el nombre de usuario y la contraseña del usuario para el que intenta solicitar el token.

#### <a name="update-the-code-for-username-password-auth-scenarios"></a>Actualizar el código en escenarios de autenticación de nombre de usuario y contraseña

En este caso, reemplazamos la llamada a `AuthenticationContext.AcquireTokenAsync` por una llamada a `IPublicClientApplication.AcquireTokenByUsernamePassword`.

Esta es una comparación del código de ADAL.NET y MSAL.NET en escenarios de nombre de usuario y contraseña:

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
var ac = new AuthenticationContext("https://login.microsoftonline.com/<tenantId>");
AuthenticationResult result;
result = await context.AcquireTokenAsync(
   resource, clientId, 
   new UserPasswordCredential("john@contoso.com", johnsPassword));

```
:::column-end:::   
:::column span="":::
```csharp
 string authority = "https://login.microsoftonline.com/contoso.com";
 string[] scopes = new string[] { "user.read" };
 IPublicClientApplication app;
 app = PublicClientApplicationBuilder.Create(clientId)
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
   var securePassword = new SecureString();
   foreach (char c in "dummy")        // you should fetch the password
    securePassword.AppendChar(c);  // keystroke by keystroke

   result = await app.AcquireTokenByUsernamePassword(scopes,
                                                    "joe@contoso.com",
                                                     securePassword)
                      .ExecuteAsync();
  }
  catch(MsalException)
  {
   // See details below
  }
 }
 Console.WriteLine(result.Account.Username);
```
   :::column-end:::
:::row-end:::

## <a name="device-code"></a>[Código del dispositivo](#tab/devicecode)

En la autenticación de flujo de código de dispositivo, un dispositivo con experiencia de usuario limitada muestra un código de dispositivo para completar el flujo de autenticación en un dispositivo alternativo.

#### <a name="find-out-if-your-code-uses-device-code-flow-authentication"></a>Averiguar si el código usa la autenticación de flujo de código de dispositivo

El código de ADAL de la aplicación usa escenarios de flujo de código de dispositivo si contiene una llamada a `AuthenticationContext.AcquireTokenByDeviceCodeAsync`, con los parámetros siguientes:
- Objeto `DeviceCodeResult`, del que se crea una instancia con el parámetro `resourceID` del recurso para el que pide un token, y `clientId`, que es el GUID que representa la aplicación.

#### <a name="update-the-code-for-device-code-flow-scenarios"></a>Actualizar el código en escenarios de flujo de código de dispositivo

 [!INCLUDE [Common steps](includes/msal-net-adoption-steps-public-clients.md)]

En este caso, reemplazamos la llamada a `AuthenticationContext.AcquireTokenAsync` por una llamada a `IPublicClientApplication.AcquireTokenWithDeviceCode`.

Esta es una comparación del código de ADAL.NET y MSAL.NET en escenarios de flujo de código de dispositivo:

:::row:::
:::column span="":::
    ADAL
:::column-end:::
:::column span="":::
    MSAL
:::column-end:::
:::row-end:::
:::row:::
:::column span="":::
      
```csharp
static async Task<AuthenticationResult> GetTokenViaCode(AuthenticationContext ctx)
{
 AuthenticationResult result = null;
 try
 {
  result = await ac.AcquireTokenSilentAsync(resource, clientId);
 }
 catch (AdalException adalException)
 {
  if (adalException.ErrorCode == AdalError.FailedToAcquireTokenSilently
   || adalException.ErrorCode == AdalError.InteractionRequired)
  {
  try
  {
   DeviceCodeResult codeResult = await ctx.AcquireDeviceCodeAsync(resource, clientId);
   Console.WriteLine("You need to sign in.");
   Console.WriteLine("Message: " + codeResult.Message + "\n");
   result = await ctx.AcquireTokenByDeviceCodeAsync(codeResult);
  }
  catch (Exception exc)
  {
   Console.WriteLine("Something went wrong.");
   Console.WriteLine("Message: " + exc.Message + "\n");
  }
 }
 return result;
}

```
:::column-end:::   
:::column span="":::
```csharp
private const string ClientId = "<client_guid>";
private const string Authority = "https://login.microsoftonline.com/contoso.com";
private readonly string[] scopes = new string[] { "user.read" };

static async Task<AuthenticationResult> GetATokenForGraph()
{
    IPublicClientApplication pca = PublicClientApplicationBuilder
            .Create(ClientId)
            .WithAuthority(Authority)
            .WithDefaultRedirectUri()
            .Build();

    var accounts = await pca.GetAccountsAsync();

    // All AcquireToken* methods store the tokens in the cache, so check the cache first
    try
    {
        return await pca.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
            .ExecuteAsync();
    }
    catch (MsalUiRequiredException ex)
    {
        // No token found in the cache or AAD insists that a form interactive auth is required (e.g. the tenant admin turned on MFA)
        // If you want to provide a more complex user experience, check out ex.Classification

        return await AcquireByDeviceCodeAsync(pca);
    }
}

private static async Task<AuthenticationResult> AcquireByDeviceCodeAsync(IPublicClientApplication pca)
{
    try
    {
        var result = await pca.AcquireTokenWithDeviceCode(scopes,
            deviceCodeResult =>
            {
                    // This will print the message on the console which tells the user where to go sign-in using
                    // a separate browser and the code to enter once they sign in.
                    // The AcquireTokenWithDeviceCode() method will poll the server after firing this
                    // device code callback to look for the successful login of the user via that browser.
                    // This background polling (whose interval and timeout data is also provided as fields in the
                    // deviceCodeCallback class) will occur until:
                    // * The user has successfully logged in via browser and entered the proper code
                    // * The timeout specified by the server for the lifetime of this code (typically ~15 minutes) has been reached
                    // * The developing application calls the Cancel() method on a CancellationToken sent into the method.
                    //   If this occurs, an OperationCanceledException will be thrown (see catch below for more details).
                    Console.WriteLine(deviceCodeResult.Message);
                return Task.FromResult(0);
            }).ExecuteAsync();

        Console.WriteLine(result.Account.Username);
        return result;
    }

    // TODO: handle or throw all these exceptions depending on your app
    catch (MsalServiceException ex)
    {
        // Kind of errors you could have (in ex.Message)

        // AADSTS50059: No tenant-identifying information found in either the request or implied by any provided credentials.
        // Mitigation: as explained in the message from Azure AD, the authoriy needs to be tenanted. you have probably created
        // your public client application with the following authorities:
        // https://login.microsoftonline.com/common or https://login.microsoftonline.com/organizations

        // AADSTS90133: Device Code flow is not supported under /common or /consumers endpoint.
        // Mitigation: as explained in the message from Azure AD, the authority needs to be tenanted

        // AADSTS90002: Tenant <tenantId or domain you used in the authority> not found. This may happen if there are
        // no active subscriptions for the tenant. Check with your subscription administrator.
        // Mitigation: if you have an active subscription for the tenant this might be that you have a typo in the
        // tenantId (GUID) or tenant domain name.
    }
    catch (OperationCanceledException ex)
    {
        // If you use a CancellationToken, and call the Cancel() method on it, then this *may* be triggered
        // to indicate that the operation was cancelled.
        // See https://docs.microsoft.com/dotnet/standard/threading/cancellation-in-managed-threads
        // for more detailed information on how C# supports cancellation in managed threads.
    }
    catch (MsalClientException ex)
    {
        // Possible cause - verification code expired before contacting the server
        // This exception will occur if the user does not manage to sign-in before a time out (15 mins) and the
        // call to `AcquireTokenWithDeviceCode` is not cancelled in between
    }
}
```
   :::column-end:::
:::row-end:::

---
### <a name="msal-benefits"></a>Ventajas de MSAL

Entre las principales ventajas de MSAL.NET para la aplicación se incluyen las siguientes:

- **Resistencia**. MSAL.NET ayuda a que la aplicación sea resistente a través de lo siguiente:

   - Ventajas del servicio de credenciales almacenadas en caché (CCS) de Azure AD. CCS funciona como una copia de seguridad de Azure AD.
   - Renovación proactiva de tokens si la API a la que llama habilita tokens de larga duración a través de [Evaluación continua de acceso](app-resilience-continuous-access-evaluation.md).

### <a name="troubleshooting"></a>Solución de problemas

La siguiente información de solución de problemas hace dos suposiciones: 

- El código ADAL.NET estaba funcionando.
- Migró a MSAL manteniendo el mismo identificador de cliente.

Si recibe una excepción con cualquiera de los mensajes siguientes: 

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

Puede solucionar problemas de la excepción mediante estos pasos:

1. Confirme que usa la versión más reciente de MSAL.NET.
1. Confirme que el host de autoridad que estableció al compilar la aplicación cliente confidencial y el host de autoridad que usó con ADAL son similares. En concreto, ¿es la misma [nube](msal-national-cloud.md) (Azure Government, Azure China 21Vianet o Azure Alemania)?

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las [diferencias entre las aplicaciones de ADAL.NET y MSAL.NET](msal-net-differences-adal-net.md).
Obtenga más información sobre la [serialización de la caché de tokens en MSAL.NET](msal-net-token-cache-serialization.md)
