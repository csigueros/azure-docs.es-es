---
title: Tokens de id. de la plataforma de identidad de Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre cómo usar los id_tokens emitidos por Azure AD v1.0 y los puntos de conexión de la plataforma de identidad de Microsoft (v2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom:
- aaddev
- identityplatformtop40
- fasttrack-edit
ms.openlocfilehash: 920589c3c0582387a83d5f7d85c660f0692a761b
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110471285"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Tokens de id. de la plataforma de identidad de Microsoft

El token de identificador es la extensión principal que [OpenID Connect](v2-protocols-oidc.md) establece en OAuth 2.0. Los tokens de identificador los emite el servidor de autorización y contienen notificaciones que incluyen información sobre el usuario. Se pueden enviar junto o en lugar de un token de acceso. La información de los tokens de identificador permite al cliente comprobar que un usuario es quien dice ser. Los tokens de identificador están diseñados para que los comprendan aplicaciones de terceros. Los tokens de identificador no se deben usar con fines de autorización. Los [tokens de acceso](access-tokens.md) se usan para la autorización. Las notificaciones que proporcionan los tokens de identificador pueden usarse para la experiencia del usuario en la aplicación, como [claves en una base de datos](#using-claims-to-reliably-identify-a-user-subject-and-object-id), y proporcionar acceso a la aplicación cliente.

## <a name="prerequisites"></a>Requisitos previos

El siguiente artículo le resultará útil antes de seguir este artículo:

* [Protocolos OAuth 2.0 y OpenID Connect](active-directory-v2-protocols.md) en la Plataforma de identidad de Microsoft


## <a name="claims-in-an-id-token"></a>Notificaciones de un token de identificador.

Los tokens de identificador son [tokens web JSON (JWT)](https://jwt.io/introduction/). Estos tokens de identificador constan de un encabezado, una carga útil y una firma. Puede usar el encabezado y la firma para comprobar la autenticidad del token, mientras que la carga útil contiene la información sobre el usuario solicitado por el cliente. Los token de identificador v1.0 y v2.0 presentan diferencias en la información que contienen. La versión se basa en el punto de conexión desde donde se solicitó. Aunque es probable que las aplicaciones existentes utilicen el punto de conexión de Azure AD (v1.0), las nuevas aplicaciones deben usar el punto de conexión "Plataforma de identidad de Microsoft" (v2.0).

* v1.0: punto de conexión de Azure AD `https://login.microsoftonline.com/common/oauth2/authorize`
* v2.0: punto de conexión de la Plataforma de identidad de Microsoft `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="sample-v10-id-token"></a>Token de identificador de la versión 1.0 de ejemplo

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Puede ver este token de ejemplo de la versión v1.0 en [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="sample-v20-id-token"></a>Token de identificador de la versión 2.0 de ejemplo

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Puede ver este token de ejemplo de la versión v2.0 en [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

Todas las notificaciones JWT que se enumeran a continuación aparecen en los tokens v1.0 y v2.0, a menos que se indique lo contrario.

### <a name="header-claims"></a>Notificaciones de encabezado

En la tabla siguiente se muestran las notificaciones de encabezado presentes en los tokens de identificador.

|Notificación | Formato | Descripción |
|-----|--------|-------------|
|`typ` | Cadena: siempre "JWT" | Indica que se trata de un token JWT.|
|`alg` | String | Indica el algoritmo que se usó para firmar el token. Ejemplo: "RS256" |
|`kid` | String | Huella digital de la clave pública utilizada para verificar este token. Se emite en los `id_tokens` de las versiones 1.0 y 2.0. |
|`x5t` | String | Es igual (en uso y valor) a `kid`. Sin embargo, esta es una notificación heredada emitida solo en los `id_tokens` de la versión 1.0 para fines de compatibilidad. |

### <a name="payload-claims"></a>Notificaciones de carga

En esta lista se muestran las notificaciones que se encuentran en la mayoría de los tokens de identificador de forma predeterminada (excepto que se indique de otro modo).  Sin embargo, su aplicación puede usar [notificaciones opcionales](active-directory-optional-claims.md) para solicitar notificaciones adicionales en el token de identificador. Las notificaciones opcionales pueden ir desde la notificación `groups` hasta información sobre el nombre del usuario.

|Notificación | Formato | Descripción |
|-----|--------|-------------|
|`aud` |  Cadena, GUID de identificador de aplicación | Identifica al destinatario previsto del token. En los `id_tokens`, la audiencia es el identificador de aplicación de la aplicación, asignado a la aplicación en Azure Portal. Este valor debe validarse. El token debe rechazarse si no coincide con el identificador de la aplicación. |
|`iss` |  Cadena, URI de emisor | Identifica al emisor o "servidor de autorización" que construye y devuelve el token. También identifica al inquilino de Azure AD para el que se autenticó el usuario. Si el token fue emitido por el punto de conexión de la versión 2.0, el identificador URI finalizará con `/v2.0`.  El GUID que indica que el usuario es un usuario consumidor desde una cuenta de Microsoft es `9188040d-6c67-4c5b-b112-36a304b66dad`. La aplicación debe usar la parte del GUID de la notificación para restringir el conjunto de inquilinos que pueden iniciar sesión en la aplicación, si procede. |
|`iat` |  entero, una marca de tiempo de UNIX | La notificación "iat" (emitido a las) indica cuándo se produjo la autenticación de este token.  |
|`idp`|Cadena, normalmente un identificador URI de STS | Registra el proveedor de identidades que autenticó al firmante del token. Este valor es idéntico al valor de la notificación del emisor, a menos que la cuenta de usuario no esté en el mismo inquilino que el emisor: los invitados, por ejemplo. Si la notificación no está presente, significa que el valor de `iss` se puede usar en su lugar.  Para las cuentas personales que se usan en un contexto de la organización (por ejemplo, una cuenta personal invitada a un inquilino de Azure AD), la notificación `idp` puede ser "live.com" o un identificador URI de STS que contenga al inquilino de la cuenta Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  entero, una marca de tiempo de UNIX | La notificación "nbf" (no antes de) identifica la hora antes de la cual no debe ser aceptado el token JWT para su procesamiento.|
|`exp` |  entero, una marca de tiempo de UNIX | La notificación "exp" (fecha de expiración) identifica la hora de expiración en la que o después de la que el token JWT no debe ser aceptado para su procesamiento.  Es importante tener en cuenta que, en determinadas circunstancias, un recurso puede rechazar el token antes de este tiempo. Por ejemplo, si se requiere un cambio en la autenticación o se detecta una revocación de tokens. |
| `c_hash`| String |El hash de código se incluye en los tokens de identificador solo cuando se emite el token de identificador con un código de autorización de OAuth 2.0. Se puede usar para validar la autenticidad de un código de autorización. Para comprender cómo realizar esta validación, consulte la [especificación OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). |
|`at_hash`| String |El hash de token de acceso se incluye en los tokens de identificador solo cuando el token de identificador se emite desde el punto de conexión `/authorize` con un token de acceso de OAuth 2.0. Se puede usar para validar la autenticidad de un token de acceso. Para comprender cómo realizar esta validación, consulte la [especificación OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). Esto no se devuelve en tokens de identificador desde el punto de conexión de `/token`. |
|`aio` | Cadena opaca | Una notificación interna usada por Azure AD para registrar los datos para la reutilización de tokens. Se debe omitir.|
|`preferred_username` | String | El nombre de usuario principal que representa al usuario. Puede ser una dirección de correo electrónico, un número de teléfono o un nombre de usuario genérico sin un formato especificado. Su valor es mutable y podría cambiar en el tiempo. Puesto que este valor se puede modificar, no debe usarse para tomar decisiones de autorización. El ámbito `profile` es necesario para recibir esta notificación.|
|`email` | String | La notificación `email` está presente de manera predeterminada para las cuentas de invitado que tengan una dirección de correo electrónico.  La aplicación puede solicitar la notificación de correo electrónico para los usuarios administrados (del mismo inquilino que el recurso) mediante la [notificación opcional](active-directory-optional-claims.md) de `email`.  En el punto de conexión v2.0, la aplicación también puede solicitar el ámbito `email` de OpenID Connect. No es necesario que solicite ambos, la notificación opcional y el ámbito para obtener la notificación.  La notificación de correo electrónico solo es compatible con correo direccionable desde la información del perfil del usuario. |
|`name` | String | La notificación `name` proporciona un valor en lenguaje natural que identifica al firmante del token. No se garantiza que el valor sea único, se puede modificar y está diseñado para usarse solo con fines de visualización. El ámbito `profile` es necesario para recibir esta notificación. |
|`nonce`| String | El valor nonce coincide con el parámetro incluido en la solicitud /authorize original al IDP. Si no coincide, la aplicación debe rechazar el token. |
|`oid` | Cadena, un identificador GUID | El identificador inmutable de un objeto en el sistema de identidades Microsoft, en este caso, una cuenta de usuario. Este identificador identifica de forma única el usuario entre aplicaciones: dos aplicaciones diferentes que inician sesión con el mismo usuario recibirán el mismo valor en la notificación `oid`. Microsoft Graph devuelve este identificador como la propiedad `id` para una cuenta de usuario determinada. Dado que la notificación `oid` permite que varias aplicaciones pongan en correlación a los usuarios, se requiere el ámbito `profile` para recibir esta notificación. Tenga en cuenta que, si un usuario existe en varios inquilinos, el usuario contendrá un id. de objeto distinto en cada inquilino y se consideran cuentas diferentes, incluso si el usuario inicia sesión en todas las cuentas con las mismas credenciales. La notificación `oid` es un GUID y no se puede volver a usar. |
|`roles`| Matriz de cadenas | El conjunto de roles que se asignaron al usuario que ha iniciado sesión. |
|`rh` | Cadena opaca |Una notificación interna que Azure usa para volver a validar los tokens. Se debe omitir. |
|`sub` | String | La entidad de seguridad sobre la que el token declara información como, por ejemplo, el usuario de una aplicación. Este valor es inmutable y no se puede reasignar ni volver a usar. El firmante es un identificador en pares: es único para un identificador de aplicación determinado. Si un usuario inicia sesión en dos aplicaciones diferentes con dos identificadores de cliente diferentes, esas aplicaciones recibirán dos valores diferentes para la notificación de firmante. Esto puede ser o no deseable en función de los requisitos de arquitectura y privacidad. |
|`tid` | Cadena, un identificador GUID | Un GUID que representa el inquilino de Azure AD de donde proviene el usuario. En el caso de las cuentas profesionales y educativas, el GUID es el identificador del inquilino inmutable de la organización a la que pertenece el usuario. En el caso de las cuentas personales, el valor es `9188040d-6c67-4c5b-b112-36a304b66dad`. El ámbito `profile` es necesario para recibir esta notificación. |
|`unique_name` | String | Proporciona un valor en lenguaje natural que identifica al firmante del token. Este valor es único en un momento dado, pero dado que los mensajes de correo electrónico y otros identificadores pueden reutilizarse, puede aparecer de nuevo en otras cuentas. Por lo tanto, el valor solo debe usarse con fines de visualización. Solo se emite en los `id_tokens` de la versión 1.0. |
|`uti` | Cadena opaca | Una notificación interna que Azure usa para volver a validar los tokens. Se debe omitir. |
|`ver` | Cadena, 1.0 o 2.0 | Indica la versión del id_token. |
|`hasgroups`|Boolean|Si existe, siempre es true, lo cual indica que el usuario está en al menos un grupo. Se usa en lugar de la notificación de grupos para métodos JWT en los flujos de concesión implícita si la notificación completa de grupos amplía el fragmento URI por encima de los límites de longitud de la dirección URL (actualmente 6 o más grupos). Indica que el cliente debe utilizar Microsoft Graph API para determinar los grupos del usuario (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`).|
|`groups:src1`|Objeto JSON | Para las solicitudes de tokens que no tienen limitación de longitud (consulte la notificación `hasgroups` anterior) pero que todavía son demasiado grandes para el token, se incluirá un enlace a la lista completa de grupos del usuario. Para métodos JWT como una notificación distribuida, para SAML como una nueva notificación en lugar de la notificación `groups`. <br><br>**Valor de JWT de ejemplo**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Para más información, consulte [Notificación de grupos por encima del límite](#groups-overage-claim).|

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Uso de notificaciones para identificar a un usuario de forma confiable (firmante e id. de objeto)

Al identificar a un usuario (por ejemplo, buscarlo en una base de datos o decidir qué permisos tiene), es fundamental usar información que permanecerá constante y única a lo largo del tiempo. Las aplicaciones heredadas a veces usan campos, como la dirección de correo electrónico, un número de teléfono o el UPN.  Todos ellos pueden cambiar con el tiempo y también se pueden reutilizar. Por ejemplo, si un empleado cambia de nombre, o a un empleado se le asigna una dirección de correo electrónico que coincide con la de un empleado anterior que ya no está presente. Por lo tanto, es **crítico** que la aplicación no use datos legibles para identificar a un usuario; legible normalmente significa que alguien lo leerá y querrá cambiarlo. En su lugar, use las notificaciones proporcionadas por el estándar OIDC o las notificaciones de extensión proporcionadas por Microsoft: las notificaciones `sub` y `oid`.

Para almacenar correctamente la información por usuario, use solo `sub` o `oid` (que son únicos como los GUID), y use `tid` para el enrutamiento o el particionamiento si es necesario.  Si necesita compartir datos entre servicios, `oid`+`tid` es lo mejor cuando todas las aplicaciones obtienen las mismas notificaciones `oid` y `tid` para un usuario determinado.  La notificación `sub` en la plataforma de identidad de Microsoft es "por pares"; es única en función de una combinación del destinatario del token, el inquilino y el usuario.  Por lo tanto, dos aplicaciones que solicitan tokens de identificador para un usuario determinado recibirán diferentes notificaciones `sub`, pero las mismas notificaciones `oid` para ese usuario.

>[!NOTE]
> No utilice la notificación `idp` para almacenar información sobre un usuario en un intento de correlacionar a los usuarios entre inquilinos.  No funcionará, ya que las notificaciones `oid` y `sub` para un usuario cambian entre los inquilinos, por diseño, para asegurarse de que las aplicaciones no puedan realizar el seguimiento de los usuarios entre inquilinos.  
>
> Los escenarios de invitado, en los que un usuario se hospeda en un inquilino y se autentica en otro, deben tratar al usuario como si fuera un usuario completamente nuevo en el servicio.  Los documentos y privilegios del inquilino de Contoso no deben aplicarse en el inquilino de Fabrikam. Esto es importante para evitar la filtración accidental de datos entre los inquilinos.

### <a name="groups-overage-claim"></a>Notificación de grupos por encima del límite
Para garantizar que el tamaño del token no supera los límites de tamaño del encabezado HTTP, Azure AD limita el número de identificadores de objeto que se incluyen en la notificación `groups`. Si un usuario es miembro de más grupos que el límite de uso por encima del límite (150 para los tokens SAML, 200 para los tokens JWT), Azure AD no emite la notificaciones de grupos en el token. En su lugar, incluye una demanda de uso por encima del límite en el token que indica a la aplicación que consulte la Microsoft Graph API para recuperar la pertenencia a grupos del usuario.

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

## <a name="id-token-lifetime"></a>Vigencia del token de identificador

De manera predeterminada, un token de identificador es válido durante una hora; después de una hora, el cliente debe adquirir un nuevo token de identificador.

Puede ajustar la vigencia de un token de identificador para controlar la frecuencia con la que la aplicación cliente expira la sesión de la aplicación y requerirá que el usuario se vuelva a autenticar (de forma silenciosa o interactiva). Para más información, consulte [Vigencia de tokens configurable en la Plataforma de identidad de Microsoft (versión preliminar)](active-directory-configurable-token-lifetimes.md).

## <a name="validating-an-id-token"></a>Validación de un token de identificador

La validación de un token de identificador es similar al primer paso de [validación de un token de acceso](access-tokens.md#validating-tokens). El cliente puede comprobar si el token se ha alterado. También puede validar al emisor para asegurarse de que el emisor correcto ha devuelto el token. Dado que los tokens de identificador siempre son un token JWT, existen muchas bibliotecas para validar estos tokens: se recomienda usar una de estas en lugar de hacerlo usted mismo.  Tenga en cuenta que solo los clientes confidenciales (los que tienen un secreto) deben validar los tokens de identificador.  Las aplicaciones públicas (el código se ejecuta completamente en un dispositivo o red que no controla; por ejemplo, el explorador de un usuario o su red doméstica) no se benefician de la validación del token de identificador. Esto se debe a que un usuario malintencionado puede interceptar y editar las claves usadas para la validación del token.

Para validar manualmente el token, consulte los pasos detallados en [Validación de un token de acceso](access-tokens.md#validating-tokens). Las siguientes notificaciones JWT deben validarse en el token de identificador después de validar la firma en el token. La biblioteca de validación de tokens también puede validar estas notificaciones:

* Marcas de tiempo: las marcas de tiempo `iat`, `nbf` y `exp` deben estar todas antes o después de la hora actual, según corresponda.
* Audiencia: la notificación `aud` debe coincidir con el identificador de aplicación de la aplicación.
* Nonce: la notificación `nonce` de la carga debe coincidir con el parámetro nonce pasado en el punto de conexión /authorize durante la solicitud inicial.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre los [tokens de acceso](access-tokens.md)
* Personalice las notificaciones de JWT del token de identificación mediante las [notificaciones opcionales](active-directory-optional-claims.md).
