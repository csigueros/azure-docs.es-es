---
title: Protocolos de autenticación en Azure Active Directory B2C | Microsoft Docs
description: Creación de aplicaciones directamente mediante los protocolos que admite Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a9a79322439db33620b67ff568387a5750f7ee5c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350588"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Protocolos de autenticación
Azure Active Directory B2C (Azure AD B2C) proporciona identidad como servicio para sus aplicaciones gracias a la compatibilidad con dos protocolos estándar del sector: OpenID Connect y OAuth 2.0. Aunque el servicio cumple con la norma, pueden existir diferencias sutiles entre dos implementaciones cualesquiera de estos protocolos.

La información de esta guía es útil si el código se escribe mediante el envío y el control directo de solicitudes HTTP, en lugar de usar una biblioteca de código abierto. Se recomienda que lea esta página antes de profundizar en los detalles de cada protocolo específico. Pero si ya está familiarizado con Azure AD B2C, puede ir directamente a [las guías de referencia de protocolo](#protocols).

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Conceptos básicos
Todas las aplicaciones que usen Azure AD B2C deben estar registrada en su directorio B2C del [Portal de Azure](https://portal.azure.com). El proceso de registro de la aplicación recopila y asigna algunos valores a la aplicación:

* Un **identificador de la aplicación** que identifica la aplicación de forma única.
* Un **URI de redirección** o un **identificador de paquete** que pueda usarse para devolver las respuestas a la aplicación.
* Algunos otros valores específicos de cada escenario. Para obtener más información, consulte [Cómo registrar la aplicación](tutorial-register-applications.md).

Una vez registrada, la aplicación se comunica con Azure Active Directory (Azure AD) mediante el envío de solicitudes al punto de conexión:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

En casi todos los flujos de OAuth y OpenID Connect hay cuatro partes implicadas en el intercambio:




:::image type="content" source="./media/protocols-overview/protocols_roles.png" alt-text="Diagrama que muestra los cuatro roles de OAuth 2.0.":::

* El **servidor de autorización** es el punto de conexión de Azure AD. Controla de manera segura todo lo relacionado con el acceso y la información de usuario. También controla las relaciones de confianza entre las partes de un flujo. Es responsable de garantizar la identidad del usuario, conceder y revocar el acceso a los recursos y emitir tokens. También es conocido como proveedor de identidades.

* El **propietario del recurso** suele ser el usuario final. Es la parte que posee los datos y tiene la capacidad de permitir que terceros tengan acceso a esos datos o recursos.

* El **cliente OAuth** es su aplicación. Se identifica mediante su identificador de aplicación. Suele ser la entidad con la que interactúan los usuarios finales. También solicita tokens del servidor de autorizaciones. El propietario del recurso debe conceder al cliente el permiso para tener acceso al recurso.

* El **servidor de recursos** es el lugar en que residen el recurso o los datos. Confía en el servidor de autorizaciones para autenticar y autorizar de forma segura al cliente OAuth. También usa tokens de acceso de portador para asegurarse de que se puede conceder el acceso a un recurso.

## <a name="policies-and-user-flows"></a>Directivas y flujos de usuario
Puede decirse que las directivas de Azure AD B2C son las características más importantes del servicio. Azure AD B2C amplía los protocolos estándar de OAuth 2.0 y OpenID Connect mediante la introducción de las directivas. Estas permiten que Azure AD B2C realice tareas que van más allá de la mera autenticación y autorización.

Para ayudarle a configurar las tareas más comunes de identidad, el portal de Azure AD B2C incluye directivas predefinidas y configurables denominadas **flujos de usuario**. Los flujos de usuario describen por completo las experiencias de identidad del consumidor, como el registro, el inicio de sesión y la edición de perfiles. Los flujos de usuario se pueden definir en una interfaz de usuario administrativa. Se pueden ejecutar mediante un parámetro de consulta especial en solicitudes de autenticación de HTTP.

Las directivas y los flujos de usuario no son características estándar de OAuth 2.0 y OpenID Connect, así que debe dedicar tiempo a entenderlas. Para más información, consulte la [guía de referencia de flujos de usuario de Azure AD B2C](user-flow-overview.md).

## <a name="tokens"></a>Tokens
La implementación de Azure AD B2C de OAuth 2.0 y OpenID Connect hace un uso generalizado de tokens de portador, incluidos los representados como tokens web JSON (JWT). Un token de portador es un token de seguridad ligero que concede al "portador" acceso a un recurso protegido.

El "portador" es cualquier parte que pueda presentar el token. Para que una parte pueda recibir un token de portador, es necesario que Azure AD la autentique previamente. Pero si no se realizan los pasos necesarios para proteger el token durante la transmisión y el almacenamiento, puede ser interceptado y usado por usuarios no previstos.

Algunos tokens de seguridad tienen mecanismos integrados que impiden que partes no autorizadas puedan usarlos, pero los tokens de portador no disponen de este mecanismo. Deberán ser transportados en un canal seguro, como la seguridad de la capa de transporte (HTTPS).

Si un token de portador se transmite fuera de un canal seguro, un usuario malintencionado puede utilizar un ataque de tipo "man in the middle" para adquirir el token y usarlo para obtener acceso sin autorización a un recurso protegido. Los mismos principios de seguridad se aplican cuando los tokens de portador se almacenan o guardan en caché para su uso posterior. Asegúrate siempre de que la aplicación transmite y almacena los tokens de portador de manera segura.

Para ver más consideraciones de seguridad relativas a los tokens de portador, consulte la [sección 5 de RFC 6750](https://tools.ietf.org/html/rfc6750).

En [Azure AD B2C: referencia de tokens](tokens-overview.md) encontrará más información sobre los distintos tipos de tokens que se usan en Azure AD B2C.

## <a name="protocols"></a>Protocolos
Cuando esté listo para revisar algunas solicitudes de ejemplo, puede comenzar con uno de los siguientes tutoriales. Cada uno de ellos corresponde a un escenario de autenticación determinado. Si necesita ayuda para determinar qué flujo es el más apropiado en su caso, consulte [los tipos de aplicaciones que puede compilar con Azure AD B2C](application-types.md).

* [Creación de aplicaciones móviles nativas mediante OAuth 2.0](authorization-code-flow.md)
* [Creación de aplicaciones web mediante OpenID Connect](openid-connect.md)
* [Creación de aplicaciones de una sola página con el flujo implícito de OAuth 2.0](implicit-flow-single-page-application.md)

