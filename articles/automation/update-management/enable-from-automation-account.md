---
title: Habilitación de Update Management de Azure Automation desde una cuenta de Automation
description: En este artículo se indica cómo habilitar Update Management desde una cuenta de Automation.
services: automation
ms.subservice: update-management
ms.date: 11/09/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: bc17b875c8f16304d5292f7ed852909373b8c5d9
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122768315"
---
# <a name="enable-update-management-from-an-automation-account"></a>Habilitación de Update Management desde una cuenta de Automation

En este artículo se describe cómo puede usar su cuenta de Automation para habilitar la característica [Update Management](overview.md) para las VM del entorno, incluidos los servidores o las máquinas registrados en [servidores habilitados para Azure Arc](../../azure-arc/servers/overview.md). Para habilitar máquinas virtuales de Azure a escala, debe habilitar una máquina virtual de Azure existente mediante Update Management.

> [!NOTE]
> Al habilitar Update Management, solo en determinadas regiones se puede vincular un área de trabajo de Log Analytics y una cuenta de Automation. Para obtener una lista de los pares de asignación que se admiten, consulte [Asignación de región para la cuenta de Automation y el área de trabajo de Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Requisitos previos

* Suscripción de Azure. Si aún no tiene ninguna, puede [activar las ventajas de la suscripción a MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o suscribirse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Una [cuenta de Automation](../automation-security-overview.md) para administrar máquinas.
* Una [máquina virtual de Azure](../../virtual-machines/windows/quick-create-portal.md), o VM o servidor registrados con servidores habilitados para Arc. Los servidores o máquinas virtuales que no son de Azure deben tener el [agente de Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) para Windows o Linux instalado e informar al área de trabajo vinculada a la cuenta de Automation en la que está habilitado Update Management. Se recomienda instalar el agente de Log Analytics para Windows o Linux. Para ello, primero debe conectar la máquina a los [servidores habilitados para Azure Arc](../../azure-arc/servers/overview.md) y, a continuación, usar Azure Policy para asignar el [agente de Log Analytics a la directiva integrada de las máquinas de Azure Arc de *Linux* o de *Windows*](../../governance/policy/samples/built-in-policies.md#monitoring). Como alternativa, si planea supervisar las máquinas con Azure Monitor para VM, en su lugar, use la iniciativa [Habilitar Azure Monitor para VM](../../governance/policy/samples/built-in-initiatives.md#monitoring).


## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="enable-update-management"></a>Habilitación de la Administración de actualizaciones

1. En su cuenta de Automation, seleccione **Update Management** en **Update Management**.

2. Elija el área de trabajo de Log Analytics y la cuenta de Automation, y seleccione **Habilitar** para habilitar Update Management. La instalación tarda hasta 15 minutos en completarse.

    ![Habilitación de la Administración de actualizaciones](media/enable-from-automation-account/onboardsolutions2.png)

## <a name="enable-azure-vms"></a>Habilitar máquinas virtuales de Azure

1. En su cuenta de Automation, seleccione **Update Management** en **Update Management**.

2. Seleccione **+ Agregar máquinas virtuales de Azure** y seleccione una o varias máquinas virtuales en la lista. Las máquinas virtuales que no se pueden habilitar aparecen atenuadas y no se pueden seleccionar. Las VM de Azure pueden existir en cualquier región, con independencia de la ubicación de la cuenta de Automation.

3. Seleccione **Habilitar** para agregar las máquinas virtuales seleccionadas a la búsqueda guardada de los grupos de equipos para la característica.

    ![Habilitar máquinas virtuales de Azure](media/enable-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Habilitación de máquinas virtuales que no son de Azure

En el caso de las máquinas o los servidores hospedados fuera de Azure, incluidos los registrados con servidores habilitados para Azure Arc, siga los pasos a continuación para habilitarlos con Update Management.  

1. En su cuenta de Automation, seleccione **Update Management** en **Update Management**.

2. Seleccione **Agregar máquina que no es de Azure**. Esta acción abre una nueva ventana del explorador con las [instrucciones para instalar y configurar el agente de Log Analytics para Windows](../../azure-monitor/agents/log-analytics-agent.md) y que la máquina pueda empezar a enviar notificaciones a Update Management. Si va a habilitar una máquina que Operations Manager administre, no es necesario un nuevo agente. La información del área de trabajo se agrega a la configuración de los agentes.

## <a name="enable-machines-in-the-workspace"></a>Habilitación de máquinas en el área de trabajo

Las máquinas instaladas manualmente o las máquinas que ya envían notificaciones al área de trabajo deben agregarse a Azure Automation para poder habilitar Update Management.

1. En su cuenta de Automation, seleccione **Update Management** en **Update Management**.

2. Seleccione **Administrar máquinas**. Es posible que, si previamente ha seleccionado la opción **Habilitar en todas las máquinas disponibles y futuras**, el botón **Administrar máquinas** esté atenuado.

    ![Búsquedas guardadas](media/enable-from-automation-account/managemachines.png)

3. Para habilitar Update Management en todas las máquinas disponibles que informan al área de trabajo, seleccione **Habilitar en todas las máquinas disponibles** en la página Administrar máquinas. Esta acción deshabilita el control para agregar máquinas individualmente y agrega todas las máquinas que informan al área de trabajo a la consulta de búsqueda guardada del grupo de equipos `MicrosoftDefaultComputerGroup`. Cuando se selecciona, esta acción deshabilita la opción **Administrar máquinas**.

4. Para habilitar la característica en todas las máquinas disponibles y futuras, seleccione **Habilitar en todas las máquinas disponibles y futuras**. Esta opción elimina la búsqueda guardada y la configuración de ámbito del área de trabajo y permite a la característica incluir todas las máquinas de Azure y que no son de Azure que, actualmente o en el futuro, envían notificaciones al área de trabajo. Cuando está seleccionada, esta acción deshabilita permanentemente la opción **Administrar máquinas**, ya que no hay ninguna configuración de ámbito disponible.

    > [!NOTE]
    > Dado que esta opción elimina la búsqueda guardada y la configuración de ámbito dentro de Log Analytics, es importante quitar todos los bloqueos de eliminación en el área de trabajo de Log Analytics antes de seleccionar esta opción. Si no lo hace, la opción no quitará las configuraciones y deberá hacerlo manualmente.

5. Si es necesario, puede agregar de nuevo la configuración de ámbito si vuelve a agregar la consulta de búsqueda guardada inicial. Para obtener más información, consulte el tema sobre la [limitación del ámbito de implementación de Update Management](scope-configuration.md).

6. Para habilitar la característica para una o varias máquinas, seleccione **Habilitar en las máquinas seleccionadas** y seleccione **Agregar** junto a cada máquina. Esta tarea agrega los nombres de máquina seleccionados a la consulta de la búsqueda guardada de grupos de equipos para la característica.

## <a name="next-steps"></a>Pasos siguientes

* Para usar Update Management para máquinas virtuales, consulte [Administración de actualizaciones y revisiones para las máquinas virtuales](manage-updates-for-vm.md).

* Cuando ya no tenga que administrar máquinas virtuales ni servidores con Update Management, consulte [Eliminación de máquinas virtuales de Update Management](remove-vms.md).

* Para solucionar los errores de Update Management generales, consulte [Solución de problemas de Update Management](../troubleshoot/update-management.md).
