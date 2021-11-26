---
title: 'Acceso condicional: Exigir autenticación multifactor para la administración de Azure (Azure Active Directory)'
description: Cree una directiva de acceso condicional personalizada para exigir autenticación multifactor para las tareas de administración de Azure.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 308f64681c3d78070f70e295c4947842d8694b6f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715572"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Acceso condicional: Exigir autenticación multifactor para la administración de Azure

Las organizaciones usan muchos servicios de Azure y los administran desde herramientas basadas en Azure Resource Manager, como:

* Portal de Azure
* Azure PowerShell
* Azure CLI

Estas herramientas pueden proporcionar acceso con privilegios elevados a los recursos, que pueden modificar las configuraciones de toda la suscripción, la configuración del servicio y la facturación de la suscripción. Para proteger estos recursos con privilegios, Microsoft recomienda exigir la autenticación multifactor para cualquier usuario que tenga acceso a ellos. En Azure AD, estas herramientas están agrupadas en un conjunto denominado [Microsoft Azure Management](concept-conditional-access-cloud-apps.md#microsoft-azure-management). Para Azure Government, este debería ser la aplicación Cloud Management API de Azure Government. 

## <a name="user-exclusions"></a>Exclusiones de usuarios

Las directivas de acceso condicional son herramientas eficaces, por lo que se recomienda excluir las siguientes cuentas de la directiva:

* Cuentas de **acceso de emergencia** para **evitar** el bloqueo de cuentas en todo el inquilino. En el caso improbable de que todos los administradores estén bloqueados en el inquilino, la cuenta administrativa de acceso de emergencia se puede usar para iniciar sesión en el inquilino y realizar los pasos para recuperar el acceso.
   * Se puede encontrar más información en el artículo [Administración de cuentas de acceso de emergencia en Azure AD](../roles/security-emergency-access.md).
* **Cuentas de servicio** y **entidades de servicio**, como la cuenta de sincronización de Azure AD Connect. Las cuentas de servicio son cuentas no interactivas que no están asociadas a ningún usuario en particular. Los servicios back-end las usan normalmente para permitir el acceso mediante programación a las aplicaciones, pero también se utilizan para iniciar sesión en los sistemas con fines administrativos. Las cuentas de servicio como estas se deben excluir porque MFA no se puede completar mediante programación. El acceso condicional no bloquea las llamadas realizadas por las entidades de servicio.
   * Si su organización usa estas cuentas en scripts o código, piense en la posibilidad de reemplazarlas por [identidades administradas](../managed-identities-azure-resources/overview.md). Como solución temporal, puede excluir estas cuentas específicas de la directiva de línea de base.

## <a name="template-deployment"></a>Implementación de plantilla

A la hora de implementar esta directiva las organizaciones pueden optar por utilizar los pasos que se describen a continuación o las [plantillas de acceso condicional (versión preliminar)](concept-conditional-access-policy-common.md#conditional-access-templates-preview). 

## <a name="create-a-conditional-access-policy"></a>Creación de una directiva de acceso condicional

Los pasos siguientes le ayudarán a crear una directiva de acceso condicional para exigir que los usuarios con acceso al conjunto de [Microsoft Azure Management](concept-conditional-access-cloud-apps.md#microsoft-azure-management) realicen la autenticación multifactor.

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Todos los usuarios**.
   1. En **Excluir**, seleccione **Usuarios y grupos** y, luego, elija las cuentas de acceso de emergencia de la organización. 
   1. Seleccione **Listo**.
1. En **Aplicaciones en la nube o acciones** > **Incluir**, elija sucesivamente **Seleccionar aplicaciones**, **Microsoft Azure Management**, **Seleccionar** y, luego, **Listo**.
1. En **Controles de acceso** > **Conceder**, seleccione **Conceder acceso**, **Requerir autenticación multifactor** y **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Solo informe**.
1. Seleccione **Crear** para crear la directiva.

Después de confirmar la configuración desde el [modo de solo informe](howto-conditional-access-insights-reporting.md), un administrador puede pasar el botón de alternancia **Habilitar directiva** de **Solo informe** a **Activar**.

## <a name="next-steps"></a>Pasos siguientes

[Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md)

[Simulación del comportamiento de inicio de sesión mediante la herramienta What If de acceso condicional](troubleshoot-conditional-access-what-if.md)
