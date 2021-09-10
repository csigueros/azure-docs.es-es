---
title: Supervisión de la CPU y la memoria de las máquinas virtuales de dispositivos Azure Stack Edge Pro con GPU
description: Aprenda a supervisar las métricas de CPU y memoria de las máquinas virtuales de dispositivos Azure Stack Edge Pro con GPU en Azure Portal.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: c58b7fd1cf4ab08dd8d645e4fc5f537d9ee937e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780582"
---
# <a name="monitor-vm-metrics-for-cpu-memory-on-azure-stack-edge-pro-gpu"></a>Supervisión de las métricas de CPU y memoria de las máquinas virtuales de dispositivos Azure Stack Edge Pro con GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo supervisar las métricas de CPU y memoria de las máquinas virtuales de dispositivos Azure Stack Edge Pro con GPU.

## <a name="about-vm-metrics"></a>Información sobre las métricas de máquina virtual

La pestaña **Métricas** de una máquina virtual permite ver las métricas de CPU y memoria, ajustar el período de tiempo y hacer zoom en períodos de interés.

Las métricas de máquina virtual se basan en los datos de uso de CPU y memoria recopilados del sistema operativo invitado de la máquina virtual. El uso de recursos se muestrea una vez por minuto.

Si un dispositivo está desconectado, las métricas se almacenan en caché en el dispositivo. Cuando se vuelve a conectar el dispositivo, las métricas se insertan desde la caché y las **métricas** de máquina virtual se actualizan.

## <a name="monitor-cpu-and-memory-metrics"></a>Supervisión de las métricas de memoria y CPU

1. Abra el dispositivo en Azure Portal y vaya a **Máquinas virtuales**. Seleccione la máquina virtual y elija **Métricas**.

    ![Captura de pantalla que muestra la pestaña Métricas de una máquina virtual de un dispositivo Azure Stack Edge. La pestaña Métricas está resaltada.](media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-01.png)

2. De forma predeterminada, los gráficos muestran el uso medio de CPU y memoria de la hora anterior. Para ver los datos de un período de tiempo diferente, seleccione otra opción junto a **Mostrar datos del último período**.

    ![Captura de pantalla de la pestaña Métricas de una máquina virtual en un dispositivo Azure Stack Edge. Se resaltan la opción "Mostrar datos del último período" y el valor seleccionado.](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-02.png)

3. Apunte a cualquier parte del gráfico con el mouse para que se muestre una línea vertical con una mano que puede mover a la izquierda o a la derecha para ver una muestra de datos anterior o posterior. Haga clic para abrir una vista de detalles de ese período de tiempo.

    ![Captura de pantalla que muestra la pestaña Métricas de una máquina virtual. El puntero que se muestra al mantener el puntero sobre un área de un gráfico está resaltado.](./media/azure-stack-edge-gpu-monitor-virtual-machine-metrics/metrics-03.png)


## <a name="next-steps"></a>Pasos siguientes

- [Supervisar la actividad de la máquina virtual en el dispositivo](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Recopilar registros de invitados de máquina virtual en un paquete de soporte técnico](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)
