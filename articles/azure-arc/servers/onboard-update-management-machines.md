---
title: Conexión de máquinas desde Update Management de Azure Automation
description: En este artículo, aprenderá a conectar máquinas híbridas a Azure Arc administrado por Update Management de Automation.
ms.date: 09/14/2021
ms.topic: conceptual
ms.openlocfilehash: fc5de9146ea07e490e69760397a661f4bbb99eec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699528"
---
# <a name="connect-hybrid-machines-to-azure-from-automation-update-management"></a>Conexión de máquinas híbridas a Azure desde Update Management de Automation

Puede habilitar servidores habilitados para Azure Arc para una o varias de las máquinas virtuales Windows o Linux o servidores físicos hospedados en el entorno local o en otro entorno en la nube que se administran con Update Management de Azure Automation. Este proceso de incorporación automatiza la descarga y la instalación del [agente de Connected Machine](agent-overview.md). Para conectar las máquinas a los servidores habilitados para Azure Arc, se usa una [entidad de servicio](../../active-directory/develop/app-objects-and-service-principals.md) de Azure Active Directory en lugar de usar su identidad con privilegios para [conectar de manera interactiva](onboard-portal.md) la máquina. Esta entidad de servicio se crea automáticamente como parte del proceso de incorporación para estas máquinas.

Antes de comenzar, asegúrese de revisar los [requisitos previos](agent-overview.md#prerequisites) y compruebe que su suscripción y sus recursos los cumplen. Para obtener información sobre las regiones admitidas y otras consideraciones relacionadas, consulte [Regiones de Azure admitidas](overview.md#supported-regions).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="how-it-works"></a>Cómo funciona

Cuando se inicia el proceso de incorporación, se crea una [entidad de servicio](../../active-directory/fundamentals/service-accounts-principal.md) de Active Directory en el inquilino. 

Para instalar y configurar el agente de Connected Machine en la máquina de destino, se ejecuta un runbook maestro denominado **Add-AzureConnectedMachines** en el espacio aislado de Azure. En función del sistema operativo que se detecte en la máquina, el runbook maestro llama a un runbook secundario denominado **Add-AzureConnectedMachineWindows** o **Add-AzureConnectedMachineLinux** que se ejecuta en el rol [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) del sistema directamente en la máquina. La salida del trabajo del runbook se escribe en el historial de trabajos y puede ver el [resumen de estado](../../automation/automation-runbook-execution.md#job-statuses) o profundizar en los detalles de un trabajo de runbook en [Azure Portal](../../automation/manage-runbooks.md#view-statuses-in-the-azure-portal), o bien mediante [Azure PowerShell](../../automation/manage-runbooks.md#retrieve-job-statuses-using-powershell). La ejecución de runbooks en Azure Automation escribe los detalles en un registro de actividad de la cuenta de Automation. Para más información sobre el uso del registro, consulte [Recuperación de detalles del registro de actividad](../../automation/manage-runbooks.md#retrieve-details-from-activity-log).

El paso final establece la conexión con Azure Arc con el comando `azcmagent` mediante la entidad de servicio a fin de registrar la máquina como recurso en Azure.

## <a name="prerequisites"></a>Requisitos previos

Este método requiere que sea miembro del rol [Operador de trabajos de Automation](../../automation/automation-role-based-access-control.md#automation-job-operator) o de uno superior para poder crear trabajos de runbook en la cuenta de Automation. 

Si habilitó Azure Policy para [administrar la ejecución de un runbook](../../automation/enforce-job-execution-hybrid-worker.md) y aplicar el destino de la ejecución del runbook en un grupo de Hybrid Runbook Worker, debe deshabilitar esta directiva. De lo contrario, se producirá un error en los trabajos de runbook que incorporen las máquinas a los servidores habilitados para Arc. 

## <a name="add-machines-from-the-azure-portal"></a>Incorporación de máquinas desde Azure Portal

Siga estos pasos para configurar la máquina híbrida con servidores habilitados para Arc. El servidor o la máquina deben estar encendidos y en línea para que el proceso se complete correctamente.

1. En el explorador, vaya a [Azure Portal](https://portal.azure.com).

1. Vaya a la página **Servidores: Azure Arc** y seleccione **Agregar** en la parte superior derecha.

1. En la página **Seleccionar un método**, seleccione el icono **Add managed servers from Update Management (preview)** (Agregar servidores administrados desde Update Management [versión preliminar]) y, luego, seleccione **Agregar servidores**.

1. En la página **Aspectos básicos**, configure lo siguiente:

    1. En la lista desplegable **Grupo de recursos**, seleccione el grupo de recursos desde el que se administrará la máquina.
    1. En la lista desplegable **Región**, seleccione la región de Azure en la que se almacenarán los metadatos de los servidores.
    1. Si la máquina se comunica mediante un servidor proxy para conectarse a Internet, especifique la dirección IP del servidor proxy o el nombre y el número de puerto que usará la máquina para comunicarse con el servidor proxy. Escriba el valor con el formato `http://<proxyURL>:<proxyport>`.
    1. Seleccione **Siguiente: Máquinas**.

1. En la página **Máquinas**, seleccione la **suscripción** y la **cuenta de Automation** en la lista desplegable que tiene habilitada la característica Update Management y que incluye las máquinas que desea incorporar a servidores habilitados para Azure Arc.

   Después de especificar la cuenta de Automation, en la lista que aparece a continuación se muestran máquinas que no son de Azure administradas por Update Management para esa cuenta de Automation. Se muestran tanto máquinas Windows como Linux. Seleccione **Agregar** para cada una de ellas.

   Si desea revisar la selección, haga clic en **Revisar la selección**. Si desea quitar una máquina, seleccione **Quitar** en la columna **Acción**. 

   Una vez que confirme la selección, haga clic en **Siguiente: Etiquetas**.

1. En la página **Etiquetas**, especifique uno o varios pares **Nombre**/**Valor** para admitir sus estándares. Seleccione **Siguiente: Revisar y agregar**.

1. En la pestaña **Revisar y agregar**, revise la información resumida y, luego, seleccione **Agregar máquinas**. Si todavía necesita realizar cambios, seleccione **Anterior**.

## <a name="verify-the-connection-with-azure-arc"></a>Comprobación de la conexión con Azure Arc

Una vez que el agente está instalado y configurado para que se conecte a los servidores habilitados para Azure Arc, vaya a Azure Portal a fin de comprobar que el servidor se conectó correctamente. Vea las máquinas en [Azure Portal](https://aka.ms/hybridmachineportal).

![Una conexión de servidor correcta](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Pasos siguientes

- Puede encontrar información sobre la solución de problemas en la guía [Solución de problemas de conexión del agente de Connected Machine](troubleshoot-agent-onboard.md).

- Examine la [guía de planeamiento e implementación](plan-at-scale-deployment.md) para planear la implementación de servidores habilitados para Azure Arc a cualquier escala e implementar la administración y supervisión centralizadas.

- Aprenda a administrar la máquina con [Azure Policy](../../governance/policy/overview.md) para tareas como la [configuración de invitado](../../governance/policy/concepts/guest-configuration.md) de la máquina virtual, la comprobación de que la máquina informa al área de trabajo de Log Analytics esperada, la habilitación de la supervisión con [Información de máquinas virtuales](../../azure-monitor/vm/vminsights-enable-policy.md) y mucho más.