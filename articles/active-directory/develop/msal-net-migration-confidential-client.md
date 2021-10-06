---
title: Migración de aplicaciones cliente confidenciales a MSAL.NET
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo migrar una aplicación cliente confidencial de la Biblioteca de autenticación de Azure Active Directory para .NET a la Biblioteca de autenticación de Microsoft para .NET.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 72537e46d7d249190585552e0a8ee11c43e40340
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128566565"
---
# <a name="migrate-confidential-client-applications-from-adalnet-to-msalnet"></a>Migración de aplicaciones cliente confidenciales de ADAL.NET a MSAL.NET

En este artículo se describe cómo migrar una aplicación cliente confidencial de la Biblioteca de autenticación de Azure Active Directory para .NET (ADAL.NET) a la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET). Las aplicaciones cliente confidenciales son aplicaciones web, API web y aplicaciones de demonio que llaman a otro servicio en su propio nombre. Para más información sobre las aplicaciones confidenciales, consulte [Flujos de autenticación y escenarios de aplicaciones](authentication-flows-app-scenarios.md). Si la aplicación se basa en ASP.NET Core, use [Microsoft.Identity.Web](microsoft-identity-web.md).

Para los registros de aplicaciones:

- No es necesario crear un nuevo registro de aplicaciones (se mantiene el mismo identificador de cliente).
- No es necesario cambiar las autorizaciones previas (permisos de API con consentimiento del administrador).

## <a name="migration-steps"></a>Pasos de migración

1. Busque el código mediante ADAL.NET en la aplicación.

   El código que usa ADAL en una aplicación cliente confidencial crea instancias de `AuthenticationContext` y llama a `AcquireTokenByAuthorizationCode` o a una invalidación de `AcquireTokenAsync` con los parámetros siguientes:

   - Cadena de `resourceId`. Esta variable es el URI del identificador de la aplicación de la API web a la que desea llamar.
   - Instancia de `IClientAssertionCertificate` o `ClientAssertion`. Esta instancia proporciona las credenciales de cliente de la aplicación para demostrar la identidad de esta.

1. Tras haber identificado que tiene aplicaciones que usan ADAL.NET, instale el paquete NuGet de MSAL.NET [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) y actualice sus referencias de la biblioteca de proyectos. Para obtener más información, consulte cómo [instalar un paquete NuGet](https://www.bing.com/search?q=install+nuget+package). Si quiere usar serializadores de caché de tokens, instale también [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web).

1. Actualice el código según el escenario de cliente confidencial. Algunos pasos son comunes y se aplican en todos los escenarios de cliente confidenciales. Otros pasos son únicos para cada escenario. 

   Los escenarios de cliente confidenciales son:

   - [Escenarios de demonio](?tabs=daemon#migrate-daemon-apps) compatibles con las aplicaciones web, las API web y las aplicaciones de consola de demonio.
   - [API web que llama a las API web de nivel inferior](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis) compatibles con las API web que llaman a las API web de nivel inferior en nombre del usuario.
   - [Aplicación web que llama a las API web](?tabs=authcode#migrate-a-web-api-that-calls-downstream-web-apis) compatibles con las aplicaciones web que inician la sesión de los usuarios y llaman a una API web de nivel inferior.

Podría haber proporcionado un contenedor en torno a ADAL.NET para controlar los certificados y el almacenamiento en caché. En este artículo se usa el mismo enfoque para mostrar el proceso de migración de ADAL.NET a MSAL.NET. Sin embargo, este código solo tiene fines de demostración. No copie ni pegue estos contenedores ni los integre en el código tal como están.

## <a name="daemon"></a>[Demonio](#tab/daemon)

### <a name="migrate-daemon-apps"></a>Migración de aplicaciones de demonio

Los escenarios de demonio usan el [flujo de credenciales de cliente](v2-oauth2-client-creds-grant-flow.md) de OAuth2.0. También se denominan llamadas de servicio a servicio. La aplicación adquiere un token en su propio nombre, no en nombre de un usuario.

#### <a name="find-out-if-your-code-uses-daemon-scenarios"></a>Averiguar si el código usa escenarios de demonio

El código ADAL de la aplicación usa escenarios de demonio si contiene una llamada a `AuthenticationContext.AcquireTokenAsync` con los parámetros siguientes:

- Un recurso (URI del identificador de la aplicación) como primer parámetro
- `IClientAssertionCertificate` o `ClientAssertion` como segundo parámetro

`AuthenticationContext.AcquireTokenAsync` no tiene un parámetro de tipo `UserAssertion`. Si lo tiene, entonces la aplicación es una API web y usa el escenario de [API web que llama a las API web de nivel inferior](?tabs=obo#migrate-a-web-api-that-calls-downstream-web-apis).

#### <a name="update-the-code-of-daemon-scenarios"></a>Actualización del código de escenarios de demonio

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

En este caso, reemplazamos la llamada a `AuthenticationContext.AcquireTokenAsync` por una llamada a `IConfidentialClientApplication.AcquireTokenClient`.

Esta es una comparación del código ADAL.NET y MSAL.NET de escenarios de demonio:

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
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult()
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);


  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                );

  return authResult;
 }
}
```
:::column-end:::   
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority 
   = "https://login.microsoftonline.com/{tenant}";
 // App ID URI of web API to call
 const string resourceId = "https://target-api.domain.com";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult()
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }

  var authResult = await app.AcquireTokenForClient(
              new [] { $"{resourceId}/.default" })
              .ExecuteAsync()
              .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>Beneficiarse del almacenamiento en caché de tokens

Para beneficiarse de la caché en memoria, la instancia de `IConfidentialClientApplication` debe mantenerse en una variable de miembro. Si vuelve a crear la aplicación cliente confidencial cada vez que solicite un token, no se beneficiará de la caché de tokens.

Tendrá que serializar `AppTokenCache` si decide no usar la caché de tokens de aplicación en memoria predeterminada. Del mismo modo, si desea implementar una caché de tokens distribuida, deberá serializar `AppTokenCache`. Para obtener detalles, consulte el artículo sobre la [caché de tokens para una aplicación web o API web (aplicación cliente confidencial)](msal-net-token-cache-serialization.md?tabs=aspnet) y el ejemplo [active-directory-dotnet-v1-to-v2/ConfidentialClientTokenCache](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache).

[Obtenga más información sobre el escenario de demonio](scenario-daemon-overview.md) y cómo se implementa con MSAL.NET o Microsoft.Identity.Web en las aplicaciones nuevas.

## <a name="web-api-calling-downstream-web-apis"></a>[API web que llama a las API web de nivel inferior](#tab/obo)

### <a name="migrate-a-web-api-that-calls-downstream-web-apis"></a>Migración de una API web que llama a las API web de nivel inferior

Las API web que llaman a las API web de nivel inferior usan el flujo [con derechos delegados (OBO)](v2-oauth2-on-behalf-of-flow.md) de OAuth2.0. El código de la API web usa el token recuperado del encabezado autorizado HTTP y lo valida. Este token se cambia por un token para llamar a la API web de nivel inferior. Este token se usa como una instancia de `UserAssertion` tanto en ADAL.NET como en MSAL.NET.

#### <a name="find-out-if-your-code-uses-obo"></a>Averiguar si el código usa OBO

El código ADAL de la aplicación usa OBO si contiene una llamada a `AuthenticationContext.AcquireTokenAsync` con los parámetros siguientes:

- Un recurso (URI del identificador de la aplicación) como primer parámetro
- `IClientAssertionCertificate` o `ClientAssertion` como segundo parámetro
- Parámetro de tipo `UserAssertion`

#### <a name="update-the-code-by-using-obo"></a>Actualización del código mediante OBO

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)]

En este caso, reemplazamos la llamada a `AuthenticationContext.AcquireTokenAsync` por una llamada a `IConfidentialClientApplication.AcquireTokenOnBehalfOf`.

Esta es una comparación del código OBO de ejemplo de ADAL.NET y MSAL.NET:

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
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId, 
  string tokenUsedToCallTheWebApi)
 {


  var authContext = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await authContext.AcquireTokenAsync(
                                      resourceId,
                                      clientAssertionCert,
                                      userAssertion,
                                );

  return authResult;
 }
}
```
:::column-end:::
:::column span="":::
```csharp
using Microsoft.Identity.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
  const string authority 
  = "https://login.microsoftonline.com/common";
 X509Certificate2 certificate = LoadCertificate();

 IConfidentialClientApplication app;

 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string tokenUsedToCallTheWebApi)
 {
  if (app == null)
  {
   app = ConfidentialClientApplicationBuilder.Create(ClientId)
           .WithCertificate(certificate)
           .WithAuthority(authority)
           .Build();
  }


  var userAssertion = new UserAssertion(tokenUsedToCallTheWebApi);

  var authResult = await app.AcquireTokenOnBehalfOf(
              new string[] { $"{resourceId}/.default" },
              userAssertion)
              .ExecuteAsync()
              .ConfigureAwait(false);
  
  return authResult;
 }
}
```
:::column-end:::
:::row-end:::

#### <a name="benefit-from-token-caching"></a>Beneficiarse del almacenamiento en caché de tokens

Para el almacenamiento en caché de tokens en OBO, debe usar una caché de tokens distribuida. Para obtener detalles, consulte el artículo sobre la [caché de tokens para una aplicación web o API web (aplicación cliente confidencial)](msal-net-token-cache-serialization.md?tabs=aspnet) y lea el [código de ejemplo](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache).

```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```

[Obtenga más información sobre las API web que llaman a las API web de nivel inferior](scenario-web-api-call-api-overview.md) y cómo se implementan con MSAL.NET o Microsoft.Identity.Web en las aplicaciones nuevas.

## <a name="web-app-calling-web-apis"></a>[Aplicación web que llama a las API web](#tab/authcode)

### <a name="migrate-a-web-app-that-calls-web-apis"></a>Migración de una aplicación web que llama a las API web

Si la aplicación usa ASP.NET Core, se recomienda encarecidamente que actualice a Microsoft.Identity.Web, que procesa todo automáticamente. Para una presentación rápida, consulte el [anuncio de Microsoft.Identity.Web de disponibilidad general](https://github.com/AzureAD/microsoft-identity-web/wiki/1.0.0). Para obtener detalles sobre cómo usarlo en una aplicación web, consulte los [motivos para la utilización de Microsoft.Identity.Web en las aplicaciones web](https://aka.ms/ms-id-web/webapp).

Las aplicaciones web que inician la sesión de los usuarios y llaman a las API web en nombre de los usuarios utilizan el [flujo de código de autorización](v2-oauth2-auth-code-flow.md) de OAuth2.0. Generalmente:

1. La aplicación web inicia la sesión de un usuario mediante la ejecución de un primer segmento del flujo de código de autorización. Para ello, vaya al punto de conexión de autorización en Azure Active Directory (Azure AD). El usuario inicia sesión y realiza autenticaciones multifactor si es necesario. Como resultado de esta operación, la aplicación recibe el código de autorización. Hasta ahora, ADAL y MSAL no están implicados.
2. La aplicación ejecuta el segundo segmento del flujo de código de autorización. Usa el código de autorización para obtener un token de acceso, un token de identificador y un token de actualización. La aplicación debe proporcionar el valor `redirectUri`, que es el URI en el que Azure AD proporcionará los tokens de seguridad. Una vez que la aplicación recibe ese URI, normalmente llama a `AcquireTokenByAuthorizationCode` para que ADAL o MSAL canjee el código y obtenga un token que se almacenará en la caché de tokens.
3. La aplicación usa ADAL o MSAL para llamar a `AcquireTokenSilent` para que pueda obtener tokens para llamar a las API web necesarias. Esto se hace desde los controladores de la aplicación web.

#### <a name="find-out-if-your-code-uses-the-auth-code-flow"></a>Averiguar si el código usa el flujo de código de autenticación

El código de ADAL de la aplicación usa el flujo de código de autenticación si contiene una llamada a `AuthenticationContext.AcquireTokenByAuthorizationCodeAsync`.

#### <a name="update-the-code-by-using-the-authorization-code-flow"></a>Actualización del código mediante el flujo de código de autorización

[!INCLUDE [Common steps](includes/msal-net-adoption-steps-confidential-clients.md)] 

En este caso, reemplazamos la llamada a `AuthenticationContext.AcquireTokenAsync` por una llamada a `IConfidentialClientApplication.AcquireTokenByAuthorizationCode`.

Esta es una comparación de los flujos de código de autorización de ejemplo de ADAL.NET y MSAL.NET:

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
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (AppID)";
 const string authority 
     = "https://login.microsoftonline.com/common";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();



 public async Task<AuthenticationResult> GetAuthenticationResult(
  string resourceId,
  string authorizationCode)
 {


  var ac = new AuthenticationContext(authority);
  var clientAssertionCert = new ClientAssertionCertificate(
                                  ClientId,
                                  certificate);



  var authResult = await ac.AcquireTokenByAuthorizationCodeAsync(
                                      authorizationCode,
                                      redirectUri,
                                      clientAssertionCert,
                                      resourceId,
                                );
  return authResult;
 }
}
```      
   :::column-end:::
   :::column span="":::
```csharp
using Microsoft.Identity.Client;
using Microsoft.Identity.Web;
using System;
using System.Security.Claims;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;

public partial class AuthWrapper
{
 const string ClientId = "Guid (Application ID)";
 const string authority
    = "https://login.microsoftonline.com/{tenant}";
 private Uri redirectUri = new Uri("host/login_oidc");
 X509Certificate2 certificate = LoadCertificate();

 public IConfidentialClientApplication CreateApplication()
 {
  IConfidentialClientApplication app;

  app = ConfidentialClientApplicationBuilder.Create(ClientId)
               .WithCertificate(certificate)
               .WithAuthority(authority)
               .WithRedirectUri(redirectUri.ToString())
               .WithLegacyCacheCompatibility(false)
               .Build();

  // Add a token cache. For details about other serialization
  // see https://aka.ms/msal-net-cca-token-cache-serialization
  app.AddInMemoryTokenCache();

  return app;
 }

 // Called from 'code received event'.
 public async Task<AuthenticationResult> GetAuthenticationResult(
      string resourceId,
      string authorizationCode)
 {
  IConfidentialClientApplication app = CreateApplication();

  var authResult = await app.AcquireTokenByAuthorizationCode(
                  new[] { $"{resourceId}/.default" },
                  authorizationCode)
                  .ExecuteAsync()
                  .ConfigureAwait(false);

  return authResult;
 }
}
```
   :::column-end:::
:::row-end:::

La llamada a `AcquireTokenByAuthorizationCode` agrega un token a la caché de tokens cuando se recibe el código de autorización. Para adquirir tokens adicionales para otros recursos o inquilinos, use `AcquireTokenSilent` en los controladores.

```csharp
public partial class AuthWrapper
{
 // Called from controllers
 public async Task<AuthenticationResult> GetAuthenticationResult(
      string resourceId2,
      string authority)
 {
  IConfidentialClientApplication app = CreateApplication();
  AuthenticationResult authResult;

  var scopes = new[] { $"{resourceId2}/.default" };
  var account = await app.GetAccountAsync(ClaimsPrincipal.Current.GetMsalAccountId());

  try
  {
   // try to get an already cached token
   authResult = await app.AcquireTokenSilent(
               scopes,
               account)
                .WithAuthority(authority)
                .ExecuteAsync().ConfigureAwait(false);
  }
  catch (MsalUiRequiredException)
  {
   // The controller will need to challenge the user
   // including asking for claims={ex.Claims}
   throw;
  }
  return authResult;
 }
}
```

#### <a name="benefit-from-token-caching"></a>Beneficiarse del almacenamiento en caché de tokens

Dado que la aplicación web usa `AcquireTokenByAuthorizationCode`, esta debe utilizar una caché de tokens distribuida para el almacenamiento en caché de tokens. Para obtener detalles, consulte el artículo sobre la [caché de tokens para una aplicación web o API web](msal-net-token-cache-serialization.md?tabs=aspnet) y lea el [código de ejemplo](https://github.com/Azure-Samples/active-directory-dotnet-v1-to-v2/tree/master/ConfidentialClientTokenCache).


```CSharp
app.UseInMemoryTokenCaches(); // or a distributed token cache.
```

#### <a name="handling-msaluirequiredexception"></a>Control de MsalUiRequiredException

Cuando el controlador intenta adquirir un token de forma silenciosa para otros ámbitos o recursos, es posible que MSAL.NET inicie una excepción `MsalUiRequiredException`. Esto se espera si, por ejemplo, el usuario necesita volver a iniciar sesión o si el acceso al recurso necesita más notificaciones (por ejemplo, debido a una directiva de acceso condicional). Para obtener más información sobre la mitigación, vea [Control de errores y excepciones en MSAL.NET](msal-error-handling-dotnet.md).

[Obtenga más información sobre las aplicaciones web que llaman a las API web](scenario-web-app-call-api-overview.md) y cómo se implementan con MSAL.NET o Microsoft.Identity.Web en las aplicaciones nuevas.

---

## <a name="msal-benefits"></a>Ventajas de MSAL

Entre las principales ventajas de MSAL.NET para la aplicación se incluyen las siguientes:

- **Resistencia**. MSAL.NET ayuda a que la aplicación sea resistente a través de lo siguiente:

   - Ventajas del servicio de credenciales almacenadas en caché (CCS) de Azure AD. CCS funciona como una copia de seguridad de Azure AD.
   - Renovación proactiva de tokens si la API a la que llama habilita tokens de larga duración a través de [Evaluación continua de acceso](app-resilience-continuous-access-evaluation.md).

- **Seguridad**. Puede adquirir tokens de prueba de posesión (PoP) si la API web a la que desea llamar así lo requiere. Para obtener detalles, consulte [Tokens de prueba de posesión en MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Proof-Of-Possession-(PoP)-tokens)

- **Rendimiento y escalabilidad**. Si no necesita compartir la caché con ADAL.NET, deshabilite la compatibilidad de la memoria caché heredada al crear la aplicación cliente confidencial (`.WithLegacyCacheCompatibility(false)`). Esto aumenta significativamente el rendimiento.
  
  ```csharp
  app = ConfidentialClientApplicationBuilder.Create(ClientId)
          .WithCertificate(certificate)
          .WithAuthority(authority)
          .WithLegacyCacheCompatibility(false)
          .Build();
  ```

## <a name="troubleshooting"></a>Solución de problemas

La siguiente información de solución de problemas hace dos suposiciones: 

- El código ADAL.NET estaba funcionando.
- Migró a MSAL manteniendo el mismo identificador de cliente.

Si recibe una excepción con cualquiera de los mensajes siguientes: 

> `AADSTS700027: Client assertion contains an invalid signature. [Reason - The key was not found.]`

> `AADSTS90002: Tenant 'cf61953b-e41a-46b3-b500-663d279ea744' not found. This may happen if there are no active`
> `subscriptions for the tenant. Check to make sure you have the correct tenant ID. Check with your subscription`
> `administrator.`

Puede solucionar problemas de la excepción mediante estos pasos:

1. Confirme que usa la versión más reciente de MSAL.NET.
1. Confirme que el host de autoridad que estableció al compilar la aplicación cliente confidencial y el host de autoridad que usó con ADAL son similares. En concreto, ¿es la misma [nube](msal-national-cloud.md) (Azure Government, Azure China 21Vianet o Azure Alemania)?

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las [diferencias entre las aplicaciones de ADAL.NET y MSAL.NET](msal-net-differences-adal-net.md).
Obtenga más información sobre la [serialización de la caché de tokens en MSAL.NET](msal-net-token-cache-serialization.md)