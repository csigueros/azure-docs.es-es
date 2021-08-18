---
title: Directivas de Azure AD Identity Protection
description: Identificación de las tres directivas que se habilitan con Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8f1b31c8f7698e395c4944d0c6283e6e6257c2d2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727657"
---
# <a name="identity-protection-policies"></a>Directivas de Identity Protection

Azure Active Directory Identity Protection incluye tres directivas predeterminadas que los administradores pueden optar por habilitar. Estas directivas incluyen una personalización limitada, pero son aplicables a la mayoría de las organizaciones. Todas las directivas permiten excluir a usuarios, como las [cuentas de administrador de acceso de emergencia](../roles/security-emergency-access.md).

![Directivas de Identity Protection](./media/concept-identity-protection-policies/identity-protection-policies.png)

## <a name="azure-ad-mfa-registration-policy"></a>Directiva de registro de Azure AD MFA

Identity Protection puede ayudar a las organizaciones a implementar Azure AD Multi-Factor Authentication (MFA) mediante una directiva de acceso condicional que requiere registro al iniciar sesión. La habilitación de esta directiva es una excelente manera de asegurarse de que los nuevos usuarios de la organización se hayan registrado en MFA el primer día. La autenticación multifactor es uno de los métodos de corrección automática para los eventos de riesgo dentro de Identity Protection. La corrección automática permite que los usuarios realicen acciones por su cuenta para reducir el volumen de llamadas al departamento de soporte técnico.

Encuentre más información sobre Azure AD Multi-Factor Authentication en el artículo [Cómo funciona: Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

## <a name="sign-in-risk-policy"></a>Directiva de riesgo de inicio de sesión

Identity Protection analiza las señales de cada inicio de sesión, tanto en tiempo real como sin conexión, y calcula una puntuación de riesgo en función de la probabilidad de que el usuario no haya realizado el inicio de sesión. Los administradores pueden tomar una decisión basada en esta señal de puntuación de riesgo para aplicar los requisitos de la organización. Los administradores pueden elegir bloquear el acceso, permitir el acceso o permitir el acceso pero requerir autenticación multifactor.

Si se detecta un riesgo, los usuarios pueden realizar el proceso de autenticación multifactor para solucionar automáticamente el evento de inicio de sesión peligroso y cerrarlo para evitar ruidos innecesarios para los administradores.

> [!NOTE] 
> Los usuarios deben haberse registrado previamente para Azure AD Multi-Factor Authentication antes de desencadenar la directiva de riesgo de inicio de sesión.

### <a name="custom-conditional-access-policy"></a>Directiva de acceso condicional personalizada

Los administradores también pueden optar por crear una directiva de acceso condicional personalizada que incluya el riesgo de inicio de sesión como una condición de asignación. Puede encontrar más información sobre el riesgo como una condición en una directiva de acceso condicional en el artículo [Acceso condicional: Condiciones](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk)

![Directiva de riesgo de inicio de sesión de acceso condicional personalizada](./media/concept-identity-protection-policies/identity-protection-custom-sign-in-policy.png)

## <a name="user-risk-policy"></a>Directiva de riesgo de usuario

Identity Protection puede calcular los niveles normales del comportamiento de un usuario y usarlos para basar las decisiones de su riesgo. El riesgo del usuario es un cálculo de la probabilidad de que se haya puesto en peligro una identidad. Los administradores pueden tomar una decisión basada en esta señal de puntuación de riesgo para aplicar los requisitos de la organización. Los administradores pueden elegir bloquear el acceso, permitir el acceso o permitir el acceso pero requerir un cambio de contraseña mediante el [autoservicio de restablecimiento de contraseña de Azure AD](../authentication/howto-sspr-deployment.md).

Si se detecta un riesgo, los usuarios pueden utilizar el autoservicio de restablecimiento de contraseña para corregir automáticamente el evento de riesgo de usuario y cerrarlo para evitar ruidos innecesarios para los administradores.

> [!NOTE] 
> Los usuarios deben haberse registrado previamente para el autoservicio de restablecimiento de contraseña antes de desencadenar la directiva de riesgo de usuario.

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación del autoservicio de restablecimiento de contraseña de Azure AD](../authentication/howto-sspr-deployment.md)

- [Habilitación de Azure AD Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md)

- [Habilitación de la directiva de registro de Azure AD Multi-Factor Authentication](howto-identity-protection-configure-mfa-policy.md)

- [Habilitación de las directivas de riesgo de inicios de sesión y de usuarios](howto-identity-protection-configure-risk-policies.md)
