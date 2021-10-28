---
title: Cómo llamar a la API REST de servicios de solicitudes (versión preliminar)
titleSuffix: Azure Active Directory Verifiable Credentials
description: Obtenga información sobre cómo emitir y comprobar credenciales mediante la API de REST del servicio de solicitudes.
documentationCenter: ''
author: barclayn
manager: karenh444
ms.service: active-directory
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 9b701589f7ee28cd3da8d4c028750459c53bb793
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130216927"
---
# <a name="request-service-rest-api-preview"></a>API REST de servicios de solicitudes (versión preliminar)

La API de REST del servicio de solicitudes de Azure Active Directory permite emitir y comprobar credenciales verificables mediante el servicio de credenciales verificables de Azure AD. En este artículo se muestra cómo empezar a usar la API de REST del servicio de solicitudes.

> [!IMPORTANT]
> Esta API está actualmente en versión preliminar pública (beta).
> Esta versión preliminar se proporciona sin un contrato de nivel de servicio, por lo que ocasionalmente es posible que se produzcan cambios importantes o se deje de usar la API. Mientras esté en versión preliminar, no se recomienda la API para cargas de trabajo de producción. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="api-access-token"></a>Token de acceso a la API

Para que la aplicación acceda a la API de REST del servicio de solicitudes, debe incluir un token de acceso válido con los permisos necesarios. Los tokens de acceso emitidos por la Plataforma de identidad de Microsoft contienen información (ámbitos) que la API de REST del servicio de solicitudes usa para validar al autor de la llamada. Asegúrese de que el autor de la llamada tenga los permisos adecuados para realizar la operación que solicita.

Para obtener un token de acceso, la aplicación debe estar registrada en la Plataforma de identidad de Microsoft y tener la autorización de un administrador para acceder a la API de REST del servicio de solicitudes. Si no ha registrado la aplicación *verifiable-credentials-app*, siga los pasos para [registrarla](verifiable-credentials-configure-tenant.md#step-3-register-an-application-in-azure-ad) y, a continuación, [genere un secreto de aplicación](verifiable-credentials-configure-issuer.md#configure-the-verifiable-credentials-app).

### <a name="get-an-access-token"></a>Obtención de un token de acceso

Use el [flujo de concesión de credenciales de cliente de OAuth 2.0](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md) para adquirir el token de acceso mediante la Plataforma de identidad de Microsoft. Se recomienda usar una biblioteca OAuth de confianza. En este tutorial, usamos la biblioteca de autenticación de Microsoft [(MSAL)](../../active-directory/develop/msal-overview.md). MSAL es una biblioteca proporcionada por Microsoft que simplifica el proceso de agregar autenticación y autorización a su aplicación para llamar a una API web segura.

# <a name="http"></a>[HTTP](#tab/http)

```http
Pleaes refer to to the Microsoft Authentication Library (MSAL) documentation for more information on how to acquire tokens via HTTP.
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

En el código anterior, proporcione los siguientes parámetros:

| Parámetro | Condición | Descripción |
| --- | --- | --- |
| Autoridad | Requerido | Inquilino del directorio en el que la aplicación tiene previsto funcionar. Por ejemplo, `https://login.microsoftonline.com/{your-tenant}`, reemplace `your-tenant` por el [Id. o nombre de su inquilino](../fundamentals/active-directory-how-to-find-tenant.md). |
| Id. de cliente | Obligatorio | El identificador de la aplicación que está asignado a la aplicación. Puede encontrar esta información en el portal de Azure donde haya registrado la aplicación. |
| Secreto del cliente | Requerido | Secreto de cliente que ha generado para la aplicación.|
| Ámbitos | Obligatorio | Se debe establecer en `bbb94529-53a3-4be5-a069-7eaf2712b826/.default`. |


Si desea más información sobre cómo obtener un token de acceso usando la identidad de una aplicación de consola, consulte uno de los artículos con relación a [C#](../develop/quickstart-v2-netcore-daemon.md), [Python](../develop/quickstart-v2-python-daemon.md) ,[Node.js](../develop/quickstart-v2-nodejs-console.md) o [Java](../develop/quickstart-v2-java-daemon.md).

También puede enviar una [solicitud de token de acceso con un certificado](../develop/v2-oauth2-client-creds-grant-flow.md) en lugar de un secreto de cliente.

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

1. Cree una solicitud HTTP POST para la API de REST del servicio de solicitudes. Reemplace `{tenantID}` por el **Id.** o el nombre de su inquilino.

    ```http
    POST https://beta.did.msidentity.com/v1.0/{tenantID}/verifiablecredentials/request
    ```

1. Adjunte el token de acceso como token de portador al encabezado "Authorization" en una solicitud HTTP.

    ```http
    Authorization: Bearer <token>
    ```

1. Establezca el encabezado `Content-Type` en `Application/json`.

1. Prepare y adjunte la carga de [emisión](issuance-request-api.md#issuance-request-payload) o [presentación](presentation-request-api.md#presentation-request-payload) al cuerpo de la solicitud.

1. Envíe la solicitud a la API de REST del servicio de solicitudes.

## <a name="issuance-request-example"></a>Ejemplo de solicitud de emisión

En el siguiente ejemplo se muestra una solicitud de emisión de credenciales verificables. Para obtener más información sobre la carga, consulte [Especificación de emisión de la API de REST del servicio de solicitudes](issuance-request-api.md).

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

Para obtener el código completo, consulte los ejemplos de código de [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/AspNetCoreVerifiableCredentials/IssuerController.cs) y [Node.js](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/issuer.js).

## <a name="presentation-request-example"></a>Ejemplo de solicitud de presentación

En el siguiente ejemplo se muestra una solicitud de presentación de credenciales verificables. Para obtener más información sobre la carga, consulte [Especificación de presentación de la API de REST del servicio de solicitudes](presentation-request-api.md).

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

Para obtener el código completo, consulte uno de los siguientes ejemplos de código:

- [C#](https://github.com/Azure-Samples/active-directory-verifiable-credentials-dotnet/blob/main/1-asp-net-core-api-idtokenhint/VerifierController.cs) 
- [Node.js](https://github.com/Azure-Samples/active-directory-verifiable-credentials-node/blob/main/1-node-api-idtokenhint/verifier.js).

## <a name="callback-events"></a>Eventos de devolución de llamada

La carga de la solicitud contiene el punto de conexión de devolución de llamada de [emisión](issuance-request-api.md#callback-events) y de [presentación](presentation-request-api.md#callback-events). El punto de conexión forma parte de la aplicación web y debe estar disponible públicamente. El servicio de credenciales verificables de Azure AD llama a su punto de conexión para informar a la aplicación sobre determinados eventos. Por ejemplo, cuando un usuario digitaliza el código QR, usa el vínculo profundo de su aplicación autenticadora o finaliza el proceso de presentación.

En el siguiente diagrama se describen la llamada que realiza su aplicación a la API de REST del servicio de solicitudes y las devoluciones de llamada a la aplicación.

![En el diagrama se describen la llamada a la API y los eventos de devolución de llamada.](media/get-started-request-api/callback-events.png)

Configure el punto de conexión para escuchar las solicitudes HTTP POST entrantes. En el siguiente fragmento de código se muestra cómo gestionar la solicitud HTTP de devolución de llamada de emisión y actualizar la interfaz de usuario en consecuencia:

# <a name="http"></a>[HTTP](#tab/http)

No aplicable. Elija uno de los lenguajes de programación anteriores.

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

Consulte los artículos siguientes:

- [Especificación de API de emisión](issuance-request-api.md)
- [Especificación de API de presentación](presentation-request-api.md)
