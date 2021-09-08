---
title: Supervisión de la actividad de las máquina virtual en un dispositivo de GPU de Azure Stack Edge Pro
description: Obtenga información sobre cómo supervisar la actividad de las máquinas virtuales en un dispositivo de GPU de Azure Stack Edge Pro en Azure Portal.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: a73337ad261d8d2d7aef2c6292c7387d7c4cad9a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780583"
---
# <a name="monitor-vm-activity-on-your-azure-stack-edge-pro-gpu-device"></a>Supervisión de la actividad de las máquinas virtuales en un dispositivo de GPU de Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo visualizar los registros de actividad en Azure Portal de las máquinas virtuales en el dispositivo de GPU de Azure Stack Edge Pro.

> [!NOTE]
> Puede ampliar la información sobre el uso de CPU y memoria de una máquina virtual durante períodos de actividad en la pestaña **Métricas** de la máquina virtual. Para obtener más información, consulte [Supervisión de métricas de máquina virtual](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md).

## <a name="view-activity-logs"></a>Visualización de registros de actividad

Para visualizar los registros de actividad de las máquinas virtuales en el dispositivo de GPU de Azure Stack Edge Pro, siga estos pasos:

1. Vaya al dispositivo y, luego, a **Máquinas virtuales**. Seleccione **Registro de actividad**.

    ![Captura de pantalla en la que se muestra el panel Registro de actividad de las máquinas virtuales en un dispositivo de Azure Stack Edge. Aparece resaltado el registro de actividad en el panel de navegación.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-01.png)

    Verá los registros de invitado de máquina virtual para las máquinas virtuales del dispositivo.

1. Use los filtros que aparecen sobre la lista para centrarse en la actividad que necesita ver.

    ![Captura de pantalla en la que se muestran las opciones de Intervalo de tiempo en la página Registros de actividad de las máquinas virtuales. Aparece resaltado el intervalo de tiempo seleccionado.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-02.png)<!--Reshoot to remove pointer. Lightbox treatment?-->

1. Haga clic en la flecha abajo que aparece junto a un nombre de operación para ver la actividad asociada.

    ![Captura de pantalla en la que se muestra el panel Registro de actividad de las máquinas virtuales en un dispositivo de Azure Stack Edge. Aparece resaltada una operación expandida de la lista.](./media/azure-stack-edge-gpu-monitor-virtual-machine-activity/activity-log-03.png)<!--Reshoot to remove pointer. May be able to replace drop-down only.-->

En todos los paneles de **Registro de actividad** de Azure, puede filtrar y ordenar las actividades, seleccionar las columnas que quiere que se muestren, explorar en profundidad los detalles de una actividad específica y obtener **conclusiones rápidas** sobre los errores, las implementaciones con errores, las alertas, el estado del servicio y los cambios de seguridad durante las últimas 24 horas. Para obtener más información sobre los registros y las opciones de filtrado, vea [Visualización de registros de actividad](../azure-resource-manager/management/view-activity-logs.md).

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de implementación de máquinas virtuales](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Recopilación de registros de invitado de máquina virtual en un paquete de soporte técnico](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)