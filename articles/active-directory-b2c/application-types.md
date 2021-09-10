---
title: Tipos de aplicación compatibles con Azure AD B2C
titleSuffix: Azure AD B2C
description: Más información sobre los tipos de aplicaciones que puede usar en Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/17/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6e4f54f736a47cc6b351ff02b8fb7f8bad1c5c56
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/23/2021
ms.locfileid: "112552701"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Tipos de aplicaciones que se pueden usar en Active Directory B2C
 
Azure Active Directory B2C (Azure AD B2C) admite la autenticación en diversas arquitecturas de aplicaciones modernas. Todas ellas se basan en los protocolos estándar del sector [OAuth 2.0](protocols-overview.md) u [OpenID Connect](protocols-overview.md). Este artículo describe los tipos de aplicaciones que puede compilar, independientemente del lenguaje o la plataforma que prefiera. También ayuda a entender los escenarios de alto nivel antes de empezar a compilar aplicaciones.

Toda aplicación que use Azure AD B2C debe estar registrada en su [inquilino de Azure AD B2C](tutorial-create-tenant.md) que use [Azure Portal](https://portal.azure.com/). El proceso de registro de la aplicación recopila y asigna valores, tales como:

* Un **identificador de la aplicación** que identifica la aplicación de forma única.
* Un **URI de redirección** que puede usarse para devolver las respuestas a la aplicación.

Cada solicitud que se envía a Azure AD B2C especifica un **flujo de usuario** (una directiva integrada) o una **directiva personalizada** que controla el comportamiento de Azure AD B2C. Ambos tipos de directiva le permiten crear un conjunto altamente personalizable de experiencias de usuario.

La interacción de cada aplicación sigue un patrón de nivel alto similar:

1. La aplicación dirige al usuario al punto de conexión v2.0 para ejecutar una [directiva](user-flow-overview.md).
2. El usuario completa la directiva según la definición de la misma.
3. La aplicación recibe un token de seguridad del punto de conexión v2.0.
4. La aplicación utiliza el token de seguridad para acceder a información o recursos protegidos.
5. El servidor de recursos valida el token de seguridad para asegurarse de que se puede conceder acceso.
6. La aplicación actualiza periódicamente el token de seguridad.

Estos pasos pueden diferir ligeramente según el tipo de aplicación que está compilando.

## <a name="web-applications"></a>Aplicaciones web

Para las aplicaciones web (incluidas .NET, PHP, Java, Ruby, Python y Node.js) que se hospedan en un servidor y a las que se tiene acceso a través de un explorador, Azure AD B2C admite [OpenID Connect](protocols-overview.md) para todas las experiencias de usuario. En la implementación de OpenID Connect de Azure AD B2C, su aplicación web inicia las experiencias de usuario mediante la emisión de solicitudes de autenticación a Azure AD. El resultado de la solicitud es un elemento `id_token`. Este token de seguridad representa la identidad del usuario. También proporciona información sobre el usuario en forma de notificaciones:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Obtenga más información sobre todos los tipos de tokens y notificaciones disponibles para una aplicación en la [referencia de token de Azure AD B2C](tokens-overview.md).

En una aplicación web, cada ejecución de una [directiva](user-flow-overview.md) realiza estos pasos generales:

1. El usuario se desplaza a la aplicación web.
2. La aplicación web redirige al usuario a Azure AD B2C que indicando la directiva que se ejecutará.
3. El usuario complete la directiva.
4. Azure AD B2C devuelve un `id_token` al explorador.
5. El `id_token` se publica en el URI de redirección.
6. El `id_token` se valida y se establece una cookie de sesión.
7. Se devuelve al usuario una página segura.

La validación de `id_token` mediante una clave de firma pública que se recibe de Azure AD es suficiente para comprobar la identidad del usuario. Este proceso también establece una cookie de sesión que puede usarse para identificar al usuario en las solicitudes de página posteriores.

Para ver este escenario en acción, pruebe uno de los ejemplos de código de inicio de sesión de aplicación web en nuestra [sección Introducción](overview.md).

Además de facilitar un inicio de sesión simple, es posible que una aplicación de servidor web también necesite acceder a algún servicio web back-end. En este caso, la aplicación web puede realizar un [flujo de OpenID Connect](openid-connect.md) ligeramente distinto y adquirir tokens mediante códigos de autorización y tokens de actualización. Este escenario se describe en la siguiente sección [API web](#web-apis).

## <a name="single-page-applications"></a>Aplicación de página única
Muchas aplicaciones web modernas se compilan como aplicaciones de página única (SPA) del lado cliente. Los desarrolladores las escriben mediante JavaScript o un marco SPA, como Angular, Vue y React. Estas aplicaciones se ejecutan en un explorador web y tienen características de autenticación diferentes a las de las aplicaciones web del lado servidor tradicionales.

Azure AD B2C ofrece **dos** opciones para que las aplicaciones de página única inicien la sesión de los usuarios y obtengan tokens para acceder a los servicios de back-end o a las API web:

### <a name="authorization-code-flow-with-pkce"></a>Flujo de códigos de autorización (con PKCE)
- [Flujo de códigos de autorización de OAuth 2.0 (con PKCE)](./authorization-code-flow.md) El flujo del código de autorización permite a la aplicación intercambiar un código de autorización para los tokens de **identificador** para representar al usuario autenticado y también los tokens de **acceso** necesarios para llamar a las API protegidas. Además, devuelve tokens de **actualización** que proporcionan acceso a largo plazo a los recursos en nombre de los usuarios sin necesidad de interacción con estos. 

Este es el enfoque **recomendado**. Contar con tokens de actualización de duración limitada ayuda a que la aplicación se adapte a las [limitaciones de privacidad de cookies de los exploradores modernos](../active-directory/develop/reference-third-party-cookies-spas.md), como ITP de Safari.

Para aprovechar este flujo, la aplicación puede usar una biblioteca de autenticación que lo admita, como [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

<!-- ![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg) -->
![Aplicaciones de página única: autorización](./media/tutorial-single-page-app/active-directory-oauth-code-spa.png)

### <a name="implicit-grant-flow"></a>Flujo de concesión implícito
- [Flujo implícito de OAuth 2.0](implicit-flow-single-page-application.md). Algunos marcos, como [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core), solo admiten el flujo de concesión implícita. El flujo de concesión implícita permite a la aplicación obtener tokens de **identificador** y de **acceso**. A diferencia del flujo del código de autorización, el flujo de concesión implícita no devuelve un **token de actualización**. 

Este flujo de autenticación no incluye escenarios de aplicaciones que usan marcos de JavaScript multiplataforma, como Electron y React Native, ya que requieren más funcionalidades para la interacción con las plataformas nativas.

## <a name="web-apis"></a>API web

También puede usar Azure AD B2C para proteger los servicios web, como la API web RESTful de su aplicación. Las API web puede usar OAuth 2.0 para proteger sus datos mediante la autenticación de las solicitudes HTTP entrantes con tokens. El llamador de una API web anexa un token en el encabezado de autorización de una solicitud HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
``` 

A continuación, la API web puede usar el token para comprobar la identidad del llamador de la API y extraer información sobre el llamador de notificaciones que se codifican en el token. Aprenda más sobre todos los tipos de tokens y notificaciones disponibles para una aplicación en la [referencia de token de Azure AD B2C](tokens-overview.md).

Una API web puede recibir tokens de muchos tipos de clientes, incluidas aplicaciones web, aplicaciones móviles y de escritorio, aplicaciones de una página, demonios del lado del servidor e incluso otras API web. Por ejemplo, veamos el flujo completo de una aplicación web que llama a una API web:

1. La aplicación web ejecuta una directiva y el usuario completa la experiencia de usuario.
2. Azure AD B2C devuelve un objeto `id_token` (OpenID Connect) y un código de autorización al explorador.
3. El explorador publica el `id_token` y un código de autorización en el URI de redirección.
4. El servidor web valida el `id_token` y establece una cookie de sesión.
5. El servidor web le pide a Azure AD B2C un objeto `access_token` al que se le proporcionan el código de autorización, el identificador de cliente de la aplicación y las credenciales del cliente.
6. El `access_token` y `refresh_token` se devuelven al servidor web.
7. Se llama a la API web con el `access_token` en un encabezado de autorización.
8. La API web valida el token.
9. Se devuelven datos seguros a la aplicación web.

Para más información sobre los códigos de autorización, los tokens de actualización y los pasos para obtener tokens, lea acerca del [protocolo OAuth 2.0](authorization-code-flow.md).

Para más información sobre cómo proteger una API web con Azure AD B2C, consulte los tutoriales de API web en nuestra sección [Introducción](overview.md).

## <a name="mobile-and-native-applications"></a>Aplicaciones móviles y aplicaciones nativas

Las aplicaciones instaladas en dispositivos, como aplicaciones móviles y de escritorio, suelen necesitar el acceso a servicios back-end o a las API web en nombre de los usuarios. Puede agregar experiencias de administración de identidades personalizadas a sus aplicaciones nativas y llamar de forma segura a servicios back-end con Azure AD B2C y el [flujo de código de autorización de OAuth 2.0](authorization-code-flow.md).

En este flujo, la aplicación ejecuta [directivas](user-flow-overview.md) y recibe un elemento `authorization_code` de Azure AD después de que el usuario complete la directiva. El elemento `authorization_code` representa el permiso de la aplicación para llamar a servicios de back-end en nombre del usuario que inició la sesión. La aplicación podrá intercambiar el elemento `authorization_code` en segundo plano para `access_token` y `refresh_token`.  La aplicación puede usar el elemento `access_token` para autenticarse en una API web de back-end en las solicitudes HTTP. También puede usar el elemento `refresh_token` para obtener un nuevo `access_token` cuando caduca uno más antiguo.

## <a name="current-limitations"></a>Limitaciones actuales

### <a name="unsupported-application-types"></a>Tipos de aplicación no admitidos

#### <a name="daemonsserver-side-applications"></a>Demonios o aplicaciones del lado del servidor

Las aplicaciones que contienen procesos de larga duración o que funcionan sin la presencia de un usuario también necesitan un modo de acceder a los recursos protegidos, como las API web. Estas aplicaciones pueden autenticar y obtener tokens con la identidad de la aplicación (en lugar de una identidad delegada del usuario) mediante el flujo de credenciales de cliente de OAuth 2.0. El flujo de credenciales de cliente no es igual al flujo en nombre de y este no se debe usar para la autenticación de servidor a servidor.

Aunque actualmente el flujo de concesión de credenciales de cliente de OAuth 2.0 no es compatible directamente con el servicio de autenticación de Azure AD B2C, puede configurar el flujo de credenciales de cliente usando Azure AD y el punto de conexión de tokens o de la Plataforma de identidad de Microsoft (https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token) para una aplicación de su inquilino de Azure AD B2C. Un inquilino de Azure AD B2C comparte algunas funcionalidades con los inquilinos empresariales de Azure AD.

Para configurar el flujo de credenciales de cliente, consulte [Azure Active Directory v2.0 y el flujo de credenciales de cliente de OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Una autenticación correcta genera la recepción de un token con formato para que Azure AD lo pueda usar tal como se describe en [Referencia de tokens de Azure AD](../active-directory/develop/id-tokens.md).

Para obtener instrucciones sobre cómo registrar una aplicación de administración, consulte [Administrar Azure AD B2C con Microsoft Graph](microsoft-graph-get-started.md).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Cadenas de la API web (flujo en nombre de)

Muchas arquitecturas incluyen una API web que necesita llamar a otra API web de nivel inferior, ambas protegidas mediante Azure AD B2C. Este escenario es común en los clientes nativos que tienen un back-end de API web y llama a un servicio de Microsoft Online, como Microsoft Graph API.

Este escenario de API web encadenadas puede admitirse mediante la concesión de credenciales de portador JWT de OAuth 2.0, también conocido como flujo "en nombre de".  Sin embargo, el flujo "en nombre de" no está implementado actualmente en Azure AD B2C.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las directivas integradas proporcionadas. Para ello, consulte [Flujos de usuario en Azure Active Directory B2C](user-flow-overview.md).
