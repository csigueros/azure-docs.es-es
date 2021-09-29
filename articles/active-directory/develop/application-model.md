---
title: Modelo de aplicación | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información sobre el proceso de registro de la aplicación para que pueda integrarse con la Plataforma de identidad de Microsoft.
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
ms.openlocfilehash: 5b2f62b0adc040b5f20b341457789378f1aa983a
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154173"
---
# <a name="application-model"></a>Modelo de aplicación

Las aplicaciones pueden iniciar por sí mismas la sesión de los usuarios o delegar el inicio de sesión en un proveedor de identidades. En este artículo se describen los pasos necesarios para registrar una aplicación en la Plataforma de identidad de Microsoft.

## <a name="register-an-application"></a>Registro de una aplicación

Para que un proveedor de identidades sepa que un usuario tiene acceso a una determinada aplicación, tanto el usuario como la aplicación deben estar registrados con el proveedor de identidades. Cuando registra la aplicación en Azure Active Directory (Azure AD), proporciona una configuración de identidad para la aplicación, lo que permite integrarla en la Plataforma de identidad de Microsoft. El registro de la aplicación también le permite:

* Personalizar la marca de la aplicación en el cuadro de diálogo de inicio de sesión. Esto es importante, ya que el inicio de sesión es la primera experiencia que tendrá un usuario con la aplicación.
* Decidir si desea permitir que los usuarios únicamente puedan iniciar sesión si pertenecen a su organización. Esta arquitectura se conoce como una aplicación de un solo inquilino. O bien, puede permitir que los usuarios inicien sesión con cualquier cuenta profesional o educativa, lo que se conoce como una aplicación de varios inquilinos. También puede permitir cuentas Microsoft personales o una cuenta de las redes sociales: LinkedIn, Google, etc.
* Solicitar permisos de ámbito. Por ejemplo, puede solicitar el ámbito "user.read", que concede permiso para leer el perfil del usuario conectado.
* Definir ámbitos que definan el acceso a la API web. Normalmente, cuando una aplicación desea acceder a la API, tiene que solicitar permisos para los ámbitos que se definen.
* Compartir un secreto con la Plataforma de identidad de Microsoft que demuestra la identidad de la aplicación. Usar un secreto es pertinente en el caso de una aplicación cliente confidencial. Una aplicación cliente confidencial es una aplicación que puede almacenar las credenciales de forma segura. Se necesita un servidor back-end de confianza para almacenar las credenciales.

Después de registrar la aplicación, se le proporcionará un identificador único que compartirá con la Plataforma de identidad de Microsoft cuando solicite tokens. Si se trata de una [aplicación cliente confidencial](developer-glossary.md#client-application), también compartirá el secreto o la clave pública, en función de si se utilizaron certificados o secretos.

La Plataforma de identidad de Microsoft representa a las aplicaciones que usan un modelo que cumple dos funciones principales:

* Identificar la aplicación mediante los protocolos de autenticación que admite.
* Proporcionar todos los identificadores, las direcciones URL, los secretos y la información relacionada que se necesitan para la autenticación.

La Plataforma de identidad de Microsoft:

* Contiene todos los datos necesarios para admitir la autenticación en tiempo de ejecución.
* Contiene todos los datos para decidir qué recursos podría necesitar una aplicación para obtener acceso y en qué circunstancias debería cumplimentarse una solicitud.
* Proporciona la infraestructura necesaria para implementar el aprovisionamiento de la aplicación dentro del inquilino del desarrollador de la aplicación y en cualquier otro inquilino de Azure AD.
* Controla el consentimiento del usuario en el momento de solicitud del token y facilita el aprovisionamiento dinámico de aplicaciones entre inquilinos.

El *consentimiento* es el proceso de un propietario de recursos para conceder autorización a una aplicación cliente para acceder a recursos protegidos, bajo permisos específicos, en nombre del propietario del recurso. La Plataforma de identidad de Microsoft permite que:

* Los usuarios y administradores concedan o denieguen dinámicamente el consentimiento para que la aplicación acceda a recursos en su nombre.
* Los administradores decidan qué pueden hacer las aplicaciones en última instancia, qué usuarios pueden usar aplicaciones específicas y cómo se accede a los recursos de directorio.

## <a name="multi-tenant-apps"></a>Aplicaciones multiinquilino

En la Plataforma de identidad de Microsoft, un [objeto de aplicación](developer-glossary.md#application-object) describe una aplicación. Durante la implementación, la Plataforma de identidad de Microsoft usa el objeto de aplicación como plano técnico para crear una [entidad de servicio](developer-glossary.md#service-principal-object), que representa una instancia concreta de una aplicación de un directorio o inquilino. La entidad de servicio define qué es lo que puede hacer realmente la aplicación en un directorio de destino específico, quién puede usarla, a qué recursos tiene acceso, etc. La Plataforma de identidad de Microsoft crea una entidad de servicio a partir de un objeto de aplicación mediante su [consentimiento](developer-glossary.md#consent).

En el siguiente diagrama se muestra un flujo de aprovisionamiento de la plataforma de identidad de Microsoft basado en el consentimiento. Aparecen dos inquilinos: *A* y *B*.

* El *inquilino A* es el propietario de la aplicación.
* El *inquilino B* crea instancias de la aplicación mediante una entidad de servicio.

![Diagrama que muestra un flujo de aprovisionamiento simplificado basado en el consentimiento.](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

En este flujo de aprovisionamiento:

1. Un usuario del inquilino B intenta iniciar sesión con la aplicación. El punto de conexión de autorización solicita un token para la aplicación.
1. Se adquieren y verifican las credenciales de usuario para la autenticación.
1. El usuario debe dar su consentimiento para que la aplicación tenga acceso al inquilino B.
1. La Plataforma de identidad de Microsoft usa el objeto de aplicación del inquilino A como plano técnico para crear una entidad de servicio en el inquilino B.
1. El usuario recibe el token solicitado.

Puede repetir este proceso con más inquilinos. El inquilino A conserva el plano técnico de la aplicación (objeto de aplicación). Los usuarios y administradores de todos los demás inquilinos en los que se concede consentimiento a la aplicación conservan el control sobre lo que la aplicación puede hacer con el objeto de la entidad de servicio correspondiente de cada inquilino. Para más información, consulte [Objetos de aplicación y de entidad de servicio en la Plataforma de identidad de Microsoft](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la autenticación y la autorización en la Plataforma de identidad de Microsoft, consulte los siguientes artículos:

* Para obtener información sobre los conceptos básicos de la autenticación y la autorización, vea [Autenticación frente a autorización](authentication-vs-authorization.md).
* Para saber cómo se usan los tokens de acceso, los tokens de actualización y los tokens de id. en la autenticación y la autorización, consulte [Tokens de seguridad](security-tokens.md).
* Para obtener información sobre el flujo de inicio de sesión de las aplicaciones web, de escritorio y móviles, vea [Flujo de inicio de sesión de aplicaciones](app-sign-in-flow.md).

Para más información sobre el modelo de aplicación, consulte los siguientes artículos:

* Para más información sobre los objetos de aplicación y las entidades de servicio en la Plataforma de identidad de Microsoft, consulte [Cómo y por qué se agregan aplicaciones a Azure AD](active-directory-how-applications-are-added.md).
* Para más información sobre las aplicaciones de un solo inquilino y las aplicaciones de varios inquilinos, consulte [Inquilinos en Azure Active Directory](single-and-multi-tenant-apps.md).
* Para más información sobre cómo Azure AD también proporciona Azure Active Directory B2C para que las organizaciones puedan iniciar sesión de los usuarios, normalmente clientes, con identidades sociales como una cuenta de Google, consulte la [documentación de Azure Active Directory B2C](../../active-directory-b2c/index.yml).
