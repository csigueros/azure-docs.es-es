---
title: Adquisición de un token para llamar a una API web (aplicación de escritorio) | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a compilar una aplicación de escritorio que llame a API web para adquirir un token para la aplicación.
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
ms.openlocfilehash: 0e23951d0d38f2ea8ddfba44a5a2afc7590e2ba2
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234307"
---
# <a name="desktop-app-that-calls-web-apis-acquire-a-token"></a>Aplicación de escritorio que llama a API web: Adquisición de un token

Una vez que se ha compilado una instancia de la aplicación cliente pública, se usará para adquirir un token que, a su vez, se emplea para llamar a una API web.

## <a name="recommended-pattern"></a>Patrón recomendado

La API web se define mediante sus `scopes`. Sea cual sea la experiencia que proporcione en la aplicación, el patrón que debe usar es:

- Intente obtener de forma sistemática un token de la caché de tokens mediante una llamada a `AcquireTokenSilent`.
- Si se produce un error en esta llamada, use el flujo `AcquireToken` que quiera emplear, que aquí está representado por `AcquireTokenXX`.

# <a name="net"></a>[.NET](#tab/dotnet)

### <a name="in-msalnet"></a>En MSAL.NET

```csharp
AuthenticationResult result;
var accounts = await app.GetAccountsAsync();
IAccount account = ChooseAccount(accounts); // for instance accounts.FirstOrDefault
                                            // if the app manages is at most one account
try
{
 result = await app.AcquireTokenSilent(scopes, account)
                   .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
  result = await app.AcquireTokenXX(scopes, account)
                    .WithOptionalParameterXXX(parameter)
                    .ExecuteAsync();
}
```

# <a name="java"></a>[Java](#tab/java)

```java
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
```

# <a name="macos"></a>[macOS](#tab/macOS)

### <a name="in-msal-for-ios-and-macos"></a>En MSAL para iOS y macOS

Objective-C:

```objc
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:nil];

MSALSilentTokenParameters *silentParams = [[MSALSilentTokenParameters alloc] initWithScopes:scopes account:account];
[application acquireTokenSilentWithParameters:silentParams completionBlock:^(MSALResult *result, NSError *error) {

    // Check the error
    if (error && [error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
    {
        // Interactive auth will be required, call acquireTokenWithParameters:error:
    }
}];
```

Swift:

```swift
guard let account = try? application.account(forIdentifier: accountIdentifier) else { return }
let silentParameters = MSALSilentTokenParameters(scopes: scopes, account: account)
application.acquireTokenSilent(with: silentParameters) { (result, error) in

    guard let authResult = result, error == nil else {

    let nsError = error! as NSError

        if (nsError.domain == MSALErrorDomain &&
            nsError.code == MSALError.interactionRequired.rawValue) {

            // Interactive auth will be required, call acquireToken()
            return
        }
        return
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

En el nodo MSAL, los tokens se adquieren a través del flujo de código de autorización con la clave de prueba para el intercambio de código (PKCE). El nodo MSAL usa una caché de token en memoria para ver si hay alguna cuenta de usuario en la memoria caché. Si la hay, el objeto de cuenta se puede pasar al método `acquireTokenSilent()` para recuperar un token de acceso almacenado en caché.

```javascript

const msal = require("@azure/msal-node");

const msalConfig = {
    auth: {
        clientId: "your_client_id_here",
        authority: "your_authority_here",
    }
};

const pca = new msal.PublicClientApplication(msalConfig);
const msalTokenCache = pca.getTokenCache();

let accounts = await msalTokenCache.getAllAccounts();

    if (accounts.length > 0) {

        const silentRequest = {
            account: accounts[0], // Index must match the account that is trying to acquire token silently
            scopes: ["user.read"],
        };

        pca.acquireTokenSilent(silentRequest).then((response) => {
            console.log("\nSuccessful silent token acquisition");
            console.log("\nResponse: \n:", response);
            res.sendStatus(200);
        }).catch((error) => console.log(error));
    } else {
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
    }
```

# <a name="python"></a>[Python](#tab/python)

```python
result = None

# Firstly, check the cache to see if this end user has signed in before
accounts = app.get_accounts(username=config["username"])
if accounts:
    result = app.acquire_token_silent(config["scope"], account=accounts[0])

if not result:
    result = app.acquire_token_by_xxx(scopes=config["scope"])
```

---

Estas son las distintas formas de adquirir tokens en una aplicación de escritorio.

- [interactivamente,](scenario-desktop-acquire-token-interactive.md)
- [Autenticación integrada de Windows](scenario-desktop-acquire-token-integrated-windows-authentication.md)
- [WAM](scenario-desktop-acquire-token-wam.md)
- [Nombre de usuario/contraseña](scenario-desktop-acquire-token-username-password.md)
- [Flujo de código de dispositivo](scenario-desktop-acquire-token-device-code-flow.md)

---
## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo de este escenario, [Llamada a una API web desde la aplicación de escritorio](scenario-desktop-call-api.md).
