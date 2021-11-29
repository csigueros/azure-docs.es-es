---
title: 'Acceso condicional: requiere dispositivos unidos compatibles o híbridos - Azure Active Directory'
description: Cree una directiva de acceso condicional personalizada para exigir el cumplimiento de los dispositivos unidos compatibles o híbridos.
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
ms.openlocfilehash: cf4638c452714eaae69188450892c131c2033310
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132710812"
---
# <a name="conditional-access-require-compliant-or-hybrid-azure-ad-joined-device"></a>Acceso condicional: requerir un dispositivo unido a Azure AD híbrido o compatible

Las organizaciones que han implementado Microsoft Intune pueden usar la información que devuelven sus dispositivos para identificar aquellos que satisfacen los requisitos de cumplimiento, por ejemplo:

* Exigir un PIN para desbloquearlos
* Exigir el cifrado del dispositivo
* Exigir una versión mínima o máxima del sistema operativo
* Exigir que un dispositivo no se haya liberado ni modificado

Esta información del cumplimiento de las directivas se reenvía a Azure AD donde se pueden tomar decisiones mediante el acceso condicional para conceder o bloquear el acceso a los recursos. Para más información sobre las directivas de cumplimiento de dispositivos, consulte el artículo [Establecimiento de reglas en los dispositivos para permitir el acceso a recursos de su organización con Intune](/intune/protect/device-compliance-get-started).

## <a name="template-deployment"></a>Implementación de plantilla

Las organizaciones pueden optar por implementar esta directiva mediante los pasos descritos a continuación o mediante las [plantillas de acceso condicional (versión preliminar)](concept-conditional-access-policy-common.md#conditional-access-templates-preview). 

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
1. En **Controles de acceso** > **Conceder**.
   1. Seleccione **Require device to be marked as compliant** (Requerir que el dispositivo se marque como compatible) y **Require Hybrid Azure AD joined device** (Requerir un dispositivo de Azure AD híbrido unido).
   1. **En el caso de controles múltiples**, seleccione **Requerir uno de los controles seleccionados**.
   1. Elija **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Solo informe**.
1. Seleccione **Crear** para crear la directiva.

Después de confirmar la configuración mediante el [modo de solo informe](howto-conditional-access-insights-reporting.md), un administrador puede mover el botón de alternancia **Habilitar directiva** de **Solo informe** a **Activar**.

> [!NOTE]
> Puede inscribir sus nuevos dispositivos en Intune aunque seleccione **Requerir que el dispositivo esté marcado como compatible** para **Todos los usuarios** y **Todas las aplicaciones en la nube** mediante los pasos anteriores. El control **Requerir que el dispositivo esté marcado como compatible** no impide la inscripción a Intune. 

### <a name="known-behavior"></a>Comportamiento conocido

En Windows 7, iOS, Android, macOS y algunos exploradores web de terceros, Azure AD identifica el dispositivo mediante un certificado de cliente que se aprovisiona cuando el dispositivo se registra con Azure AD. Cuando un usuario inicia sesión por primera vez a través del explorador, se le pide que seleccione el certificado. El usuario final debe seleccionar este certificado para poder seguir usando el explorador.

## <a name="next-steps"></a>Pasos siguientes

[Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md)

[Determinación del impacto mediante el modo de solo informe de acceso condicional](howto-conditional-access-insights-reporting.md)

[Simulación del comportamiento de inicio de sesión mediante la herramienta What If de acceso condicional](troubleshoot-conditional-access-what-if.md)

[Las directivas de cumplimiento de dispositivos funcionan con Azure AD](/intune/device-compliance-get-started#device-compliance-policies-work-with-azure-ad)
