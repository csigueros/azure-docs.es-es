---
title: Supervisión y solución de problemas de inicios de sesión con evaluación continua de acceso en Azure AD
description: Solución de problemas y respuesta más rápida a los cambios en el estado del usuario con la evaluación continua de acceso en Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/22/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jlu, shreyamalik
ms.collection: M365-identity-device-management
ms.openlocfilehash: 431124d79307c305ebb589a72a0608347de8a2c7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594570"
---
# <a name="monitor-and-troubleshoot-continuous-access-evaluation"></a>Supervisión y solución de problemas de evaluación continua de acceso

Los administradores pueden supervisar y solucionar problemas de eventos de inicio de sesión en los que la [evaluación continua de acceso (CAE)](concept-continuous-access-evaluation.md) se aplica de varias maneras.

## <a name="continuous-access-evaluation-sign-in-reporting"></a>Informes de inicio de sesión de evaluación continua de acceso

Los administradores tendrán la oportunidad de supervisar los inicios de sesión de los usuarios en los que se aplica la CAE. Este panel se puede encontrar mediante las instrucciones siguientes:

1.  Inicie sesión en **Azure Portal** como administrador de acceso condicional, administrador de seguridad o administrador global.
1.  Vaya a **Azure Active Directory** > **Inicios de sesión**. 
1.  Aplique el filtro **Is CAE Token** (Es token de CAE). 

[ ![Adición de un filtro al registro de inicios de sesión para ver dónde se aplica CAE o no](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-sign-ins-log-apply-filter.png#lightbox)

Desde aquí, se mostrará a los administradores información sobre los eventos de inicio de sesión de sus usuarios. Seleccione cualquier inicio de sesión para ver detalles sobre la sesión, como qué directivas de acceso condicional se han aplicado y si se ha habilitado CAE. 

Un intento de inicio de sesión determinado puede mostrarse en la pestaña interactiva o no interactiva. Es posible que los administradores tengan que comprobar las dos pestañas a medida que realicen el seguimiento de los inicios de sesión de sus usuarios.

### <a name="searching-for-specific-sign-in-attempts"></a>Búsqueda de intentos de inicio de sesión específicos

Use filtros para restringir la búsqueda. Por ejemplo, si un usuario ha iniciado sesión en Teams, use el filtro Aplicación y establézcalo en Teams. Es posible que los administradores tengan que comprobar los inicios de sesión desde pestañas interactivas y no interactivas para localizar el inicio de sesión específico. Para restringir aún más la búsqueda, los administradores pueden aplicar varios filtros.

## <a name="continuous-access-evaluation-workbooks"></a>Libros de evaluación continua de acceso

El libro de conclusiones de evaluación continua de acceso permite a los administradores ver y supervisar las conclusiones de uso de CAE para sus inquilinos. En la primera tabla se muestran los intentos de autenticación con errores de coincidencia de IP. En la segunda tabla se muestra el estado de compatibilidad con CAE en varias aplicaciones. Este libro se puede encontrar como plantilla en la categoría Acceso condicional. 

### <a name="accessing-the-cae-workbook-template"></a>Acceso a la plantilla de libro de CAE

Antes de que se muestren los libros, es necesario completar la integración de Log Analytics. Para obtener más información sobre cómo transmitir los registros de inicio de sesión de Azure AD a un área de trabajo de Log Analytics, consulte el artículo [Integración de registros de Azure AD con registros de Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).
 
1.  Inicie sesión en **Azure Portal** como administrador de acceso condicional, administrador de seguridad o administrador global. 
1.  Vaya a **Azure Active Directory** > **Workbooks**.
1.  En **Plantillas públicas**, busque **Continuous access evaluation insights** (Conclusiones de evaluación continua de acceso).

[ ![Búsqueda del libro de conclusiones de CAE en la galería para continuar con la supervisión](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png) ](./media/howto-continuous-access-evaluation-troubleshoot/azure-ad-workbooks-continuous-access-evaluation.png#lightbox)

El libro **Continuous access evaluation insights** (Conclusiones de evaluación continua de acceso) contiene dos tablas:

### <a name="potential-ip-address-mismatch-between-azure-ad-and-resource-provider"></a>Posible falta de coincidencia de direcciones IP entre Azure AD y el proveedor de recursos  

![Tabla 1 del libro en la que se muestran posibles discrepancias de direcciones IP](./media/howto-continuous-access-evaluation-troubleshoot/continuous-access-evaluation-insights-workbook-table-1.png)

La tabla de posible falta de coincidencia de direcciones IP entre el proveedor de recursos y Azure AD permite a los administradores investigar las sesiones en las que la dirección IP detectada por Azure AD no coincide con la detectada por el proveedor de recursos. 

En esta tabla del libro se aclaran estos escenarios y se muestran las direcciones IP correspondientes y si se ha emitido un token de CAE durante la sesión. 

#### <a name="ip-address-configuration"></a>Configuración de dirección IP

El proveedor de identidades y los proveedores de recursos pueden ver diferentes direcciones IP. Este error de coincidencia se puede producir debido a los ejemplos siguientes:

- La red implementa la tunelización dividida.
- El proveedor de recursos usa una dirección IPv6 y Azure AD una dirección IPv4.
- Debido a las configuraciones de red, Azure AD ve una dirección IP del cliente y el proveedor de recursos ve otra.

Si se produce este escenario en el entorno, con el fin de evitar bucles infinitos, Azure AD emitirá un token de CAE de una hora y no aplicará el cambio de ubicación del cliente durante ese periodo. Incluso en este caso, se mejora la seguridad en comparación con los tokens de una hora tradicionales, ya que todavía se evalúan los otros eventos además de los eventos de cambio de ubicación del cliente.

Los administradores pueden ver los registros filtrados por intervalo de tiempo y aplicación. Los administradores pueden comparar el número de IP no coincidentes detectadas con el número total de inicios de sesión durante un período de tiempo especificado. 

Para desbloquear a los usuarios, los administradores pueden agregar direcciones IP específicas a una ubicación con nombre de confianza.

1.  Inicie sesión en **Azure Portal** como administrador de acceso condicional, administrador de seguridad o administrador global. 
1.  Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional** > **Ubicaciones con nombre**. Aquí puede crear o actualizar las ubicaciones IP de confianza.

> [!NOTE]
> Antes de agregar una dirección IP como una ubicación con nombre de confianza, confirme que la dirección IP pertenece realmente a la organización deseada.

Para obtener más información sobre las ubicaciones con nombre, vea el artículo [Uso de la condición de ubicación](location-condition.md#named-locations).
 
## <a name="next-steps"></a>Pasos siguientes

- [Integración de registros de Azure AD con registros de Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
- [Uso de la condición de ubicación](location-condition.md#named-locations)
- [Evaluación continua de acceso](concept-continuous-access-evaluation.md)
