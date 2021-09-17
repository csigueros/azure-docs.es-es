---
title: Deshabilitación de la supervisión en VM Insights
description: En este artículo se describe cómo puede interrumpir la supervisión de las máquinas virtuales en VM Insights.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: fbb169924498b30284ea26d172f605596e6a0379
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446365"
---
# <a name="disable-monitoring-of-your-vms-in-vm-insights"></a>Deshabilitación de la supervisión de las VM en VM Insights

Después de habilitar la supervisión de las máquinas virtuales (VM), más adelante puede elegir deshabilitar la supervisión en VM Insights. En este artículo se muestra cómo deshabilitar la supervisión de una o más VM.  

Actualmente, VM Insights no permite deshabilitar de forma selectiva la supervisión de VM. Puede que el área de trabajo de Log Analytics admita VM Insights y otras soluciones. También puede recopilar otros datos de supervisión. Si el área de trabajo de Log Analytics proporciona estos servicios, debe entender el efecto y métodos de la deshabilitación de la supervisión antes de empezar.

VM Insights se basa en los siguientes componentes para ofrecer su experiencia:

* Un área de trabajo de Log Analytics, que almacena los datos de supervisión de las máquinas virtuales y otros orígenes.
* Una colección de contadores de rendimiento configurados en el área de trabajo. La colección actualiza la configuración de supervisión en todas las VM conectadas al área de trabajo.
* `VMInsights`, que es una solución de supervisión configurada en el área de trabajo. Esta solución actualiza la configuración de supervisión en todas las máquinas virtuales conectadas al área de trabajo.
* `MicrosoftMonitoringAgent` (Windows) o `OmsAgentForLinux` (Linux) y `DependencyAgent`, que son extensiones de máquina virtual de Azure. Estas extensiones recopilan y envían datos al área de trabajo.

Cuando se prepare para deshabilitar la supervisión de las VM, tenga en cuenta estas consideraciones:

* Si realizó la evaluación con una sola VM y usó el área de trabajo de Log Analytics predeterminada preseleccionada, puede deshabilitar la supervisión mediante la desinstalación del agente de dependencia de la máquina virtual y la desconexión del agente de Log Analytics de esta área de trabajo. Este enfoque es adecuado si tiene la intención de utilizar la máquina virtual para otros fines y decide posteriormente volver a conectarla a un área de trabajo diferente.
* Si seleccionó un área de trabajo de Log Analytics existente que admite otras soluciones de supervisión y recopilación de datos de otros orígenes, puede quitar los componentes de la solución del área de trabajo sin que el área de trabajo se vea afectada.  

>[!NOTE]
> Después de quitar los componentes de la solución del área de trabajo, puede seguir viendo los datos de rendimiento y de mapa para las máquinas virtuales de Azure. Los datos, finalmente, dejarán de aparecer en las vistas **Rendimiento** y **Mapa**. La opción **Habilitar** estará disponible en la máquina virtual de Azure seleccionada para que pueda volver a habilitar la supervisión en el futuro.  

## <a name="remove-vm-insights-completely"></a>Eliminación completa de VM Insights

Si aún necesita el área de trabajo de Log Analytics, siga estos pasos para quitar por completo VM Insights. Va a quitar la solución `VMInsights` del área de trabajo.  

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. En Azure Portal, seleccione **Todos los servicios**. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista filtrará las sugerencias en función de la entrada. Seleccione **Log Analytics**.
3. En la lista de áreas de trabajo de Log Analytics, seleccione la que eligió al habilitar VM Insights.
4. A la izquierda, seleccione **Soluciones**.  
5. En la lista de soluciones, seleccione **VMInsights(nombre de área de trabajo)** . En la página **Introducción** de la solución, seleccione **Eliminar**. Cuando se le solicite confirmación, haga clic en **Sí**.

## <a name="disable-monitoring-and-keep-the-workspace"></a>Deshabilitar la supervisión y conservar el área de trabajo  

Si el área de trabajo de Log Analytics aún debe admitir la supervisión de otros orígenes, siga estos pasos para deshabilitar la supervisión en la VM que usó para evaluar VM Insights. Para las VM de Azure, quitará la extensión de VM del agente de dependencias y la extensión de VM del agente de Log Analytics para Windows o Linux directamente de la VM. 

>[!NOTE]
>No quite el agente de Log Analytics si: 
>
> * Azure Automation administra la VM para orquestar los procesos o para administrar la configuración o las actualizaciones. 
> * Azure Security Center administra la VM para la seguridad y la detección de amenazas. 
>
> Si quita el agente de Log Analytics, impedirá que esos servicios y soluciones administren de forma proactiva la VM. 

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 
2. En Azure Portal, seleccione **Virtual Machines**. 
3. En la lista, seleccione una máquina virtual. 
4. En el panel izquierdo, seleccione **Extensiones**. En la página **Extensiones**, seleccione **DependencyAgent**.
5. En la página de propiedades de la extensión, seleccione **Desinstalar**.
6. En la página **Extensiones**, seleccione **MicrosoftMonitoringAgent**. En la página de propiedades de la extensión, seleccione **Desinstalar**.  
