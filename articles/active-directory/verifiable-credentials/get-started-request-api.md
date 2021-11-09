---
title: Cómo llamar a la API REST de servicios de solicitudes (versión preliminar)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Aprenda a emitir y comprobar mediante la API de REST de solicitud de servicio
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 805ba2fc9a24536a940e4baf02e2934d26c10d75
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474029"
---
# <a name="request-service-rest-api-preview"></a>API REST de servicios de solicitudes (versión preliminar)

Las credenciales verificables de Azure Active Directory (Azure AD) incluyen la API de REST de solicitud de servicio. Esta API le permite emitir y comprobar credenciales. En este artículo se muestra cómo empezar a usar la API de REST del servicio de solicitudes.

> [!IMPORTANT]
> La API de REST de solicitud de servicio está actualmente en versión preliminar. Esta versión preliminar se proporciona sin ningún contrato de nivel de servicio, por lo que es posible que se produzcan cambios importantes o se deje de usar la API mientras está en versión preliminar. No se recomienda la versión preliminar de la API para cargas de trabajo de producción. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="api-access-token"></a>Token de acceso a la API

Para que la aplicación acceda a la API de REST del servicio de solicitudes, debe incluir un token de acceso válido con los permisos necesarios. Los tokens de acceso emitidos por la Plataforma de identidad de Microsoft contienen información (ámbitos) que la API de REST del servicio de solicitudes usa para validar al autor de la llamada. Un token de acceso garantiza que el autor de llamada tenga los permisos adecuados para realizar la operación que solicita.

Para obtener un token de acceso, la aplicación debe estar registrada en Plataforma de identidad de Microsoft y tener la autorización de un administrador para acceder a la API de REST de solicitud de servicio. Si no ha registrado la aplicación *verifiable-credentials-app*, vea [cómo registrar la aplicación](verifiable-credentials-configure-tenant.md#register-an-application-in-azure-ad) y luego [genere un secreto de aplicación](verifiable-credentials-configure-issuer.md#configure-the-verifiable-credentials-app).

### <a name="get-an-access-token"></a>Obtención de un token de acceso

Use el [flujo de concesión de credenciales de cliente de OAuth 2.0](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) para adquirir el token de acceso mediante Plataforma de identidad de Microsoft. Use una biblioteca de confianza para este fin. En este tutorial, usamos la biblioteca de autenticación de Microsoft [(MSAL)](../../active-directory/develop/msal-overview.md). MSAL simplifica la incorporación de autenticación y autorización a una aplicación que puede llamar a una API web segura.

# <a name="http"></a>[HTTP](#tab/http)

```http
Refer to to the Microsoft Authentication Library (MSAL) documentation for more information on how to acquire tokens via HTTP.
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
    .WithClientSecret(AppSettings.ClientSecret)
    .WithAuthority(new Uri(AppSettings.Authority))
    .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientSecret: config.azClientSecret,
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

En el código anterior, proporcione los parámetros siguientes:

| Parámetro | Condición | Descripción |
| --- | --- | --- |
| Autoridad | Requerido | Inquilino del directorio en el que la aplicación tiene previsto funcionar. Por ejemplo: `https://login.microsoftonline.com/{your-tenant}`. (Remplace `your-tenant` por el [identificador o nombre de inquilino](../fundamentals/active-directory-how-to-find-tenant.md)). |
| Id. de cliente | Obligatorio | El identificador de la aplicación que está asignado a la aplicación. Puede encontrar esta información en Azure Portal, donde ha registrado la aplicación. |
| Secreto del cliente | Requerido | Secreto de cliente que ha generado para la aplicación.|
| Ámbitos | Obligatorio | Se debe establecer en `bbb94529-53a3-4be5-a069-7eaf2712b826/.default`. |

Para obtener más información sobre cómo obtener un token de acceso mediante la identidad de una aplicación de consola, vea alguno de los artículos siguientes: [C#](../develop/quickstart-v2-netcore-daemon.md), [Python](../develop/quickstart-v2-python-daemon.md), [Node.js](../develop/quickstart-v2-nodejs-console.md) o [Java](../develop/quickstart-v2-java-daemon.md).

También puede [acceder a una solicitud de token con un certificado](../develop/v2-oauth2-client-creds-grant-flow.md) en lugar de un secreto de cliente.

# <a name="http"></a>[HTTP](#tab/http)

```http
POST /{tenant}/oauth2/v2.0/token HTTP/1.1   //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=12345678-0000-0000-00000000000000000
&scope=bbb94529-53a3-4be5-a069-7eaf2712b826/.default
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
// Initialize MSAL library by using the following code
X509Certificate2 certificate = AppSettings.ReadCertificate(AppSettings.CertificateName);
    app = ConfidentialClientApplicationBuilder.Create(AppSettings.ClientId)
        .WithCertificate(certificate)
        .WithAuthority(new Uri(AppSettings.Authority))
        .Build();

// Acquire an access token
result = await app.AcquireTokenForClient(AppSettings.Scopes)
                .ExecuteAsync();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
// Initialize MSAL library by using the following code
const msalConfig = {
  auth: {
      clientId: config.azClientId,
      authority: `https://login.microsoftonline.com/${config.azTenantId}`,
      clientCertificate: {
            thumbprint: "CERT_THUMBPRINT", // a 40-digit hexadecimal string
            privateKey: "CERT_PRIVATE_KEY"
        }
  },
  system: {
      loggerOptions: {
          loggerCallback(loglevel, message, containsPii) {
              console.log(message);
          },
          piiLoggingEnabled: false,
          logLevel: msal.LogLevel.Verbose,
      }
  }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
const msalClientCredentialRequest = {
  scopes: ["bbb94529-53a3-4be5-a069-7eaf2712b826/.default"],
  skipCache: false, 
};
module.exports.msalCca = cca;
module.exports.msalClientCredentialRequest = msalClientCredentialRequest;

// Acquire an access token
const result = await mainApp.msalCca.acquireTokenByClientCredential(mainApp.msalClientCredentialRequest);
    if ( result ) {
      accessToken = result.accessToken;
    }
```

---

## <a name="call-the-api"></a>Llamada a la API

Para emitir o comprobar una credencial verificable, siga estos pasos:

1. Cree una solicitud HTTP POST para la API de REST del servicio de solicitudes. Reemplace `{tenantID}` por el identificador o el nombre del inquilino.

    ```http
    POST https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
    ```

1. Adjunte el token de acceso como token de portador al encabezado de autorización de una solicitud HTTP.

    ```http
    Authorization: Bearer <token>
    ```

1. Establezca el encabezado `Content-Type` en `Application/json`.

1. Prepare y adjunte la carga de la solicitud de [emisión](issuance-request-api.md#issuance-request-payload) o [presentación](presentation-request-api.md#presentation-request-payload) al cuerpo de la solicitud.

1. Envíe la solicitud a la API de REST del servicio de solicitudes.

## <a name="issuance-request-example"></a>Ejemplo de solicitud de emisión

En el siguiente ejemplo se muestra una solicitud de emisión de credenciales verificables. Para obtener más información sobre la carga, vea [Especificación de la emisión de la API de REST Request Service](issuance-request-api.md).

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
    "includeQRCode": true,
    "callback": {
        "url": "https://www.contoso.com/api/issuer/issuanceCallback",
        "state": "de19cb6b-36c1-45fe-9409-909a51292a9c",
        "headers": {
            "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
        }
    },
    "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDek...",
    "registration": {
        "clientName": "Verifiable Credential Expert Sample"
    },
    "issuance": {
        "type": "VerifiedCredentialExpert",
        "manifest": "https://beta.did.msidentity.com/v1.0/12345678-0000-0000-0000-000000000000/verifiableCredential/contracts/VerifiedCredentialExpert1",
        "pin": {
            "value": "3539",
            "length": 4
        },
        "claims": {
            "given_name": "Megan",
            "family_name": "Bowen"
        }
    }
}
```  

Para obtener el código completo, vea alguno de los siguientes ejemplos de código:

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/IssuerController.cs)
- [Node.js](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js)

## <a name="presentation-request-example"></a>Ejemplo de solicitud de presentación

En el siguiente ejemplo se muestra una solicitud de presentación de credenciales verificables. Para obtener más información sobre la carga, vea [Especificación de presentación de la API de REST del servicio de solicitudes](presentation-request-api.md).

```http
POST https://beta.did.msidentity.com/v1.0/contoso.onmicrosoft.com/verifiablecredentials/request
Content-Type: application/json
Authorization: Bearer  <token>

{
  "includeQRCode": true,
  "callback": {
    "url": "https://www.contoso.com/api/verifier/presentationCallback",
    "state": "92d076dd-450a-4247-aa5b-d2e75a1a5d58",
    "headers": {
      "api-key": "OPTIONAL API-KEY for VERIFIER CALLBACK API"
    }
  },
  "authority": "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiOiJiRWo5MDY...",
  "registration": {
    "clientName": "Veritable Credential Expert Verifier"
  },
  "presentation": {
    "includeReceipt": true,
    "requestedCredentials": [
      {
        "type": "VerifiedCredentialExpert",
        "purpose": "So we can see that you a veritable credentials expert",
        "acceptedIssuers": [
          "did:ion:EiCLL8lzCqlGLYTGbjwgR6SN6OkIjO6uUKyF5zM7fQZ8Jg:eyJkZWx0YSI6eyJwYXRjaGVzIjpbeyJhY3Rpb24iOiJyZXBsYWNlIiwiZG9jdW1lbnQiOnsicHVibGljS2V5cyI6W3siaWQiOiJzaWdfOTAyZmM2NmUiLCJwdWJsaWNLZXlKd2siOnsiY3J2Ijoic2VjcDI1NmsxIiwia3R5IjoiRUMiLCJ4IjoiTEdUOWk3aFYzN1dUcFhHcUg5c1VDekxTVlFWcVl3S2JNY1Fsc0RILUZJUSIsInkiO..."
        ]
      }
    ]
  }
}
```

Para obtener el código completo, vea alguno de los siguientes ejemplos de código:

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/VerifierController.cs) 
- [Node.js](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js)

## <a name="callback-events"></a>Eventos de devolución de llamada

La carga de la solicitud contiene el punto de conexión de devolución de llamada de [emisión](issuance-request-api.md#callback-events) y de [presentación](presentation-request-api.md#callback-events). El punto de conexión forma parte de la aplicación web y debe estar disponible públicamente. Las credenciales verificables de Azure AD llaman al punto de conexión para avisar a la aplicación de determinados eventos. Por ejemplo, estos eventos pueden ser que un usuario digitalice el código QR, use el vínculo profundo de la aplicación de autenticación o finalice el proceso de presentación.

En el siguiente diagrama se describen la llamada que realiza su aplicación a la API de REST del servicio de solicitudes y las devoluciones de llamada a la aplicación.

![Diagrama que describe la llamada a la API y los eventos de devolución de llamada.](media/get-started-request-api/callback-events.png)

Configure el punto de conexión para escuchar las solicitudes HTTP POST entrantes. En el siguiente fragmento de código se muestra cómo controlar la solicitud HTTP de devolución de llamada de emisión y cómo actualizar la interfaz de usuario en consecuencia:

# <a name="http"></a>[HTTP](#tab/http)

No aplicable. Elija alguno de los demás lenguajes de programación.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[HttpPost]
public async Task<ActionResult> IssuanceCallback()
{
try
{
    string content = new System.IO.StreamReader(this.Request.Body).ReadToEndAsync().Result;
    _log.LogTrace("callback!: " + content);
    JObject issuanceResponse = JObject.Parse(content);
    
    // More code here
    if (issuanceResponse["code"].ToString() == "request_retrieved")
    {
        var cacheData = new
        {
            status = "request_retrieved",
            message = "QR Code is scanned. Waiting for issuance...",
        };
        _cache.Set(state, JsonConvert.SerializeObject(cacheData));

        // More code here
    }
}
```

Para obtener el código completo, consulte el código de [emisión](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/IssuerController.cs) y de [presentación](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/VerifierController.cs) en el repositorio GitHub.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```nodejs
mainApp.app.post('/api/issuer/issuance-request-callback', parser, async (req, res) => {
  var body = '';
  req.on('data', function (data) {
    body += data;
  });
  req.on('end', function () {
    requestTrace( req );
    console.log( body );
    var issuanceResponse = JSON.parse(body.toString());
    var message = null;
    
    if ( issuanceResponse.code == "request_retrieved" ) {
      message = "QR Code is scanned. Waiting for issuance to complete...";
    }
    if ( issuanceResponse.code == "issuance_successful" ) {
      message = "Credential successfully issued";
    }
    if ( issuanceResponse.code == "issuance_error" ) {
      message = issuanceResponse.error.message;
    }
    
    // More code here
    res.send()
  });  
  res.send()
})
```

Para obtener el código completo, consulte el código de [emisión](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js) y de [presentación](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js) en el repositorio GitHub.

---

## <a name="next-steps"></a>Pasos siguientes

Más información sobre estas especificaciones:

- [Especificación de API de emisión](issuance-request-api.md)
- [Especificación de API de presentación](presentation-request-api.md)
