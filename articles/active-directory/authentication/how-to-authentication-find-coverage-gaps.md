---
title: Detección y solución de brechas en la cobertura de autenticación sólida de los administradores en Azure Active Directory
description: Aprenda a detectar y solucionar brechas en la cobertura de autenticación sólida de los administradores en Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/03/2021
ms.author: justinha
author: inbarckMS
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e52deae12ae44fbf0eb52b8377960530459fca7
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717665"
---
# <a name="find-and-address-gaps-in-strong-authentication-coverage-for-your-administrators"></a>Detección y solución de brechas en la cobertura de autenticación sólida de los administradores

La exigencia de autenticación multifactor (MFA) a los administradores del inquilino es uno de los primeros pasos que se pueden realizar para aumentar la seguridad del inquilino. En este artículo se explica cómo asegurarse de que todos los administradores están cubiertos por la autenticación multifactor.

## <a name="detect-current-usage-for-azure-ad-built-in-administrator-roles"></a>Detección del uso actual de los roles de cuenta predefinida de administrador de Azure AD

La [puntuación segura de Azure AD](../fundamentals/identity-secure-score.md) proporciona una puntuación para **Requerir MFA para roles administrativos** en el inquilino. Esta acción de mejora realiza un seguimiento del uso de MFA por parte del Administrador global, el Administrador de seguridad, el Administrador de Exchange y el Administrador de SharePoint. 

Hay diferentes maneras de comprobar si los administradores están cubiertos por una directiva de MFA. 

- Para solucionar problemas de inicio de sesión de un administrador específico, puede usar los registros de inicio de sesión. Los registros de inicio de sesión permiten filtrar el **Requisito de autenticación** de usuarios concretos. Cualquier inicio de sesión cuyo **Requisito de autenticación** sea **Autenticación de factor único** significa que no hay ninguna directiva de autenticación multifactor, lo que es necesario para el inicio de sesión.

  ![Captura de pantalla del registro de inicio de sesión.](./media/how-to-authentication-find-coverage-gaps/auth-requirement.png)

  Haga clic en **Detalles de autenticación** para obtener [detalles sobre los requisitos de MFA](../reports-monitoring/concept-sign-ins.md#authentication-details).
  
  ![Captura de pantalla de los detalles de la actividad de autenticación.](./media/how-to-authentication-find-coverage-gaps/details.png)

- Para elegir qué directiva habilitar en función de las licencias de usuario, se dispone de un nuevo asistente para habilitar MFA que ayuda a [comparar directivas de MFA](concept-mfa-licensing.md#compare-multi-factor-authentication-policies) y a ver qué pasos son adecuados para la organización. El asistente muestra los administradores protegidos por MFA en los últimos 30 días.

  ![Captura de pantalla del asistente para habilitar la autenticación multifactor.](./media/how-to-authentication-find-coverage-gaps/wizard.png)

- Para crear mediante programación un informe en el que se enumeren todos los usuarios con roles de administrador en el inquilino y su estado de autenticación sólida, puede ejecutar un [script de PowerShell](https://github.com/microsoft/AzureADToolkit/blob/main/src/Find-UnprotectedUsersWithAdminRoles.ps1). Este script enumera todas las asignaciones de roles integrados y personalizados permanentes y aptas, así como los grupos con roles asignados, y busca usuarios que no estén registrados en MFA o que no inicien sesión con MFA mediante la evaluación de sus métodos de autenticación y su actividad de inicio de sesión.

## <a name="enforce-multi-factor-authentication-on-your-administrators"></a>Aplicación de la autenticación multifactor a los administradores

En función de las brechas detectadas, exija que los administradores usen la autenticación multifactor de una de estas maneras:

- Si los administradores tienen licencia de Azure AD Premium, puede [crear una directiva de acceso condicional](tutorial-enable-azure-mfa.md) para aplicar MFA a los administradores. También puede actualizar esta directiva para requerir MFA a los usuarios con roles personalizados.  

- Ejecute el [asistente para habilitar MFA](https://aka.ms/MFASetupGuide) para elegir la directiva de MFA.

- Si asigna roles de administrador personalizados o integrados en [Privileged Identity Management](../privileged-identity-management/pim-configure.md), exija la autenticación multifactor tras la activación del rol.

## <a name="use-passwordless-and-phishing-resistant-authentication-methods-for-your-administrators"></a>Uso de métodos de autenticación resistentes a la suplantación de identidad (phishing) y sin contraseña para los administradores

Una vez que los administradores apliquen la autenticación multifactor y hayan estado usándola durante un tiempo, es el momento de aumentar el nivel de autenticación sólida y usar un método de autenticación resistente a la suplantación de identidad (phishing) y sin contraseña: 

- [Inicio de sesión telefónico (con Microsoft Authenticator)](concept-authentication-authenticator-app.md)
- [FIDO2](concept-authentication-passwordless.md#fido2-security-keys)
- [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-overview)

Puede leer más sobre estos métodos de autenticación y sus consideraciones de seguridad en [Métodos de autenticación de Azure AD](concept-authentication-methods.md).