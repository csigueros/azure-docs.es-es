---
title: 'Configuración de la directiva de registro de MFA: Azure Active Directory Identity Protection'
description: Aprenda cómo configurar la directiva de registro de autenticación multifactor en Azure AD Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d2bfaef8f00f6246dfef17145666c817ed26d9d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747851"
---
# <a name="how-to-configure-the-azure-ad-multi-factor-authentication-registration-policy"></a>Instrucciones: Configuración de la directiva de registro de Azure AD Multi-Factor Authentication

Azure AD Identity Protection le ayuda a administrar la puesta en servicio del registro de Azure AD Multi-Factor Authentication (MFA) mediante la configuración de una directiva de acceso condicional para requerir el registro de MFA, independientemente de la aplicación de autenticación moderna en que inicie sesión.

## <a name="what-is-the-azure-ad-multi-factor-authentication-registration-policy"></a>¿Qué es la directiva de registro de Azure AD Multi-Factor Authentication?

Azure AD Multi-Factor Authentication proporciona un medio para verificar su identidad más allá del nombre de usuario y la contraseña. Proporciona una segunda capa de seguridad a los inicios de sesión de los usuarios. Para que los usuarios puedan responder a las solicitudes MFA, primero debe registrarse para Azure AD Multi-Factor Authentication.

Se recomienda requerir Azure AD Multi-Factor Authentication en los inicios de sesión de usuario por los siguientes motivos:

- Ofrece autenticación segura en una variedad de opciones de verificación.
- Desempeña un papel fundamental en la preparación de su autocorrección en caso de detecciones de riesgo en Identity Protection.

Para más información sobre Azure AD Multi-Factor Authentication, consulte [¿Qué es Azure AD Multi-Factor Authentication?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>Configuración de directivas

1. Acceda a [Azure Portal](https://portal.azure.com).
1. Vaya a **Azure Active Directory** > **Seguridad** > **Identity Protection** > **Directiva de registro de MFA**.
   1. En **Asignaciones**
      1. **Usuarios**: elija **Todos los usuarios** o **Seleccionar individuos y grupos** si limita el lanzamiento.
         1. Opcionalmente, puede optar por excluir usuarios de la directiva.
   1. **Aplicar directiva** - **Activado**
   1. **Guardar**

## <a name="user-experience"></a>Experiencia del usuario

Azure Active Directory Identity Protection solicitará a los usuarios que se registren la próxima vez que inicien sesión de forma interactiva y tendrán 14 días para completar el registro. Durante este período de 14 días, pueden omitir el registro si no se requiere MFA como condición, pero al final del período se les pedirá que se registren para poder completar el proceso de inicio de sesión.

Para obtener información general de la experiencia de usuario relacionada, consulte:

- [Experiencias de inicio de sesión con Azure AD Identity Protection](concept-identity-protection-user-experience.md).  

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación de las directivas de riesgo de inicios de sesión y de usuarios](howto-identity-protection-configure-risk-policies.md)

- [Habilitación del autoservicio de restablecimiento de contraseña de Azure AD](../authentication/howto-sspr-deployment.md)

- [Habilitación de Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)
