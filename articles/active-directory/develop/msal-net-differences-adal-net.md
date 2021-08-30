---
title: Diferencias entre las aplicaciones de ADAL.NET y MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Conozca las diferencias entre la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET) y la Biblioteca de autenticación de Azure AD para .NET (ADAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 06/09/2021
ms.author: jmprieur
ms.reviewer: saeeda, shermanouko
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: ea83d0098eaa44ffa6510a5f0fc8cea18115815c
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970387"
---
# <a name="differences-between-adalnet-and-msalnet-apps"></a>Diferencias entre las aplicaciones de ADAL.NET y MSAL.NET

Migrar las aplicaciones del uso de ADAL al uso de MSAL incluye ventajas de seguridad y resistencia. En este artículo se describen las diferencias entre MSAL.NET y ADAL.NET. En la mayoría de los casos querrá usar MSAL.NET y la Plataforma de identidad de Microsoft, que es la última generación de bibliotecas de autenticación de Microsoft. Mediante MSAL.NET adquiere tokens para los usuarios que inician sesión en su aplicación con Azure AD (cuentas profesionales y educativas), cuentas (personales) Microsoft (MSA) o Azure AD B2C.

Si ya está familiarizado con ADAL.NET y el punto de conexión de Azure AD para desarrolladores (v1.0), descubra [qué tiene de diferente la Plataforma de identidad de Microsoft](../azuread-dev/azure-ad-endpoint-comparison.md). Aun así deberá usar ADAL.NET si en la aplicación van a iniciar sesión usuarios con versiones anteriores de [Servicios de federación de Active Directory (ADFS)](/windows-server/identity/active-directory-federation-services). Para más información, consulte al [soporte técnico de Azure](https://aka.ms/msal-net-adfs-support).

|   | **ADAL NET**  | **MSAL NET**  |
|-----------------------------|--------------------------------------------|---------------------|
| **Espacios de nombres y paquetes NuGet**  |ADAL se consumió desde el paquete NuGet [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory). El espacio de nombres era `Microsoft.IdentityModel.Clients.ActiveDirectory`.  | Agregue el paquete NuGet [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) y use el espacio de nombres `Microsoft.Identity.Client`. Si va a compilar una aplicación cliente confidencial, consulte [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web). |
| **Ámbitos y recursos**  | ADAL.NET adquiere tokens para los *recursos*. |  MSAL.NET adquiere tokens para los *ámbitos*. Algunas invalidaciones de `AcquireTokenXXX` de MSAL.NET requieren un parámetro denominado scopes (`IEnumerable<string> scopes`). Este parámetro no es más que una lista de cadenas que declaran los permisos y recursos que se solicitan. Los [ámbitos de Microsoft Graph](/graph/permissions-reference) son muy conocidos. También puede [acceder a los recursos de la versión 1.0 con MSAL.NET](#scopes). |
| **Clases principales**  | ADAL.NET usó [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) como representación de la conexión al servidor de autorización o al servicio de token de seguridad (STS), a través de una autoridad. | MSAL.NET está diseñado en torno a [aplicaciones cliente](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Define interfaces `IPublicClientApplication` para aplicaciones cliente públicas e interfaces `IConfidentialClientApplication` para aplicaciones cliente confidenciales, así como una interfaz base `IClientApplicationBase` para el contrato común a ambos tipos de aplicaciones.|
| **Obtención de tokens**  | En los clientes públicos, ADAL usa `AcquireTokenAsync` y `AcquireTokenSilentAsync` para las llamadas de autenticación. | En los clientes públicos, MSAL usa `AcquireTokenInteractive` y `AcquireTokenSilent` para las mismas llamadas de autenticación. Los parámetros son distintos de los usados en ADAL. <br><br>En las aplicaciones cliente confidenciales, hay [métodos de adquisición de tokens](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-Tokens) con un nombre explícito en función del escenario. Otra diferencia es que, en MSAL.NET, no es preciso usar el elemento `ClientID` de la aplicación en todas las llamadas a AcquireTokenXX. El valor `ClientID` se establece una sola vez al compilar `IPublicClientApplication` o `IConfidentialClientApplication`.|
|  **IAccount e IUser**  | ADAL define la noción de usuario a través de la interfaz IUser. Sin embargo, un usuario es una persona o un agente de software. Como tal, un usuario puede tener una o varias cuentas en la Plataforma de identidad de Microsoft (varias cuentas de Azure AD, Azure AD B2C, cuentas personales de Microsoft). El usuario también puede ser responsable de una o varias cuentas de la Plataforma de identidad de Microsoft. | MSAL.NET define el concepto de cuenta (a través de la interfaz de IAccount). La interfaz IAccount representa información sobre una sola cuenta. El usuario puede tener varias cuentas en distintos inquilinos. MSAL.NET proporciona mejor información en escenarios de invitado, cuando se proporciona información de la cuenta doméstica. Lea más detalles sobre las [diferencias entre IUser e IAccount](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/msal-net-2-released#iuser-is-replaced-by-iaccount).|
|  **Persistencia en la caché**  | ADAL.NET le permite ampliar la clase `TokenCache` para implementar la funcionalidad de persistencia deseada en las plataformas sin un almacenamiento seguro (.NET Framework y .NET Core) mediante el uso de los métodos `BeforeAccess` y `BeforeWrite`. Para obtener más información, consulte la sección sobre la [serialización de la memoria caché de tokens en ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization). | MSAL.NET hace que la caché de tokens sea una clase sellada y elimina la posibilidad de ampliarla. De esta forma, la implementación de la persistencia en la caché de tokens debe ser en forma de clase auxiliar que interactúa con la caché de tokens sellada. Esta interacción se describe en el documento sobre la [serialización de la memoria caché de tokens en MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization). La serialización para una aplicación cliente pública (consulte el apartado sobre la [caché de tokens para una aplicación cliente pública](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application)) es distinta a la de una aplicación cliente confidencial (consulte el apartado sobre la [caché de tokens para una aplicación web o API web](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization#token-cache-for-a-public-client-application)). |
| **Autoridad común** | ADAL usa Azure AD v1.0. La autoridad `https://login.microsoftonline.com/common` en Azure AD v1.0 (que usa ADAL) permite a los usuarios iniciar sesión con cualquier cuenta de organización de AAD (profesional o educativa). Azure AD v1.0 no permite el inicio de sesión con cuentas personales de Microsoft. Para obtener más información, consulte el documento sobre [validación de autoridad en ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation). | MSAL usa Azure AD v2.0. La autoridad `https://login.microsoftonline.com/common` de Azure AD v2.0 (que usa MSAL) permite a los usuarios iniciar sesión con cualquier cuenta de organización de AAD (profesional o educativa) o con una cuenta personal de Microsoft. Para restringir el inicio de sesión solo con cuentas de organización (cuentas profesionales o educativas) en MSAL, deberá usar el punto de conexión `https://login.microsoftonline.com/organizations`.  Para más información, consulte el parámetro `authority` en la [aplicación cliente pública](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication). |

## <a name="supported-grants"></a>Concesiones que se admiten

A continuación se muestra un resumen en el que se comparan las concesiones admitidas de MSAL.NET y ADAL.NET para las aplicaciones cliente tanto públicas como confidenciales.

### <a name="public-client-applications"></a>Aplicaciones cliente públicas

La imagen siguiente resume algunas de las diferencias entre ADAL.NET y MSAL.NET para una aplicación cliente pública.

[![Captura de pantalla que muestra algunas de las diferencias entre ADAL.NET y MSAL.NET para una aplicación cliente pública.](media/msal-compare-msaldotnet-and-adaldotnet/differences.png)](media/msal-compare-msaldotnet-and-adaldotnet/differences.png#lightbox)

Estas son las concesiones que se admiten en ADAL.NET y MSAL.NET para aplicaciones de escritorio y para dispositivos móviles.

Conceder                             | MSAL.NET                                                                                                                     | ADAL.NET                                                                                                                                                                                                   |
--------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
Interactive                       | [Adquisición de tokens de forma interactiva en MSAL.NET](scenario-desktop-acquire-token.md#acquire-a-token-interactively)    | [Autenticación interactiva](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows)                                              |
Autenticación integrada de Windows | [Autenticación integrada de Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication)         | [Autenticación integrada en Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos))  |
Nombre de usuario y contraseña               | [Autenticación de nombre de usuario y contraseña](scenario-desktop-acquire-token.md#username-and-password)                      | [Adquisición de tokens con nombre de usuario y contraseña](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)                                        |
Flujo de código de dispositivo                  | [Flujo de código de dispositivo](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser)                    | [Perfil de dispositivo para dispositivos sin exploradores web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers)                                |

### <a name="confidential-client-applications"></a>Aplicaciones cliente confidenciales

La imagen siguiente resume algunas de las diferencias entre ADAL.NET y MSAL.NET para una aplicación cliente confidencial.
 
[![Captura de pantalla que muestra algunas de las diferencias entre ADAL.NET y MSAL.NET para una aplicación cliente confidencial.](media/msal-net-migration/confidential-client-application.png)](media/msal-net-migration/confidential-client-application.png#lightbox)


Estas son las concesiones que se admiten en ADAL.NET, MSAL.NET y Microsoft.Identity.Web para aplicaciones web, API web y aplicaciones de demonio.

Tipo de aplicación              | Conceder              | MSAL.NET                                                                                                                             | ADAL.NET                                                                                                                                                                                              |
------------------------ | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
Aplicación web, API web, demonio | Credenciales de cliente | [Flujos de credenciales de cliente en MSAL.NET](scenario-daemon-acquire-token.md#acquiretokenforclient-api)                        | [Flujos de credenciales de cliente en ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows)                                                               |
API Web                  | En nombre de       | [En nombre de MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/on-behalf-of)                 | [Llamadas de servicio a servicio en nombre del usuario con ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user)               |
Aplicación web                  | Código de autenticación          | [Adquisición de tokens con códigos de autorización en aplicaciones web con MSAL.NET](scenario-web-app-call-api-acquire-token.md)  | [Adquisición de tokens con códigos de autorización en aplicaciones web con ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) |

## <a name="migrating-from-adal-2x-with-refresh-tokens"></a>Migración desde ADAL 2.x con tokens de actualización

En ADAL.NET v2.X, se han expuesto los tokens de actualización, lo que le permite desarrollar soluciones en torno al uso de estos tokens mediante su almacenamiento en caché y el uso de los `AcquireTokenByRefreshToken` que proporciona ADAL 2.x.

Algunas de estas soluciones se han usado en escenarios como:

- Servicios de larga duración que realizan acciones, incluida la actualización de paneles para los usuarios cuando estos ya no están conectados o no han iniciado sesión en la aplicación.
- Escenarios de WebFarm que permiten al cliente llevar el token de actualización al servicio web (el almacenamiento en caché se realiza en el lado cliente, con la cookie cifrada, y no en el del servidor).

Por motivos de seguridad, MSAL.NET no expone los tokens de actualización. MSAL los administra automáticamente.

Afortunadamente, MSAL.NET tiene una API que permite migrar los tokens de actualización anteriores (adquiridos con ADAL) a `IConfidentialClientApplication`:

```csharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration
/// scenarios where you have a RefreshToken available.
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint.
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```

Con este método puede proporcionar el token de actualización que ha usado anteriormente junto con todos los ámbitos (recursos) que quiera. El token de actualización se intercambiará por otro nuevo y se almacenará en caché en la aplicación.

Como este método está pensado para escenarios que no son habituales, no se puede acceder a él de inmediato con `IConfidentialClientApplication` sin convertirlo primero en `IByRefreshToken`.

El fragmento de código a continuación muestra código de migración en una aplicación cliente confidencial.

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;

AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

`GetCachedRefreshTokenForSignedInUser` recupera el token de actualización que almacenó de alguna forma una versión anterior de la aplicación que utilizaba ADAL 2.x. `GetTokenCacheForSignedInUser` deserializa una cache para el usuario que ha iniciado sesión (ya que las aplicaciones cliente confidenciales deben tener una sola caché por usuario).

Un token de acceso y un token de identificador se devuelven en el valor `AuthenticationResult` mientras que el nuevo token de actualización se almacena en la memoria caché. Este método también se puede usar para diversos escenarios de integración en los que hay un token de actualización disponible.

## <a name="v10-and-v20-tokens"></a>Tokens de las versiones 1.0 y 2.0

Hay dos versiones de los tokens: v1.0 y v2.0. El punto de conexión de v1.0 (que usa ADAL) emite tokens de identificador de v1.0, mientras que el punto de conexión de v2.0 (que usa MSAL) emite tokens de identificador de v2.0. Sin embargo, ambos puntos de conexión emiten tokens de acceso de la versión del token que la API web acepta. Una propiedad del manifiesto de aplicación de la API web permite a los desarrolladores elegir la versión del token que se acepta. Consulte `accessTokenAcceptedVersion` en la documentación de referencia del [manifiesto de aplicación](reference-app-manifest.md).

Para obtener más información sobre los tokens de acceso de las versiones 1.0 y 2.0, consulte el documento sobre [tokens de acceso de Azure Active Directory](access-tokens.md).

## <a name="exceptions"></a>Excepciones

### <a name="interaction-required-exceptions"></a>Excepciones requeridas en la interacción

Mediante MSAL.NET se detecta `MsalUiRequiredException`, como se describe en [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

Para obtener más detalles, consulte [Control de errores y excepciones en MSAL.NET](msal-error-handling-dotnet.md)

ADAL.NET tiene excepciones menos explícitas. Por ejemplo, cuando se produce un error de autenticación en modo silencioso en ADAL, el procedimiento es detectar la excepción y buscar el código de error `user_interaction_required`:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Para obtener más detalles, consulte el [patrón recomendado para adquirir un token en aplicaciones cliente públicas](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) con ADAL.NET.

### <a name="handling-claim-challenge-exceptions"></a>Controlar las excepciones en los desafíos de notificaciones

En ocasiones, al adquirir un token, Azure AD produce una excepción en caso de que un recurso requiera más notificaciones del usuario (por ejemplo, la autenticación en dos fases).

En MSAL.NET, las excepciones en los desafíos de notificaciones se controlan de la siguiente forma:

- `Claims` se exponen en `MsalServiceException`.
- Hay un método `.WithClaim(claims)` que puede aplicarse a los generadores `AcquireTokenXXX`.

Para obtener más detalles, consulte cómo [controlar MsalUiRequiredException](msal-error-handling-dotnet.md#msaluirequiredexception).

En ADAL.NET, las excepciones en los desafíos de notificaciones se controlaron de la siguiente forma:

- `AdalClaimChallengeException` es una excepción (que se deriva de `AdalServiceException`). El miembro `Claims` contiene algún fragmento de JSON con las notificaciones, que se esperan. 
- La aplicación cliente pública que recibe esta excepción debía llamar a la invalidación `AcquireTokenInteractive` con un parámetro de notificaciones. Esta invalidación de `AcquireTokenInteractive` ni siquiera intenta llegar a la memoria caché porque no es necesario. La razón es que el token de la memoria caché no tiene las notificaciones correctas (de lo contrario, no se habría producido `AdalClaimChallengeException`). Por lo tanto, no es necesario examinar la memoria caché. Tenga en cuenta que `ClaimChallengeException` se puede recibir en una WebAPI que aplique OBO, pero `AcquireTokenInteractive` debe llamarse en una aplicación cliente pública que llama a esta API web.

Para obtener más detalles, incluidos ejemplos, consulte la sección sobre cómo [controlar AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception).


## <a name="scopes"></a>Ámbitos

ADAL usa el concepto de recursos con la cadena `resourceId`; sin embargo, MSAL.NET usa ámbitos. La lógica que usa Azure AD es la siguiente:

- Para el punto de conexión de ADAL (v1.0) con un token de acceso de la versión 1.0 (el único posible), `aud=resource`.
- Si MSAL (punto de conexión de la versión 2.0) pide un token de acceso para un recurso que acepta tokens de la versión 2.0, `aud=resource.AppId`.
- Si MSAL (punto de conexión de la versión 2.0) pide un token de acceso para un recurso que acepta tokens de la versión 1.0, Azure AD analiza la audiencia deseada desde el ámbito solicitado. Para ello, se toma todo el contenido antes de la última barra diagonal y se usa como identificador de recurso. De esta forma, si `https://database.windows.net` espera una audiencia de `https://database.windows.net/`, deberá solicitar un ámbito de `https://database.windows.net//.default` (observe la barra diagonal doble antes de ./default). Esto se ilustra en los ejemplos 1 y 2 siguientes.

### <a name="example-1"></a>Ejemplo 1

Si quiere adquirir tokens para una aplicación que acepte tokens de la versión 1.0 (por ejemplo, Microsoft Graph API, que es `https://graph.microsoft.com`), debe crear `scopes` mediante la concatenación de un identificador de recurso deseado con un permiso de OAuth2 deseado para dicho recurso.

Por ejemplo, para acceder al nombre del usuario a través de una API web v1.0 que tiene un URI de identificador de aplicación `ResourceId`, es probable que quiera usar lo siguiente:

```csharp
var scopes = new [] { ResourceId+"/user_impersonation" };
```

Si quiere leer y escribir con MSAL.NET Azure Active Directory mediante Microsoft Graph API (`https://graph.microsoft.com/`), debería crear una lista de ámbitos, como en el fragmento de código siguiente:

```csharp
string ResourceId = "https://graph.microsoft.com/"; 
string[] scopes = { ResourceId + "Directory.Read", ResourceId + "Directory.Write" }
```

### <a name="example-2"></a>Ejemplo 2

Si resourceId termina con "/", deberá tener un carácter "/" doble al escribir el valor del ámbito. Por ejemplo, si quiere escribir el ámbito correspondiente a la API de Azure Resource Manager (`https://management.core.windows.net/`), solicite el ámbito siguiente (observe las dos barras diagonales).

```csharp
var resource = "https://management.core.windows.net/"
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Esto se debe a que la API de Resource Manager espera una barra diagonal en su notificación de la audiencia (`aud`) y, después, hay una barra diagonal para separar el nombre de la API del ámbito.

Si quiere adquirir un token para todos los ámbitos estáticos de una aplicación v1.0, la lista de ámbitos se crearía como se muestra en el fragmento de código siguiente:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] { ResourceId+"/.default" };
```

Para un flujo de credenciales de cliente, el ámbito que se pasa también sería `/.default`. Este ámbito indica a Azure AD: "todos los permisos de nivel de aplicación a los que el administrador ha dado su consentimiento para el registro de la aplicación".

## <a name="next-steps"></a>Pasos siguientes

[Migración de aplicaciones de ADAL a MSAL](msal-net-migration.md)
[Migración de aplicaciones cliente confidenciales de ADAL.NET para usar MSAL.NET](msal-net-migration-confidential-client.md)
