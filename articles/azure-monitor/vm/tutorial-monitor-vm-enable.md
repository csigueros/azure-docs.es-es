---
title: 'Tutorial: Habilitación de la supervisión de máquinas virtuales de Azure'
description: Habilite la supervisión con VP Insights en Azure Monitor para supervisar una máquina virtual de Azure.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/04/2021
ms.openlocfilehash: 4156685d707a6e4fd6319a7130750838434f1efa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350056"
---
# <a name="tutorial-enable-monitoring-for-azure-virtual-machine"></a>Tutorial: Habilitación de la supervisión para máquinas virtuales de Azure
Para supervisar el estado y el rendimiento de una máquina virtual de Azure, debe instalar un agente para recopilar datos de su sistema operativo invitado. Vm Insights es una característica de Azure Monitor para supervisar el sistema operativo invitado y las cargas de trabajo que se ejecutan en las máquinas virtuales de Azure. Al habilitar la supervisión de una máquina virtual de Azure, instala los agentes necesarios y comienza a recopilar información de rendimiento, proceso y dependencia del sistema operativo invitado. 

> [!NOTE]
> Si no conoce Azure Monitor, debe comenzar con [Tutorial: Supervisión de recursos de Azure con Azure Monitor](../essentials/monitor-azure-resource.md). Las máquinas virtuales de Azure generan datos de supervisión similares a otros recursos de Azure, como, por ejemplo, métricas de plataforma y registro de actividad. En este tutorial se describe cómo habilitar la supervisión adicional única de las máquinas virtuales.

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Crear una área de trabajo de Log Analytics para recopilar datos de rendimiento y de registro de la máquina virtual.
> * Habilitar VM Insights para la máquina virtual que instala los agentes necesarios e inicia la recopilación de datos. 
> * Inspeccionar los gráficos que analizan los datos de rendimiento recopilados de la máquina virtual. 
> * Inspeccionar el mapa que muestra los procesos que se ejecutan en la máquina virtual y las dependencias con otros sistemas.


> [!NOTE]
> Vm Insights instala el agente de Log Analytics, que recopila datos de rendimiento del sistema operativo invitado de las máquinas virtuales. No recopila registros del sistema operativo invitado y no envía datos de rendimiento a las métricas de Azure Monitor. Para conocer esta funcionalidad, visite [Tutorial: Recopilación de registros y métricas de invitado de una máquina virtual de Azure](tutorial-monitor-vm-guest.md).

## <a name="prerequisites"></a>Requisitos previos
Para completar este tutorial, necesitará lo siguiente: 

- Una máquina virtual de Azure para supervisar.



## <a name="create-a-log-analytics-workspace"></a>Creación de un área de trabajo de Log Analytics
[!INCLUDE [Create workspace](../../../includes/azure-monitor-tutorial-workspace.md)]


## <a name="enable-monitoring"></a>Habilitar supervisión
Seleccione **Insights** en el menú de la máquina virtual en Azure Portal. Si todavía no ha habilitado VM Insights para esta función, debería ver una pantalla similar a la siguiente, en la cual podrá habilitar la supervisión. Haga clic en **Habilitar**.

> [!NOTE]
> Si ha seleccionado la opción **Habilitar supervisión detallada** al crear la máquina virtual, es posible que la información de la máquina virtual ya esté habilitada. Seleccione la área de trabajo y haga clic de nuevo en **Habilitar**. Esta es la área de trabajo a la cual se enviarán los datos recopilados por VM Insights.

:::image type="content" source="media/tutorial-monitor-vm/enable-vminsights-02.png" lightbox="media/tutorial-monitor-vm/enable-vminsights-02.png" alt-text="Habilitar VM Insights con el espacio de trabajo":::

Verá un mensaje que indica que se está habilitando la supervisión. El agente puede tardar varios minutos en instalarse y comenzar la recopilación de datos. 

> [!NOTE]
> Puede recibir un mensaje sobre la existencia de una actualización disponible para VM Insights. Si es así, seleccione la opción para realizar la actualización antes de continuar.

## <a name="view-performance"></a>Visualización del rendimiento
Una vez completada la implementación, verá vistas en la pestaña **Rendimiento** en VM Insights con datos de rendimiento para la máquina. Esto muestra los valores de las métricas clave de invitado a lo largo del tiempo. 

:::image type="content" source="media/tutorial-monitor-vm/performance.png" lightbox="media/tutorial-monitor-vm/performance.png" alt-text="Vista de rendimiento de VM Insights":::

## <a name="view-processes-and-dependencies"></a>Visualización de procesos y dependencias
Seleccione la pestaña **Mapas** para ver los procesos y las dependencias de la máquina virtual. La máquina actual está en el centro de la vista. Para ver los procesos que se ejecutan en ella, expanda la pestaña **Procesos**.

:::image type="content" source="media/tutorial-monitor-vm/map-processes.png" lightbox="media/tutorial-monitor-vm/map-processes.png" alt-text="Vista de mapa de VM Insights con procesos":::


## <a name="view-machine-details"></a>Visualización de los detalles de la máquina
La pestaña **Mapas** proporciona pestañas diferentes con información recopilada sobre la máquina virtual. Haga clic en las pestañas para ver lo que está disponible.

:::image type="content" source="media/tutorial-monitor-vm/map-details.png" lightbox="media/tutorial-monitor-vm/map-details.png" alt-text="Vista de mapa de VM Insights con los detalles de la máquina":::

## <a name="next-steps"></a>Pasos siguientes
Ahora que está recopilando datos de la máquina virtual, puede usarlos para crear alertas que le notifiquen de forma proactiva cuando se detecten problemas.

> [!div class="nextstepaction"]
> [Creación de una alerta cuando la máquina virtual de Azure no esté disponible](tutorial-monitor-vm-alert.md)

