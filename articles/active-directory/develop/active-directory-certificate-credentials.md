---
title: Credenciales de certificado de la plataforma de identidad de Microsoft
titleSuffix: Microsoft identity platform
description: En este artículo se describe el registro y el uso de credenciales de certificado para la autenticación de aplicaciones.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: contperf-fy21q4, aaddev
ms.openlocfilehash: ed3495bb7267c54f9b95f7fc3465d76ddde2faaa
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581896"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Credenciales de certificado para la autenticación de aplicaciones en la plataforma de identidad de Microsoft

La Plataforma de identidad de Microsoft permite que una aplicación use sus propias credenciales para la autenticación en cualquier parte en la que podría usarse un secreto de cliente; por ejemplo, en el flujo de [concesión de credenciales de cliente](v2-oauth2-client-creds-grant-flow.md) de OAuth 2.0 y el flujo [con derechos delegados](v2-oauth2-on-behalf-of-flow.md) (OBO).

Un formato de credencial que una aplicación puede utilizar para la autenticación es una aserción de [JSON Web Token](./security-tokens.md#json-web-tokens-and-claims) (JWT) firmada con un certificado que la aplicación posea.

## <a name="assertion-format"></a>Formato de aserción

Para calcular la aserción, puede usar una de las muchas bibliotecas de JWT en el lenguaje que prefiera. [MSAL admite esto mediante `.WithCertificate()`](msal-net-client-assertions.md). La información la lleva el token en el encabezado, las notificaciones y la firma.

### <a name="header"></a>Encabezado

| Parámetro |  Comentario |
| --- | --- |
| `alg` | Debe ser **RS256** |
| `typ` | Debe ser **JWT** |
| `x5t` | Representación hexadecimal del hash del certificado X.509 (también conocido como *huella digital* SHA-1 del certificado) codificada como un valor de cadena Base64url. Por ejemplo, para un hash de certificado X.509 con el valor `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (hexadecimal), la notificación `x5t` sería `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url). |

### <a name="claims-payload"></a>Notificaciones (carga útil)

Tipo de notificación | Value | Descripción
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | La notificación "aud" (audiencia) identifica los destinatarios para los que está previsto el JWT (en este caso Azure AD) Vea [RFC 7519, sección 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  En este caso, el destinatario es el servidor de inicio de sesión (login.microsoftonline.com).
exp | 1601519414 | La notificación "exp" (fecha de expiración) identifica la hora de expiración en la que o después de la que el token JWT no debe ser aceptado para su procesamiento. Vea [RFC 7519, Sección 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  Esto permite que la aserción se use hasta entonces, por lo que debe mantenerse a menos de 5-10 minutos después de `nbf` como máximo.  En la actualidad, Azure AD no impone restricciones en el tiempo de `exp`. 
iss | {ClientID} | La notificación "iss" (emisor) identifica la entidad de seguridad que ha emitido el JWT; en este caso, la aplicación cliente.  Use el identificador de aplicación GUID.
jti | (un GUID) | La notificación "jti" (Id. de JWT) proporciona un identificador único para el JWT. El valor del identificador se DEBE asignar de forma que se garantice que hay una probabilidad insignificante de que el mismo valor se asigne por accidente a otro objeto de datos; si en la aplicación se usan varios emisores, también se DEBEN evitar las colisiones entre los valores generados por otros emisores. El valor "jti" es una cadena que distingue mayúsculas de minúsculas. [RFC 7519, Sección 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7).
nbf | 1601519114 | La notificación "nbf" (no antes de) identifica la hora antes de la cual no debe ser aceptado el token JWT para su procesamiento. [RFC 7519, Sección 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  El uso de la hora actual es adecuado. 
sub | {ClientID} | La notificación "sub" (asunto) identifica el asunto del JWT; en este caso, también su aplicación. Use el mismo valor que `iss`. 

### <a name="signature"></a>Firma

La firma se calcula mediante la aplicación del certificado, como se describe en [Especificación RFC7519 de JSON Web Token](https://tools.ietf.org/html/rfc7519).

## <a name="example-of-a-decoded-jwt-assertion"></a>Ejemplo de una aserción de JWT descodificada

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Ejemplo de una aserción de JWT codificada

La cadena siguiente es un ejemplo de aserción codificada. Si observa detenidamente, verá tres secciones separadas por puntos (`.`):

* La primera sección codifica el *encabezado*.
* La segunda sección codifica las *notificaciones* (carga útil).
* La última sección es la *firma* calculada con los certificados a partir del contenido de las dos primeras secciones.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registro del certificado con la plataforma de identidad de Microsoft

Puede asociar las credenciales del certificado con la aplicación cliente en la Plataforma de identidad de Microsoft a través de Azure Portal mediante cualquiera de los métodos siguientes:

### <a name="uploading-the-certificate-file"></a>Cargar el archivo de certificado

En el registro de aplicación de Azure para la aplicación cliente:
1. Seleccione **Certificados y secretos**.
2. Haga clic en **Cargar certificado** y seleccione el archivo del certificado que va a cargar.
3. Haga clic en **Agregar**.
  Una vez que se carga el certificado, se muestra la huella digital, la fecha de inicio y los valores de expiración.

### <a name="updating-the-application-manifest"></a>Actualizar el manifiesto de aplicación

Después de adquirir un certificado, calcule estos valores:

- `$base64Thumbprint`: valor codificado en Base 64 del hash del certificado
- `$base64Value`: valor codificado en Base 64 de los datos sin procesar del certificado

Proporcione un GUID para identificar la clave en el manifiesto de la aplicación (`$keyId`).

En el registro de aplicación de Azure para la aplicación cliente:
1. Seleccione **Manifiesto** para abrir el manifiesto de aplicación.
2. Reemplace la propiedad *keyCredentials* por la información del nuevo certificado con el siguiente esquema.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Guarde las modificaciones en el manifiesto de aplicación y cárguelo en la plataforma de identidad de Microsoft.

   La propiedad `keyCredentials` es multivalor, por lo que puede cargar varios certificados para una administración de claves más eficaz.
   
## <a name="using-a-client-assertion"></a>Uso de una aserción de cliente

Las aserciones de cliente se pueden usar en cualquier lugar en el que se use un secreto de cliente.  Por ejemplo, en el [flujo de código de autorización](v2-oauth2-auth-code-flow.md), puede pasar un objeto `client_secret` para demostrar que la solicitud procede de la aplicación. Puede reemplazarlo por parámetros `client_assertion` y `client_assertion_type`. 

| Parámetro | Valor | Descripción|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Se trata de un valor fijo, que indica que está usando una credencial de certificado. |
|`client_assertion`| JWT |Este es el JWT creado anteriormente. |

## <a name="next-steps"></a>Pasos siguientes

La biblioteca [MSAL.NET controla este escenario](msal-net-client-assertions.md) en una sola línea de código.

En el ejemplo de código [Aplicación de consola de demonio de .NET Core con la Plataforma de identidad de Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) de GitHub se muestra cómo utiliza una aplicación sus propias credenciales para la autenticación. También se muestra cómo se puede [crear un certificado autofirmado](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script) mediante el cmdlet de PowerShell `New-SelfSignedCertificate`. También puede usar los [scripts de creación de aplicaciones](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) del repositorio de ejemplos para crear los certificados, calcular la huella digital, etc.
