---
title: Adquisición de un token para llamar de forma interactiva a una API web (aplicación de escritorio) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a compilar una aplicación de escritorio que llame a API web para adquirir un token para la aplicación de forma interactiva
services: active-directory
author: maliksahil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/25/2021
ms.author: sahmalik
ms.custom: aaddev, devx-track-python, has-adal-ref
ms.openlocfilehash: 6cd9e4ff795e1051267aa20e244e03c37c15f831
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837919"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token-interactively"></a>Aplicación de escritorio que llama a las API web: adquisición de un token de forma interactiva

El ejemplo siguiente muestra un código mínimo para obtener un token de forma interactiva para leer el perfil del usuario con Microsoft Graph.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>En MSAL.NET

```csharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Parámetros obligatorios

`AcquireTokenInteractive` solo tiene un parámetro obligatorio, ``scopes``, que contiene una enumeración de cadenas que definen los ámbitos en los que se necesita un token. Si el token es para Microsoft Graph, los ámbitos necesarios se pueden encontrar en la sección denominada "Permisos" de la referencia de API de cada instancia de Microsoft Graph API. Por ejemplo, para [enumerar los contactos del usuario](/graph/api/user-list-contacts), se debe usar el ámbito "User.Read", "Contacts.Read". Para más información, consulte [Referencia de permisos de Microsoft Graph](/graph/permissions-reference).

En Android, también debe especificar la actividad principal mediante `.WithParentActivityOrWindow`, como se muestra, para que el token vuelva a esa actividad principal tras la interacción. Si no se especifica, se produce una excepción al llamar a `.ExecuteAsync()`.

### <a name="specific-optional-parameters-in-msalnet"></a>Parámetros opcionales específicos de MSAL.NET

#### <a name="withparentactivityorwindow"></a>WithParentActivityOrWindow

La interfaz de usuario es importante porque es interactiva. `AcquireTokenInteractive` tiene un parámetro opcional específico que permite determinar, en las plataformas que lo admiten, la interfaz de usuario principal. Cuando se usa en una aplicación de escritorio, `.WithParentActivityOrWindow` tiene un tipo diferente que depende de la plataforma. También, si no quiere controlar dónde aparece el cuadro de diálogo de inicio de sesión en la pantalla, puede omitir el parámetro opcional de ventana principal para crear una ventana. Esta opción sería aplicable a las aplicaciones basadas en línea de comandos que se usan para pasar llamadas a cualquier otro servicio de back-end y no necesitan ventanas para la interacción del usuario.

```csharp
// net45
WithParentActivityOrWindow(IntPtr windowPtr)
WithParentActivityOrWindow(IWin32Window window)

// Mac
WithParentActivityOrWindow(NSWindow window)

// .NET Standard (this will be on all platforms at runtime, but only on NetStandard at build time)
WithParentActivityOrWindow(object parent).
```

Comentarios:

- En .NET Standard, el elemento `object` esperado es `Activity` en Android, `UIViewController` en iOS, `NSWindow` en Mac y `IWin32Window` o `IntPr` en Windows.
- En Windows, debe llamar a `AcquireTokenInteractive` desde el subproceso de interfaz de usuario para que el explorador integrado obtenga el contexto de sincronización de la interfaz de usuario adecuado. Si no se llama desde el subproceso de interfaz de usuario, es posible que los mensajes no se suministren correctamente o que se produzcan escenarios de interbloqueo con la interfaz de usuario. Una manera de llamar a bibliotecas de autenticación de Microsoft (MSAL) desde el subproceso de interfaz de usuario (si aún no está en él) es usar `Dispatcher` en WPF.
- Si usa WPF, para obtener una ventana de un control WPF puede usar la clase `WindowInteropHelper.Handle`. Así, la llamada procede de un control WPF (`this`):

  ```csharp
  result = await app.AcquireTokenInteractive(scopes)
                    .WithParentActivityOrWindow(new WindowInteropHelper(this).Handle)
                    .ExecuteAsync();
  ```

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` se usa para controlar la interactividad con el usuario mediante la especificación de un símbolo del sistema. El comportamiento exacto se puede controlar mediante la estructura [Microsoft.Identity.Client.Prompt](/dotnet/api/microsoft.identity.client.prompt).

La estructura define las constantes siguientes:

- `SelectAccount` obliga a STS a presentar el cuadro de diálogo de selección de cuenta que contiene las cuentas para las que el usuario tiene una sesión. Esta opción es útil cuando los desarrolladores de aplicaciones quieren permitir a los usuarios elegir entre diferentes identidades. Esta opción dirige a MSAL para que envíe `prompt=select_account` al proveedor de identidades. Es la opción predeterminada. Funciona bien para proporcionar la mejor experiencia posible en función de la información disponible, como cuenta y presencia de una sesión para el usuario. No la cambie a menos que tenga buena razón para hacerlo.
- `Consent` permite que el desarrollador de la aplicación obligue al usuario a pedir consentimiento, aunque ya lo haya otorgado antes. En este caso, MSAL envía `prompt=consent` al proveedor de identidades. Esta opción se puede usar en algunas aplicaciones centradas en la seguridad donde la gobernanza de la organización exige que se presente al usuario el cuadro de diálogo de consentimiento cada vez que se usa la aplicación.
- `ForceLogin` permite que el desarrollador de la aplicación obligue al servicio a solicitar las credenciales al usuario, incluso si este mensaje de usuario no es necesario. Esta opción puede ser útil para permitir que el usuario vuelva a iniciar sesión si se produce un error al adquirir un token. En este caso, MSAL envía `prompt=login` al proveedor de identidades. A veces se usa en aplicaciones centradas en la seguridad donde la gobernanza de la organización exige que el usuario vuelva a iniciar sesión cada vez que accede a determinadas partes de una aplicación.
- `Create` desencadena una experiencia de inicio de sesión, que se usa con External Identities, mediante el envío de `prompt=create` al proveedor de identidades. Este aviso no se debe enviar en el caso de aplicaciones de Azure AD B2C. Para más información, consulte [Incorporación de un flujo de usuario de registro de autoservicio a una aplicación](../external-identities/self-service-sign-up-user-flow.md).
- `Never` (solo para .NET 4.5 y WinRT) no pregunta al usuario, sino que, en su lugar, intenta usar la cookie almacenada en la vista web insertada oculta. Para más información, consulte las vistas web de MSAL.NET. Se puede producir un error al usar esta opción. En ese caso, `AcquireTokenInteractive` produce una excepción para notificar que se necesita una interacción de la interfaz de usuario. Debe usar otro parámetro `Prompt`.
- `NoPrompt` no enviará ningún aviso al proveedor de identidades que, por lo tanto, decide presentar la mejor experiencia de inicio de sesión al usuario (inicio de sesión único o seleccionar cuenta). Esta opción también es obligatoria para las directivas de edición de perfiles de Azure Active Directory (Azure AD) B2C. Para más información, consulte [Información específica sobre Azure AD B2C](https://aka.ms/msal-net-b2c-specificities).

#### <a name="withuseembeddedwebview"></a>WithUseEmbeddedWebView

Este método le permite especificar si quiere forzar el uso de una vista web insertada o una vista web del sistema (si está disponible). Para más información, consulte [Uso de exploradores web](msal-net-web-browsers.md).

```csharp
var result = await app.AcquireTokenInteractive(scopes)
                    .WithUseEmbeddedWebView(true)
                    .ExecuteAsync();
```

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Este modificador se usa en un escenario avanzado en el que se quiere que el usuario otorgue su consentimiento por adelantado a varios recursos y no se quiere emplear el consentimiento incremental, que normalmente se usa con MSAL.NET o la plataforma de identidad de Microsoft. Para más información, consulte [Cómo tener el consentimiento del usuario por adelantado para varios recursos](scenario-desktop-production.md#have-the-user-consent-upfront-for-several-resources).

```csharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="withcustomwebui"></a>WithCustomWebUi

Una interfaz de usuario web es un mecanismo para invocar un explorador. Este mecanismo puede ser un control WebBrowser de interfaz de usuario dedicado o una forma de delegar la apertura del explorador.
MSAL proporciona implementaciones de la interfaz de usuario web para la mayoría de las plataformas, aunque hay casos en los que es posible que quiera hospedar el explorador por su cuenta:

- Plataformas no admitidas explícitamente por MSAL, por ejemplo, Blazor, Unity o Mono en equipos de escritorio.
- Quiere probar la aplicación en la interfaz de usuario y usar un explorador automatizado que se pueda emplear con Selenium.
- El explorador y la aplicación que ejecutan MSAL están en procesos independientes.

##### <a name="at-a-glance"></a>En un vistazo

Para ello, debe proporcionar a MSAL el elemento `start Url`, que debe mostrarse en el explorador seleccionado para que el usuario final pueda escribir elementos como su nombre de usuario.
Una vez terminada la autenticación, la aplicación debe pasar a MSAL el elemento `end Url`, que contiene un código proporcionado por Azure AD.
El host de `end Url` siempre es `redirectUri`. Para interceptar `end Url`, realice una de las siguientes acciones:

- Supervise los redireccionamientos del explorador hasta que se alcance `redirect Url`.
- Haga que el explorador redirija a una dirección URL que supervise.

##### <a name="withcustomwebui-is-an-extensibility-point"></a>WithCustomWebUi es un punto de extensibilidad.

`WithCustomWebUi` es un punto de extensibilidad que se puede usar para proporcionar una interfaz de usuario propia en aplicaciones cliente públicas. También puede permitir que el usuario pase por el punto de conexión /Authorize del proveedor de identidades y dejarle iniciar sesión y dar su consentimiento. Después, MSAL.NET puede canjear el código de autenticación y obtener un token. Por ejemplo, se usa en Visual Studio para que las aplicaciones de Electron (por ejemplo, Visual Studio Feedback) proporcionen la interacción web, pero se deja que MSAL.NET realice la mayor parte del trabajo. También puede usarlo si quiere proporcionar automatización de la interfaz de usuario. En las aplicaciones cliente públicas, MSAL.NET usa la norma Proof Key for Code Exchange (PKCE) para garantizar que se respeta la seguridad. Solo MSAL.NET puede canjear el código. Para más información, consulte [RFC 7636 - Proof Key for Code Exchange por parte de clientes públicos de OAuth](https://tools.ietf.org/html/rfc7636).

  ```csharp
  using Microsoft.Identity.Client.Extensions;
  ```

##### <a name="use-withcustomwebui"></a>Uso de WithCustomWebUi

Para usar `.WithCustomWebUI`, siga estos pasos.

  1. Implemente la interfaz `ICustomWebUi`. Para más información, consulte [este sitio web](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/src/Microsoft.Identity.Client/Extensibility/ICustomWebUI.cs#L32-L70). Implemente un método `AcquireAuthorizationCodeAsync` y acepte la dirección URL de código de autorización procesada por MSAL.NET. Luego, permita que el usuario lleve a cabo la interacción con el proveedor de identidades y devuelva la dirección URL con la que este hubiera llamado a la implementación junto con el código de autorización. Si tiene problemas, la implementación debería lanzar una excepción `MsalExtensionException` para cooperar bien con MSAL.
  2. En la llamada a `AcquireTokenInteractive`, use el modificador `.WithCustomUI()` que pasa la instancia de la interfaz de usuario web personalizada.

     ```csharp
     result = await app.AcquireTokenInteractive(scopes)
                       .WithCustomWebUi(yourCustomWebUI)
                       .ExecuteAsync();
     ```

##### <a name="examples-of-implementation-of-icustomwebui-in-test-automation-seleniumwebui"></a>Ejemplos de implementación de ICustomWebUi en automatización de prueba: SeleniumWebUI

El equipo de MSAL.NET ha vuelto a escribir las pruebas de interfaz de usuario para usar este mecanismo de extensibilidad. Si le interesa, vea la clase [SeleniumWebUI](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/blob/053a98d16596be7e9ca1ab916924e5736e341fe8/tests/Microsoft.Identity.Test.Integration/Infrastructure/SeleniumWebUI.cs#L15-L160) en el código fuente de MSAL.NET.

##### <a name="provide-a-great-experience-with-systemwebviewoptions"></a>Una gran experiencia con SystemWebViewOptions

En [`SystemWebViewOptions`](/dotnet/api/microsoft.identity.client.systemwebviewoptions) de MSAL.NET 4.1, puede especificar:

- El URI al que se va a ir (`BrowserRedirectError`) o el fragmento HTML que se va a mostrar (`HtmlMessageError`) en caso de errores de inicio de sesión o consentimiento en el explorador web del sistema.
- El URI al que se va a ir (`BrowserRedirectSuccess`) o el fragmento HTML que se va a mostrar (`HtmlMessageSuccess`) en caso de inicio de sesión o consentimiento correctos.
- La acción que se va a ejecutar para iniciar el explorador del sistema. Puede proporcionar una implementación propia si establece el delegado `OpenBrowserAsync`. La clase también proporciona una implementación predeterminada para dos exploradores: `OpenWithEdgeBrowserAsync` y `OpenWithChromeEdgeBrowserAsync` para Microsoft Edge y [Microsoft Edge en Chromium](https://www.windowscentral.com/faq-edge-chromium), respectivamente.

Para usar esta estructura, escriba algo como esto:

```csharp
IPublicClientApplication app;
...

options = new SystemWebViewOptions
{
 HtmlMessageError = "<b>Sign-in failed. You can close this tab ...</b>",
 BrowserRedirectSuccess = "https://contoso.com/help-for-my-awesome-commandline-tool.html"
};

var result = app.AcquireTokenInteractive(scopes)
                .WithEmbeddedWebView(false)       // The default in .NET Core
                .WithSystemWebViewOptions(options)
                .Build();
```

#### <a name="other-optional-parameters"></a>Otros parámetros opcionales

Para más información sobre los demás parámetros opcionales de `AcquireTokenInteractive`, consulte [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder#methods).

# <a name="java"></a>[Java](#tab/java)

```java
private static IAuthenticationResult acquireTokenInteractive() throws Exception {

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

            InteractiveRequestParameters parameters = InteractiveRequestParameters
                    .builder(new URI("http://localhost"))
                    .scopes(SCOPE)
                    .build();

            // Try to acquire a token interactively with system browser. If successful, you should see
            // the token and account information printed out to console
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

### <a name="in-msal-for-ios-and-macos"></a>En MSAL para iOS y macOS

Objective-C:

```objc
MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopes webviewParameters:[MSALWebviewParameters new]];
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) {
    if (!error)
    {
        // You'll want to get the account identifier to retrieve and reuse the account
        // for later acquireToken calls
        NSString *accountIdentifier = result.account.identifier;

        NSString *accessToken = result.accessToken;
    }
}];
```

Swift:

```swift
let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopes, webviewParameters: MSALWebviewParameters())
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in

    guard let authResult = result, error == nil else {
        print(error!.localizedDescription)
        return
    }

    // Get access token from result
    let accessToken = authResult.accessToken
})
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

En el nodo MSAL, los tokens se adquieren a través del flujo de código de autorización con la clave de prueba para el intercambio de código (PKCE). El proceso consta de dos pasos: en primer lugar, la aplicación obtiene una dirección URL que se puede usar para generar un código de autorización. Esta dirección URL se puede abrir en el explorador que el usuario prefiera, donde puede escribir sus credenciales para acceder posteriormente a `redirectUri` (registrado durante el registro de la aplicación) con un código de autorización. En segundo lugar, la aplicación pasa el código de autorización recibido al método `acquireTokenByCode()` que lo intercambia por un token de acceso.

```javascript
const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);

const {verifier, challenge} = await msal.cryptoProvider.generatePkceCodes();

const authCodeUrlParameters = {
    scopes: ["User.Read"],
    redirectUri: "your_redirect_uri",
    codeChallenge: challenge, // PKCE Code Challenge
    codeChallengeMethod: "S256" // PKCE Code Challenge Method 
};

// get url to sign user in and consent to scopes needed for application
pca.getAuthCodeUrl(authCodeUrlParameters).then((response) => {
    console.log(response);

    const tokenRequest = {
        code: response["authorization_code"],
        codeVerifier: verifier // PKCE Code Verifier 
        redirectUri: "your_redirect_uri",
        scopes: ["User.Read"],
    };

    // acquire a token by exchanging the code
    pca.acquireTokenByCode(tokenRequest).then((response) => {
        console.log("\nResponse: \n:", response);
    }).catch((error) => {
        console.log(error);
    });
}).catch((error) => console.log(JSON.stringify(error)));
```

# <a name="python"></a>[Python](#tab/python)

MSAL Python 1.7+ proporciona un método interactivo de adquisición de tokens.

```python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_interactive(  # It automatically provides PKCE protection
         scopes=config["scope"])
```

---
### <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo de este escenario, [Llamada a una API web desde la aplicación de escritorio](scenario-desktop-call-api.md).
