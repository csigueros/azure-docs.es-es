---
title: Configuración de identidades de Azure Active Directory para el servicio de FHIR de Azure Healthcare APIs
description: Conozca los principios de identidad, autenticación y autorización del servicio de FHIR.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: cavoeg
ms.openlocfilehash: e38295a306e41dee6b92df7839f8f10878535ea1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780647"
---
# <a name="azure-active-directory-identity-configuration-for-fhir-service"></a>Configuración de identidades de Azure Active Directory para el servicio de FHIR

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

Al trabajar con datos del sector sanitario, es importante asegurarse de que estén protegidos y de que los usuarios o las aplicaciones no autorizados no puedan acceder a ellos. Los servidores de FHIR usan [OAuth 2.0](https://oauth.net/2/) para garantizar esta seguridad de datos. El servicio de FHIR se protege mediante [Azure Active Directory](../../active-directory/index.yml), que es un ejemplo de un proveedor de identidades de OAuth 2.0. En este artículo se proporciona información general sobre la autorización de un servidor de FHIR y los pasos necesarios para obtener un token para acceder a dicho servidor. Aunque estos pasos se aplican a cualquier servidor de FHIR y a cualquier proveedor de identidades, en este artículo se utiliza el servicio de FHIR de Azure Healthcare APIs y Azure Active Directory (Azure AD) como proveedor de identidades.

## <a name="access-control-overview"></a>Introducción al control de acceso

Para que una aplicación cliente acceda al servicio de FHIR, debe presentar un token de acceso. El token de acceso es una colección de propiedades (notificaciones) codificada [Base64](https://en.wikipedia.org/wiki/Base64) y firmada que proporcionan información sobre la identidad del cliente y los roles y privilegios que se conceden a este.

Hay muchas maneras de obtener un token, pero para el servicio de FHIR no importa cómo se obtenga, siempre que sea un token adecuadamente firmado con las notificaciones correctas. 

Con el [flujo de código de autorización](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) como ejemplo, el acceso a un servidor de FHIR pasa por los cuatro pasos siguientes:

![Authorization de FHIR](media/azure-active-directory-fhir-service/fhir-authorization.png)

1. El cliente envía una solicitud al punto de conexión `/authorize` de Azure AD. Azure AD redirigirá al cliente a una página de inicio de sesión donde el usuario se autenticará mediante las credenciales apropiadas (por ejemplo, el nombre de usuario y la contraseña o la autenticación en dos fases). Vea los detalles sobre la [obtención de un código de autorización](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#request-an-authorization-code). Después de que la autenticación se haya realizado correctamente, se devuelve un *código de autorización* al cliente. Azure AD solo permitirá que se devuelva este código de autorización a una dirección URL de respuesta registrada configurada en el registro de la aplicación cliente (consulte la información que figura a continuación).
1. La aplicación cliente intercambia el código de autorización para un *token de acceso* en el punto de conexión `/token` de Azure AD. Al solicitar un token, es posible que la aplicación cliente tenga que proporcionar un secreto de cliente (la contraseña de aplicaciones). Consulte los detalles sobre la [obtención de un token de acceso](../../active-directory/azuread-dev/v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token).
1. El cliente realiza una solicitud al servicio de FHIR, por ejemplo `GET /Patient`, para buscar todos los pacientes. Al efectuar la solicitud, incluye el token de acceso en un encabezado de solicitud HTTP, por ejemplo `Authorization: Bearer eyJ0e...`, donde `eyJ0e...` representa el token de acceso con codificación Base64.
1. El servicio de FHIR valida que el token contenga las notificaciones adecuadas (propiedades del token). Si todo concuerda, se completará la solicitud y se devolverá un conjunto de FHIR con los resultados al cliente.

Es importante tener en cuenta que el servicio de FHIR no está implicado en la validación de las credenciales de usuario ni emite el token. Azure AD lleva a cabo la autenticación y la creación del token. El servicio de FHIR simplemente valida que el token esté firmado correctamente (que sea auténtico) y que tenga las notificaciones adecuadas.

## <a name="structure-of-an-access-token"></a>Estructura de un token de acceso

El desarrollo de aplicaciones de FHIR a menudo implica la depuración de problemas de acceso. Si a un cliente se le deniega el acceso al servicio de FHIR, es útil entender la estructura del token de acceso y cómo se puede decodificar para inspeccionar el contenido (las notificaciones) del token. 

Normalmente, los servidores de FHIR esperan un método [JSON Web Token](https://en.wikipedia.org/wiki/JSON_Web_Token) (JWT, a veces pronunciado "jot"). Consta de tres partes:

**Parte 1**: un encabezado, que podría tener este aspecto:
    ```json
    {
      "alg": "HS256",
      "typ": "JWT"
    }
    ```

**Parte 2**: la carga (las notificaciones), por ejemplo:
    ```json
    {
     "oid": "123",
     "iss": "https://issuerurl",
     "iat": 1422779638,
     "roles": [
        "admin"
      ]
    }
    ```

**Parte 3**: una firma, que se calcula concatenando el contenido con codificación Base 64 del encabezado y la carga, y calculando un hash criptográfico de ellos en función del algoritmo (`alg`) especificado en el encabezado. Un servidor podrá obtener claves públicas del proveedor de identidades y validar que este token lo emitió un proveedor de identidades específico y no se ha manipulado.

El token completo consta de las versiones con codificación Base64 (realmente codificadas en URL como Base64) de esos tres segmentos. Los tres segmentos se concatenan y se separan con `.` (punto).

A continuación se muestra un token de ejemplo:

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJvaWQiOiIxMjMiLCAiaXNzIjoiaHR0cHM6Ly9pc3N1ZXJ1cmwiLCJpYXQiOjE0MjI3Nzk2MzgsInJvbGVzIjpbImFkbWluIl19.gzSraSYS8EXBxLN_oWnFSRgCzcmJmMjLiuyu5CSpyHI
```

El token se puede descodificar e inspeccionar con herramientas como [https://jwt.ms](https://jwt.ms). El resultado de descodificar el token es:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "oid": "123",
  "iss": "https://issuerurl",
  "iat": 1422779638,
  "roles": [
    "admin"
  ]
}.[Signature]
```

## <a name="obtaining-an-access-token"></a>Obtención de un token de acceso

Como se mencionó anteriormente, hay varias maneras de obtener un token desde Azure AD. Esas formas se describen en detalle en la [documentación para desarrolladores de Azure AD](../../active-directory/develop/index.yml).

Azure AD tiene dos versiones diferentes de los puntos de conexión OAuth 2.0, a las que se hace referencia como `v1.0` y `v2.0`. Ambas versiones son puntos de conexión de OAuth 2.0 y las designaciones `v1.0` y `v2.0` hacen referencia a las diferencias en el modo en que Azure AD implementa es estándar. 

Al usar un servidor de FHIR, puede usar los puntos de conexión `v1.0` o `v2.0`. La elección puede depender de las bibliotecas de autenticación que se usan en la aplicación cliente.

Las secciones pertinentes de la documentación de Azure AD son:

* Punto de conexión `v1.0`:
    * [Flujo de código de autorización](../../active-directory/azuread-dev/v1-protocols-oauth-code.md).
    * [Flujo de credenciales de cliente](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md).
* Punto de conexión `v2.0`:
    * [Flujo de código de autorización](../../active-directory/develop/v2-oauth2-auth-code-flow.md).
    * [Flujo de credenciales de cliente](../../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).

Hay otras variaciones (por ejemplo, en nombre del flujo) para obtener un token. Vea la documentación de Azure AD para más detalles. Al usar el servicio de FHIR, también hay algunos accesos directos para obtener un token de acceso (con fines de depuración) [mediante la CLI de Azure](get-healthcare-apis-access-token-cli.md).

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido algunos de los conceptos básicos implicados en la protección del acceso al servicio de FHIR mediante Azure AD. Para obtener información sobre cómo implementar el servicio de FHIR, consulte

>[!div class="nextstepaction"]
>[Implementación de un servicio de FHIR en Azure Healthcare APIs mediante el portal](fhir-portal-quickstart.md)