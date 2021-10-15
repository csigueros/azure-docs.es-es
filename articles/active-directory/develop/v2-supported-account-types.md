---
title: Tipos de cuenta admitidos | Azure
titleSuffix: Microsoft identity platform
description: Documentación conceptual sobre audiencias y tipos de cuenta admitidos en aplicaciones
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: b72e911631bc15402cf504655426c63cd26a69d6
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129231736"
---
# <a name="supported-account-types"></a>Tipos de cuenta admitidos

En este artículo se explica qué tipos de cuentas (a veces denominados *audiencias*) se admiten en aplicaciones de la Plataforma de identidad de Microsoft.

<!-- This section can be in an include for many of the scenarios (SPA, web app signing-in users, protecting a web API, Desktop (depending on the flows), Mobile -->

## <a name="account-types-in-the-public-cloud"></a>Tipos de cuentas en la nube pública

En la nube pública de Microsoft Azure, la mayoría de los tipos de aplicaciones pueden iniciar la sesión de los usuarios con cualquier audiencia:

- Si está escribiendo una aplicación de línea de negocio (LOB), puede iniciar la sesión de los usuarios en su propia organización. Este tipo de aplicación a veces se denomina *inquilino único*.
- Si es un ISV, puede escribir una aplicación que inicie la sesión de los usuarios.

  - En cualquier organización. Este tipo de aplicación se denomina aplicación web *multiinquilino*. A veces leerá que se inicia la sesión de los usuarios con sus cuentas profesionales o educativas.
  - Con la cuenta Microsoft personal o profesional o educativa.
  - Con solo la cuenta personal de Microsoft.
    
- Si está escribiendo una aplicación de empresa a consumidor, también puede iniciar la sesión de los usuarios con sus identidades sociales, por medio de Azure Active Directory B2C (Azure AD B2C).

## <a name="account-type-support-in-authentication-flows"></a>Compatibilidad de los tipos de cuentas en los flujos de autenticación

No es posible usar algunos tipos de cuenta con determinados flujos de autenticación. Por ejemplo, en aplicaciones de escritorio, UWP o de demonio:

- Las aplicaciones de demonio solo se pueden usar con organizaciones de Azure AD. No tiene sentido intentar usar aplicaciones de demonio para manipular cuentas personales de Microsoft. Nunca se concederá el consentimiento del administrador.
- Solo puede usar el flujo de autenticación integrada de Windows con cuentas profesionales o educativas (en su organización o en cualquier otra). La autenticación integrada de Windows funciona con cuentas de dominio, y es necesario que las máquinas estén unidas a un dominio o a Azure AD. Este flujo no tiene sentido para cuentas personales de Microsoft.
- La [concesión de credenciales de contraseña de propietario del recurso](./v2-oauth-ropc.md) (nombre de usuario/contraseña) no puede utilizarse con cuentas personales de Microsoft. Las cuentas personales de Microsoft requieren que el usuario consienta para obtener acceso a los recursos personales en cada inicio de sesión. Por este motivo, este comportamiento no es compatible con los flujos no interactivos.

## <a name="account-types-in-national-clouds"></a>Tipos de cuenta en nubes nacionales

Las aplicaciones también pueden iniciar la sesión de los usuarios en [nubes nacionales](authentication-national-cloud.md). Sin embargo, las cuentas personales de Microsoft no se admiten en estas nubes. Es por eso que los tipos de cuentas admitidos se reducen, para estas nubes, a su organización (inquilino único) o a cualquier organización (aplicaciones multiinquilino).

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [arrendamiento en Azure Active Directory](./single-and-multi-tenant-apps.md).
- Más información sobre las [nubes nacionales](./authentication-national-cloud.md).
