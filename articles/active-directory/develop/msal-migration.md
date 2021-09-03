---
title: Migración a la Biblioteca de autenticación de Microsoft (MSAL)
titleSuffix: Microsoft identity platform
description: Conozca las diferencias entre la Biblioteca de autenticación de Microsoft (MSAL) y la Biblioteca de Autenticación de Azure AD (ADAL) y cómo migrar a MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 145302d3569c2fc9c5d5bd58a5f9b7ccefac0b8e
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418525"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>Migración de aplicaciones a la Biblioteca de autenticación de Microsoft (MSAL)

Si alguna de las aplicaciones usa la Biblioteca de autenticación de Azure Active Directory (ADAL) para la funcionalidad de autenticación y autorización, es el momento de migrarlas a la [Biblioteca de autenticación de Microsoft (MSAL)](msal-overview.md#languages-and-frameworks).

- Todo el soporte técnico y desarrollo de Microsoft para ADAL, incluidas las correcciones de seguridad, finaliza el 30 de junio de 2022.
- No se han agregado nuevas características a ADAL desde el 30 de junio de 2020.

> [!WARNING]
> Si decide no migrar a MSAL antes de que finalice el soporte técnico de ADAL el 30 de junio de 2022, pone en riesgo la seguridad de la aplicación. Las aplicaciones existentes que usan ADAL seguirán funcionando después de la fecha de finalización del soporte técnico, pero Microsoft ya no lanzará correcciones de seguridad en ADAL.

## <a name="why-switch-to-msal"></a>¿Por qué cambiar a MSAL?

MSAL proporciona varias ventajas con respecto a ADAL, incluidas las siguientes características: 

|Características|MSAL|ADAL|
|---------|---------|---------|
|**Seguridad**|||
|Correcciones de seguridad posteriores al 30 de junio de 2022|![Correcciones de seguridad posteriores al 30 de junio de 2022: MSAL proporciona la característica][y]|![Correcciones de seguridad posteriores al 30 de junio de 2022: ADAL no proporciona la característica][n]|
| Actualice y revoque los tokens de forma proactiva en función de la directiva o los eventos críticos para Microsoft Graph y otras API que admiten la [Evaluación continua de acceso (CAE)](app-resilience-continuous-access-evaluation.md).|![Actualice y revoque los tokens de forma proactiva en función de la directiva o los eventos críticos para Microsoft Graph y otras API que admiten la Evaluación continua de acceso (CAE); MSAL ofrece la característica.][y]|![Actualice y revoque los tokens de forma proactiva en función de la directiva o los eventos críticos para Microsoft Graph y otras API que admiten la Evaluación continua de acceso (CAE); ADAL no ofrece la característica.][n]|
| Estándares compatibles con OAuth v2.0 y OpenID Connect (OIDC) |![Estándares compatibles con OAuth v2.0 y OpenID Connect (OIDC); MSAL proporciona la característica.][y]|![Estándares compatibles con OAuth v2.0 y OpenID Connect (OIDC); ADAL no proporciona la característica.][n]|
|**Cuentas de usuario y experiencias**|||
|Cuentas de Azure Active Directory (Azure AD)|![Cuentas de Azure Active Directory (Azure AD); MSAL proporciona la característica.][y]|![Cuentas de Azure Active Directory (Azure AD); ADAL proporciona la característica.][y]|
| Cuenta de Microsoft (MSA) |![Cuenta de Microsoft (MSA); MSAL proporciona la característica.][y]|![Cuenta de Microsoft (MSA); ADAL no proporciona la característica.][n]|
| Cuentas de Azure AD B2C |![Cuentas de Azure AD B2C; MSAL proporciona la característica.][y]|![Cuentas de Azure AD B2C; ADAL no proporciona la característica.][n]|
| Mejor experiencia de inicio de sesión único |![Mejor experiencia de inicio de sesión único; MSAL proporciona la característica.][y]|![Mejor experiencia de inicio de sesión único; ADAL no proporciona la característica.][n]|
|**Resistencia**|||
| Renovación proactiva de tokens |![Renovación proactiva de tokens; MSAL proporciona la característica.][y]|![Renovación proactiva de tokens; ADAL no proporciona la característica.][n]|
| Limitaciones |![Limitaciones; MSAL proporciona la característica.][y]|![Limitaciones; ADAL no proporciona la característica.][n]|

## <a name="ad-fs-support-in-msalnet"></a>Compatibilidad con AD FS en MSAL.NET

Puede usar MSAL.NET, MSAL Java y MSAL Python para obtener tokens de Servicios de federación de Active Directory (AD FS) 2019 o posterior. MSAL no admite las versiones anteriores de AD FS, incluido AD FS 2016.

Si necesita seguir usando AD FS, debe actualizar a AD FS 2019 o posterior antes de actualizar las aplicaciones de ADAL a MSAL.

## <a name="how-to-migrate-to-msal"></a>Migración a MSAL

Antes de iniciar la migración, debe identificar qué aplicaciones usan ADAL para la autenticación. Siga los pasos de este artículo para obtener una lista mediante Azure Portal:
- [Obtención de una lista completa de aplicaciones que usan ADAL en el inquilino](howto-get-list-of-all-active-directory-auth-library-apps.md)

Después de identificar las aplicaciones que usan ADAL, mígrelas a MSAL en función del tipo de aplicación, como se muestra a continuación.

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="migration-help"></a>Ayuda para la migración

Si tiene alguna pregunta sobre cómo migrar la aplicación de ADAL a MSAL, estas son algunas opciones:

- Publique su pregunta en [Microsoft Q&A](/answers/topics/azure-ad-adal-deprecation.html) y etiquétela con `[azure-ad-adal-deprecation]`.
- Abra una incidencia en el repositorio de GitHub de la biblioteca. Vea la sección [Lenguajes y plataformas](msal-overview.md#languages-and-frameworks) del artículo información general de MSAL para obtener vínculos al repositorio de cada biblioteca.

Si se asoció con un proveedor de software independiente (ISV) en el desarrollo de la aplicación, se recomienda ponerse en contacto directamente con él para conocer la trayectoria de migración a MSAL.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre MSAL, incluida la información de uso y las bibliotecas disponibles para diferentes lenguajes de programación y tipos de aplicación, consulte:

- [Adquisición y almacenamiento en caché de tokens con MSAL](msal-acquire-cache-tokens.md)
- [Opciones de configuración de aplicaciones](msal-client-application-configuration.md)
- [Bibliotecas de autenticación de MSAL](reference-v2-libraries.md)

<!--
 ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->
[y]: media/common/yes.png
[n]: media/common/no.png