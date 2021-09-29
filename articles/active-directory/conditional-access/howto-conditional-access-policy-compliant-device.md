---
title: 'Acceso condicional: Requerir dispositivos compatibles (Azure Active Directory)'
description: Cree una directiva de acceso condicional personalizada para exigir el cumplimiento de los dispositivos.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 327d04109be26e14f8fedc46e88266e51e733455
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615422"
---
# <a name="conditional-access-require-compliant-devices"></a>Acceso condicional: Requerir dispositivos compatibles

Las organizaciones que han implementado Microsoft Intune pueden usar la información que devuelven sus dispositivos para identificar aquellos que satisfacen los requisitos de cumplimiento, por ejemplo:

* Exigir un PIN para desbloquearlos
* Exigir el cifrado del dispositivo
* Exigir una versión mínima o máxima del sistema operativo
* Exigir que un dispositivo no se haya liberado ni modificado

Esta información del cumplimiento de las directivas se reenvía a Azure AD donde se pueden tomar decisiones mediante el acceso condicional para conceder o bloquear el acceso a los recursos. Para más información sobre las directivas de cumplimiento de dispositivos, consulte el artículo [Establecimiento de reglas en los dispositivos para permitir el acceso a recursos de su organización con Intune](/intune/protect/device-compliance-get-started).

## <a name="create-a-conditional-access-policy"></a>Creación de una directiva de acceso condicional

Los pasos siguientes le ayudarán a crear una directiva de acceso condicional para exigir que los dispositivos que acceden a los recursos se marquen como compatibles con las directivas de cumplimiento de Intune de su organización.

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Todos los usuarios**.
   1. En **Excluir**, seleccione **Usuarios y grupos** y, luego, elija las cuentas de acceso de emergencia de la organización. 
   1. Seleccione **Listo**.
1. En **Aplicaciones en la nube o acciones** > **Incluir**, seleccione **Todas las aplicaciones en la nube**.
   1. Si debe excluir aplicaciones específicas de la directiva, puede seleccionarlas en la pestaña **Excluir** en **Seleccionar las aplicaciones en la nube excluidas** y elegir **Seleccionar**.
   1. Seleccione **Listo**.
1. En **Condiciones** > **Aplicaciones cliente (versión preliminar)**  > **Seleccionar aplicaciones cliente a las que se aplicará la directiva** mantenga todos los valores seleccionados y haga clic en **Listo**.
1. En **Controles de acceso** > **Conceder**, seleccione **Requerir que el dispositivo esté marcado como compatible**.
   1. Elija **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Activado**.
1. Seleccione **Crear** para crear la directiva.

> [!NOTE]
> Puede inscribir sus nuevos dispositivos en Intune aunque seleccione **Requerir que el dispositivo esté marcado como compatible** para **Todos los usuarios** y **Todas las aplicaciones en la nube** mediante los pasos anteriores. El control **Requerir que el dispositivo esté marcado como compatible** no impide la inscripción a Intune. 

### <a name="known-behavior"></a>Comportamiento conocido

En Windows 7, iOS, Android, macOS y algunos exploradores web de terceros, Azure AD identifica el dispositivo mediante un certificado de cliente que se aprovisiona cuando el dispositivo se registra con Azure AD. Cuando un usuario inicia sesión por primera vez a través del explorador, se le pide que seleccione el certificado. El usuario final debe seleccionar este certificado para poder seguir usando el explorador.

## <a name="next-steps"></a>Pasos siguientes

[Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md)

[Determinación del impacto mediante el modo de solo informe de acceso condicional](howto-conditional-access-insights-reporting.md)

[Simulación del comportamiento de inicio de sesión mediante la herramienta What If de acceso condicional](troubleshoot-conditional-access-what-if.md)

[Las directivas de cumplimiento de dispositivos funcionan con Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
