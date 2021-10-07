---
title: Tokens de seguridad | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre los aspectos básicos de los tokens de seguridad de la Plataforma de identidad de Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 2567a75c0accd7e2bb932d8578e1a225518f5ce3
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092763"
---
# <a name="security-tokens"></a>Tokens de seguridad

Un proveedor de identidades centralizado es especialmente útil para las aplicaciones que tienen usuarios repartidos por todo el mundo que no inician sesión necesariamente desde la red de la empresa. La Plataforma de identidad de Microsoft autentica a los usuarios y proporciona tokens de seguridad, como los [tokens de acceso](developer-glossary.md#access-token), los [tokens de actualización](developer-glossary.md#refresh-token)y los [tokens de id](developer-glossary.md#id-token). Los tokens de seguridad permiten a una [aplicación cliente](developer-glossary.md#client-application) acceder a recursos protegidos en un [servidor de recursos](developer-glossary.md#resource-server).

**Token de acceso**: un token de acceso es un token de seguridad emitido por un [servidor de autorización](developer-glossary.md#authorization-server) como parte de un flujo de [OAuth 2.0](active-directory-v2-protocols.md). Contiene información sobre el usuario y el recurso para el que está previsto el token. La información se puede usar para tener acceso a las API web y a otros recursos protegidos. Los recursos validan los tokens de acceso para conceder acceso a una aplicación cliente. Para más información sobre cómo emite la Plataforma de identidad de Microsoft los tokens de acceso, consulte [Tokens de acceso](access-tokens.md).

**Token de actualización**: Dado que los tokens de acceso solo son válidos durante un breve período de tiempo, los servidores de autorización a veces emiten un token de actualización al mismo tiempo que se emite el token de acceso. La aplicación cliente puede intercambiar este token de actualización por un nuevo token de acceso cuando es necesario. Para más información sobre cómo usa la plataforma de identidad de Microsoft los tokens de actualización para revocar permisos, consulte [Tokens de actualización](refresh-tokens.md).

**Token de id.** : Los tokens de id. se envían a la aplicación cliente como parte de un flujo de [OpenID Connect](v2-protocols-oidc.md). Se pueden enviar junto o en lugar de un token de acceso. El cliente usa los tokens de id. para autenticar al usuario. Para más información sobre cómo emite la Plataforma de identidad de Microsoft los tokens de id., consulte [Tokens de id](id-tokens.md).

> [!NOTE]
> En este artículo se describen los tokens de seguridad que usan los protocolos OAuth2 y OpenID Connect. Muchas aplicaciones empresariales usan SAML para autenticar a los usuarios. Para información sobre las aserciones de SAML, vea [Referencia de tokens SAML de Azure AD](reference-saml-tokens.md).

## <a name="validate-security-tokens"></a>Validación del token de seguridad

La validación del token corresponde a la aplicación para la que se generó el token, la aplicación web en la que inició sesión el usuario o la API web que se está llamando. El token está firmado por el servidor de autorización con una clave privada. El servidor de autorización publica la clave pública correspondiente. Para validar un token, la aplicación comprueba la firma utilizando la clave pública del servidor de autorización a fin de validar que la firma se creó con la clave privada.

Los tokens son válidos solo durante un período de tiempo limitado. Normalmente, el servidor de autorización proporciona un par de tokens, como:

* Un token de acceso, que accede a la aplicación o al recurso protegido.
* Un token de actualización, que se usa para actualizar el token de acceso cuando este está próximo a expirar.

Los tokens de acceso se pasan a una API web en el encabezado de `Authorization` como un token de portador. Una aplicación puede proporcionar un token de actualización al servidor de autorización. Si el acceso del usuario a la aplicación no se ha revocado, recibirá un nuevo token de acceso y un nuevo token de actualización. Así es cómo se administran los casos cuando alguien abandona la empresa. Cuando el servidor de autorización recibe el token de actualización, si el usuario ya no está autorizado, no emitirá otro token de acceso válido.

## <a name="json-web-tokens-and-claims"></a>Instancias de JSON Web Token y notificaciones

La Plataforma de identidad de Microsoft implementa los tokens de seguridad como instancias de JSON Web Tokens (JWT) que contienen *notificaciones*. Dado que los JWT se usan como tokens de seguridad, esta forma de autenticación se denomina a veces *autenticación de JWT*.

Una [notificación](developer-glossary.md#claim) proporciona aserciones sobre una entidad, como una aplicación cliente o un [propietario de recursos](developer-glossary.md#resource-owner), a otra entidad, como un servidor de recursos. También se puede hacer referencia a una notificación como una notificación de JWT o de JSON Web Token.

Las notificaciones son pares de nombres o valores que retransmiten datos sobre el firmante del token. Por ejemplo, una notificación puede contener datos sobre la entidad de seguridad autenticada por el servidor de autorización. Las notificaciones presentes en un token específico dependen de muchas cosas, entre las que se incluyen el tipo de token, el tipo de credencial usada para autenticar al firmante y la configuración de la aplicación.

Las aplicaciones pueden usar notificaciones para diversas tareas, como:

* Validar el token.
* Identificar el [inquilino](developer-glossary.md#tenant) del firmante del token.
* Mostrar información de usuario.
* Determinar la autorización del firmante.

Una notificación se compone de pares clave-valor que proporcionan información, como:

* El servidor de tokens de seguridad que generó el token.
* La fecha en la que se generó el token.
* El firmante (por ejemplo, el usuario, excepto en el caso de los demonios).
* La audiencia, que es la aplicación para la que se generó el token.
* La aplicación (el cliente) que solicitó el token. En el caso de las aplicaciones web, la aplicación puede ser la misma que la audiencia.

Para más información sobre cómo implementa la Plataforma de identidad de Microsoft los tokens y la información de notificaciones, consulte [Tokens de acceso](access-tokens.md) y [Tokens de id](id-tokens.md).

## <a name="how-each-flow-emits-tokens-and-codes"></a>Cómo emite cada flujo los tokens y los códigos

En función de cómo se compile el cliente, puede usar uno o varios de los flujos de autenticación admitidos por la Plataforma de identidad de Microsoft. Estos flujos pueden generar varios tokens (tokens de id., tokens de actualización, tokens de acceso) y códigos de autorización. Requieren distintos tokens para que funcionen. En esta tabla se proporciona información general.

|Flujo | Requiere | token de identificador | Access token | Token de actualización | Código de autorización |
|-----|----------|----------|--------------|---------------|--------------------|
|[Flujo de código de autorización](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Flujo implícito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Flujo de OIDC híbrido](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition)| | x  | |          |            x   |
|[Redención de token de actualización](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Token de actualización | x | x | x| |
|[Flujo en nombre de](v2-oauth2-on-behalf-of-flow.md) | Access token| x| x| x| |
|[Credenciales de cliente](v2-oauth2-client-creds-grant-flow.md) | | | x (solo aplicación)| | |

Los tokens emitidos a través del modo implícito tienen una limitación de longitud porque se pasan al explorador a través de la dirección URL (donde `response_mode` es `query` o `fragment`). Algunos exploradores tienen un límite en el tamaño de la dirección URL que se puede colocar en la barra del explorador y producen un error cuando es demasiado larga. Como resultado, estos tokens no tienen notificaciones `groups` o `wids`.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la autenticación y la autorización en la Plataforma de identidad de Microsoft, consulte los siguientes artículos:

* Para obtener información sobre los conceptos básicos de la autenticación y la autorización, vea [Autenticación frente a autorización](authentication-vs-authorization.md).
* Para obtener información sobre cómo registrar la aplicación para la integración, vea [Modelo de aplicación](application-model.md).
* Para obtener información sobre el flujo de inicio de sesión de las aplicaciones web, de escritorio y móviles, vea [Flujo de inicio de sesión de aplicaciones](app-sign-in-flow.md).
