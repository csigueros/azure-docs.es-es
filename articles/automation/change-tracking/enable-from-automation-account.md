---
title: Habilitación de Change Tracking e Inventario de Azure Automation desde una cuenta de Automation
description: En este artículo se muestra cómo habilitar Change Tracking e Inventario desde una cuenta de Automation.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 4e5e0b563b2701a8b5ddbffbf485a3127d52ece0
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769899"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Habilitación de Change Tracking e Inventario desde una cuenta de Automation

En este artículo se describe cómo puede usar la cuenta de Automation para habilitar el [Seguimiento de cambios e inventario](overview.md) para las máquinas virtuales del entorno. Para habilitar máquinas virtuales de Azure a escala, debe habilitar una máquina virtual existente mediante Change Tracking e Inventario.

> [!NOTE]
> Al habilitar Change Tracking e Inventario, solo se admite la vinculación a un área de trabajo de Log Analytics y una cuenta de Automation en regiones concretas. Para obtener una lista de los pares de asignación que se admiten, consulte [Asignación de región para la cuenta de Automation y el área de trabajo de Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Requisitos previos

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Una [cuenta de Automation](../automation-security-overview.md) para administrar máquinas.
* Una [máquina virtual](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure en https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Habilitación de Change Tracking e Inventario

1. Vaya a la cuenta de Automation y seleccione **Inventory** o **Change Tracking** en **Administración de configuración**.

2. Para habilitar Change Tracking e Inventario, elija el área de trabajo de Log Analytics y la cuenta de Automation, y haga clic en **Habilitar**. La instalación tarda hasta 15 minutos en completarse.

    ![Habilitación de Change Tracking e Inventario](media/enable-from-automation-account/enable-feature.png)

## <a name="enable-azure-vms"></a>Habilitar máquinas virtuales de Azure

1. En la cuenta de Automation, seleccione **Inventario** o **Change Tracking** en **Administración de configuración**.

2. Haga clic en **+ Agregar máquinas virtuales de Azure** y seleccione una o varias máquinas virtuales en la lista. Las máquinas virtuales que no se pueden habilitar aparecen atenuadas y no se pueden seleccionar. Las VM de Azure pueden existir en cualquier región, con independencia de la ubicación de la cuenta de Automation. 

3. Haga clic en **Habilitar** para agrega las máquinas virtuales seleccionadas a la búsqueda guardada de los grupos de equipos para la característica. Para obtener más información, consulte [Limitación del ámbito de implementación de Change Tracking e Inventario](manage-scope-configurations.md).

      [ ![Habilitación de máquinas virtuales de Azure](./media/enable-from-automation-account/enable-azure-vms.png)](./media/enable-from-automation-account/enable-azure-vms-expanded.png#lightbox)

## <a name="enable-non-azure-vms"></a>Habilitación de máquinas virtuales que no son de Azure

Las máquinas que no estén en Azure se deben agregar manualmente. Se recomienda instalar el agente de Log Analytics para Windows o Linux. Para ello, primero debe conectar su máquina a [servidores habilitados para Azure Arc](../../azure-arc/servers/overview.md) y, a continuación, usar Azure Policy para asignar la definición de directiva integrada [Implementación del agente de Log Analytics en máquinas de Azure Arc para *Linux* o *Windows*](../../governance/policy/samples/built-in-policies.md#monitoring). Si también planea supervisar las máquinas con Azure Monitor para VM, en su lugar, use la iniciativa para [habilitar Azure Monitor para VM](../../governance/policy/samples/built-in-initiatives.md#monitoring).

1. Desde la cuenta de Automation, seleccione **Inventario** o **Change Tracking** en **Administración de configuración**.

2. Haga clic en **Agregar un equipo que no es de Azure**. Esta acción abre una nueva ventana del explorador con las [instrucciones para instalar y configurar el agente de Log Analytics para Windows](../../azure-monitor/agents/log-analytics-agent.md) de forma que la máquina pueda empezar a notificar operaciones de Change Tracking e Inventario. Si va a habilitar una máquina administrada actualmente por Operations Manager, no se necesita un agente nuevo y la información del área de trabajo se escribe en el agente existente.

## <a name="enable-machines-in-the-workspace"></a>Habilitación de máquinas en el área de trabajo

Las máquinas instaladas manualmente o las que ya envían notificaciones al área de trabajo se deben agregar a Azure Automation para poder habilitar Change Tracking e Inventario.

1. En la cuenta de Automation, seleccione **Inventario** o **Change Tracking** en **Administración de configuración**.

2. Seleccione **Administrar máquinas**. Es posible que, si previamente seleccionó la opción **Habilitar en todas las máquinas disponibles y futuras**, la opción **Administrar máquinas** esté atenuada.

    ![Búsquedas guardadas](media/enable-from-automation-account/manage-machines.png)

3. Para habilitar el Seguimiento de cambios e inventario en todas las máquinas disponibles, seleccione **Habilitar en todas las máquinas disponibles** en la página **Administrar máquinas**. Esta acción deshabilita el control para agregar máquinas individualmente y agrega todas las máquinas que informan al área de trabajo a la consulta de búsqueda guardada del grupo de equipos. Cuando se selecciona, esta acción deshabilita la opción **Administrar máquinas**.

4. Para habilitar la característica en todas las máquinas disponibles y futuras, seleccione **Habilitar en todas las máquinas disponibles y futuras**. Esta opción elimina la búsqueda guardada y la configuración de ámbito del área de trabajo y abre la característica para todas las máquinas de Azure y que no son de Azure que envían notificaciones al área de trabajo. Cuando está seleccionada, esta acción deshabilita permanentemente la opción **Administrar máquinas**, ya que no queda ninguna configuración de ámbito.

    > [!NOTE]
    > Dado que esta opción elimina la búsqueda guardada y la configuración de ámbito dentro de Log Analytics, es importante quitar todos los bloqueos de eliminación en el área de trabajo de Log Analytics antes de seleccionar esta opción. Si no lo hace, la opción no quitará las configuraciones y deberá hacerlo manualmente.

5. Si es necesario, puede agregar de nuevo la configuración de ámbito si vuelve a agregar la búsqueda guardada inicial. Para obtener más información, consulte [Limitación del ámbito de implementación de Change Tracking e Inventario](manage-scope-configurations.md).

6. Para habilitar la característica para una o varias máquinas, seleccione **Habilitar en las máquinas seleccionadas** y haga clic en **Agregar** junto a cada máquina para habilitar la característica. Esta tarea agrega los nombres de máquina seleccionados a la consulta de la búsqueda guardada de grupos de equipos para la característica.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre cómo trabajar con la característica, consulte [Administración de Change Tracking](manage-change-tracking.md) y [Administración del inventario](manage-inventory-vms.md).

* Para solucionar problemas generales con la característica, consulte [Solución de problemas de Change Tracking e Inventario](../troubleshoot/change-tracking.md).
