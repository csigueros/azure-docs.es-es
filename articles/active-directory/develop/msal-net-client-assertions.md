---
title: Aserciones de cliente (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre la compatibilidad con las aserciones de cliente para aplicaciones cliente confidenciales en la Biblioteca de autenticación de Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/18/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 92c1dbe34078442fa5ba84c00880e15502602bc6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787156"
---
# <a name="confidential-client-assertions"></a>Aserciones de cliente confidenciales

Con el fin de demostrar su identidad, las aplicaciones cliente confidenciales intercambian un secreto con Azure AD. El secreto puede ser:
- un secreto de cliente (contraseña de aplicación).
- un certificado, que se usa para crear una aserción firmada que contiene notificaciones estándar.

Este secreto también puede ser una aserción firmada directamente.

MSAL.NET tiene cuatro métodos para proporcionar credenciales o aserciones a la aplicación cliente confidencial:
- `.WithClientSecret()`
- `.WithCertificate()`
- `.WithClientAssertion()`
- `.WithClientClaims()`

> [!NOTE]
> Aunque es posible usar la API `WithClientAssertion()` para adquirir tokens para el cliente confidencial, no se recomienda su uso de forma predeterminada, ya que es más avanzado y está diseñado para controlar escenarios muy concretos que no son comunes. El uso de la API `.WithCertificate()` permitirá que MSAL.NET lo controle. Esta API ofrece la posibilidad de personalizar la solicitud de autenticación si es necesario, pero la aserción predeterminada creada por `.WithCertificate()` será suficiente para la mayoría de los escenarios de autenticación. Esta API también se puede usar como solución alternativa en algunos escenarios en los que MSAL.NET no pueda realizar la operación de firma internamente.

### <a name="signed-assertions"></a>Aserciones firmadas

Una aserción de cliente firmada adopta la forma de un JWT firmado con la carga que contiene las notificaciones de autenticación necesarias impuestas por Azure AD, codificado en Base64. Para usarla:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(signedClientAssertion)
                                          .Build();
```

También puede usar el formulario de delegado, que permite calcular la aserción cuando es necesario:

```csharp
string signedClientAssertion = ComputeAssertion();
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(() => { return GetSignedClientAssertion(); } )
                                          .Build();
                                          
// or in async manner

app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientAssertion(async cancellationToken => { return await GetClientAssertionAsync(cancellationToken); })
                                          .Build();
```

A continuación se indican las [notificaciones que espera Azure AD](active-directory-certificate-credentials.md) en la aserción firmada:

Tipo de notificación | Value | Descripción
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | La notificación "aud" (audiencia) identifica los destinatarios para los que está previsto el JWT (en este caso Azure AD) Vea [RFC 7519, sección 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  En este caso, el destinatario es el servidor de inicio de sesión (login.microsoftonline.com).
exp | 1601519414 | La notificación "exp" (fecha de expiración) identifica la hora de expiración en la que o después de la que el token JWT no debe ser aceptado para su procesamiento. Vea [RFC 7519, Sección 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Esto permite que la aserción se use hasta entonces, por lo que debe mantenerse a menos de 5-10 minutos después de `nbf` como máximo.  En la actualidad, Azure AD no impone restricciones en el tiempo de `exp`. 
iss | {ClientID} | La notificación "iss" (emisor) identifica la entidad de seguridad que ha emitido el JWT; en este caso, la aplicación cliente.  Use el identificador de aplicación GUID.
jti | (un GUID) | La notificación "jti" (Id. de JWT) proporciona un identificador único para el JWT. El valor del identificador se DEBE asignar de forma que se garantice que hay una probabilidad insignificante de que el mismo valor se asigne por accidente a otro objeto de datos; si en la aplicación se usan varios emisores, también se DEBEN evitar las colisiones entre los valores generados por otros emisores. El valor "jti" es una cadena que distingue mayúsculas de minúsculas. [RFC 7519, Sección 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7).
nbf | 1601519114 | La notificación "nbf" (no antes de) identifica la hora antes de la cual no debe ser aceptado el token JWT para su procesamiento. [RFC 7519, Sección 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  El uso de la hora actual es adecuado. 
sub | {ClientID} | La notificación "sub" (asunto) identifica el asunto del JWT; en este caso, también su aplicación. Use el mismo valor que `iss`. 

Este es un ejemplo de cómo crear estas notificaciones:

```csharp
private static IDictionary<string, string> GetClaims()
{
      //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
      string aud = $"https://login.microsoftonline.com/{tenantId}/v2.0";

      string ConfidentialClientID = "00000000-0000-0000-0000-000000000000" //client id
      const uint JwtToAadLifetimeInSeconds = 60 * 10; // Ten minutes
      DateTime validFrom = DateTime.UtcNow;
      var nbf = ConvertToTimeT(validFrom);
      var exp = ConvertToTimeT(validFrom + TimeSpan.FromSeconds(JwtToAadLifetimeInSeconds));

      return new Dictionary<string, string>()
           {
                { "aud", aud },
                { "exp", exp.ToString() },
                { "iss", ConfidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "nbf", nbf.ToString() },
                { "sub", ConfidentialClientID }
            };
}
```

Aquí se muestra cómo crear una aserción de cliente firmada:

```csharp
string Encode(byte[] arg)
{
    char Base64PadCharacter = '=';
    char Base64Character62 = '+';
    char Base64Character63 = '/';
    char Base64UrlCharacter62 = '-';
    char Base64UrlCharacter63 = '_';

    string s = Convert.ToBase64String(arg);
    s = s.Split(Base64PadCharacter)[0]; // RemoveAccount any trailing padding
    s = s.Replace(Base64Character62, Base64UrlCharacter62); // 62nd char of encoding
    s = s.Replace(Base64Character63, Base64UrlCharacter63); // 63rd char of encoding

    return s;
}

string GetSignedClientAssertion()
{
    //Signing with SHA-256
    string rsaSha256Signature = "http://www.w3.org/2001/04/xmldsig-more#rsa-sha256";
     X509Certificate2 certificate = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

    //Create RSACryptoServiceProvider
    var x509Key = new X509AsymmetricSecurityKey(certificate);
    var privateKeyXmlParams = certificate.PrivateKey.ToXmlString(true);
    var rsa = new RSACryptoServiceProvider();
    rsa.FromXmlString(privateKeyXmlParams);

    //alg represents the desired signing algorithm, which is SHA-256 in this case
    //kid represents the certificate thumbprint
    var header = new Dictionary<string, string>()
         {
              { "alg", "RS256"},
              { "kid", Encode(certificate.GetCertHash()) }
         };

    //Please see the previous code snippet on how to craft claims for the GetClaims() method
    string token = Encode(Encoding.UTF8.GetBytes(JObject.FromObject(header).ToString())) + "." + Encode(Encoding.UTF8.GetBytes(JObject.FromObject(GetClaims()).ToString()));

    string signature = Encode(rsa.SignData(Encoding.UTF8.GetBytes(token), new SHA256Cng()));
    string signedClientAssertion = string.Concat(token, ".", signature);
    return signedClientAssertion;
}
```

### <a name="alternative-method"></a>Método alternativo

También tiene la opción de usar [Microsoft.IdentityModel.JsonWebTokens](https://www.nuget.org/packages/Microsoft.IdentityModel.JsonWebTokens/) para crear la aserción. El código será más elegante, tal como se muestra en el ejemplo siguiente:

```csharp
        string GetSignedClientAssertion()
        {
            var cert = new X509Certificate2("Certificate.pfx", "Password", X509KeyStorageFlags.EphemeralKeySet);

            //aud = https://login.microsoftonline.com/ + Tenant ID + /v2.0
            string aud = $"https://login.microsoftonline.com/{tenantID}/v2.0";

            // client_id
            string confidentialClientID = "00000000-0000-0000-0000-000000000000";

            // no need to add exp, nbf as JsonWebTokenHandler will add them by default.
            var claims = new Dictionary<string, object>()
            {
                { "aud", aud },
                { "iss", confidentialClientID },
                { "jti", Guid.NewGuid().ToString() },
                { "sub", confidentialClientID }
            };

            var securityTokenDescriptor = new SecurityTokenDescriptor
            {
                Claims = claims,
                SigningCredentials = new X509SigningCredentials(cert)
            };

            var handler = new JsonWebTokenHandler();
            var signedClientAssertion = handler.CreateToken(securityTokenDescriptor);
        }
```

Una vez que tenga la aserción de cliente firmada, puede usarla con las API de MSAL, como se muestra a continuación.

```csharp
            string signedClientAssertion = GetSignedClientAssertion();

            var confidentialApp = ConfidentialClientApplicationBuilder
                .Create(ConfidentialClientID)
                .WithClientAssertion(signedClientAssertion)
                .Build();
```

### <a name="withclientclaims"></a>WithClientClaims

De forma predeterminada, `WithClientClaims(X509Certificate2 certificate, IDictionary<string, string> claimsToSign, bool mergeWithDefaultClaims = true)` generará una aserción firmada que contiene las notificaciones esperadas por Azure AD más las notificaciones de cliente adicionales que se quieren enviar. Este es un fragmento de código sobre cómo hacerlo.

```csharp
string ipAddress = "192.168.1.2";
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithAuthority(new Uri(config.Authority))
                                          .WithClientClaims(certificate, 
                                                                      new Dictionary<string, string> { { "client_ip", ipAddress } })
                                          .Build();

```

Si una de las notificaciones del diccionario que se pasa es la misma que una de las notificaciones obligatorias, se tendrá en cuenta el valor de la notificación adicional. Invalidará las notificaciones calculadas por MSAL.NET.

Si quiere proporcionar notificaciones propias, incluidas las obligatorias que espera Azure AD, pase `false` para el parámetro `mergeWithDefaultClaims`.
