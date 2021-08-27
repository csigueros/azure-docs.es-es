---
title: Llamada a las API web desde una aplicación de escritorio | Azure
titleSuffix: Microsoft identity platform
description: Aprenda a compilar una aplicación de escritorio que llame a las API web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9cfd26b8cca62c106cd389e4e9e33d058a46c842
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681279"
---
# <a name="desktop-app-that-calls-web-apis-call-a-web-api"></a>Aplicación de escritorio que llama a las API web: Llamada a una API de web

Ahora que dispone de un token, puede llamar a una API web protegida.

## <a name="call-a-web-api"></a>Llamada a una API de web

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

PublicClientApplication pca = PublicClientApplication.builder(clientId)
        .authority(authority)
        .build();

// Acquire a token, acquireTokenHelper would call publicClientApplication's acquireTokenSilently then acquireToken
// see https://github.com/Azure-Samples/ms-identity-java-desktop for a full example
IAuthenticationResult authenticationResult = acquireTokenHelper(pca);

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + authenticationResult.accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

# <a name="macos"></a>[macOS](#tab/macOS)

## <a name="call-a-web-api-in-msal-for-ios-and-macos"></a>Llamada a una API web en MSAL para iOS y macOS

Los métodos para adquirir tokens devuelven un objeto `MSALResult`. `MSALResult` expone una propiedad `accessToken` que se puede usar para llamar a una API web. Agregue un token de acceso al encabezado de autorización HTTP antes de realizar la llamada para acceder a la API web protegida.

Objective-C:

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };

NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

Swift:

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]

let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

## <a name="call-several-apis-incremental-consent-and-conditional-access"></a>Llamada a varias API: consentimiento incremental y acceso condicional

Para llamar a varias API para el mismo usuario después de obtener un token para la primera API, llame a `AcquireTokenSilent`. Obtendrá un token para las otras API, en modo silencioso la mayor parte de las veces.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

La interacción es necesaria en los siguientes casos:

- El usuario ha dado su consentimiento para la primera API, pero ahora debe dar su consentimiento para más ámbitos. Este tipo de consentimiento se conoce como consentimiento incremental.
- La primera API no requería autenticación multifactor, pero la siguiente sí.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Si usa un cliente HTTP como [Axios](https://www.npmjs.com/package/axios), llame al URI del punto de conexión de la API con un token de acceso como el *portador de autorización*.

```javascript
const axios = require('axios');

async function callEndpointWithToken(endpoint, accessToken) {
    const options = {
        headers: {
            Authorization: `Bearer ${accessToken}`
        }
    };

    console.log('Request made at: ' + new Date().toString());

    const response = await axios.default.get(endpoint, options);

    return response.data;
}

```

<!--
More includes will come later for Python and Java
-->
# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

---

## <a name="next-steps"></a>Pasos siguientes

Avance al siguiente artículo de este escenario, [Paso a producción](scenario-desktop-production.md).
