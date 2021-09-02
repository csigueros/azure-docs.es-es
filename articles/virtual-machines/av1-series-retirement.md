---
title: Retirada de la serie Av1
description: Información de retirada de los tamaños de máquina virtual de la serie Av1.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: mimckitt
ms.openlocfilehash: c382954cf54779350e78fdadef7d0f4738dca48f
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634669"
---
# <a name="av1-series-retirement"></a>Retirada de la serie Av1

El 31 de agosto de 2024, retiraremos las máquinas virtuales de las series Basic y Standard A. Antes de esa fecha, deberá migrar las cargas de trabajo a máquinas virtuales de la serie Av2, que proporcionan más memoria por vCPU y almacenamiento más rápido en unidades de estado sólido (SSD).

> [!NOTE]
> En algunos casos, los usuarios deben desasignar la máquina virtual antes de cambiar el tamaño. Esto puede suceder si el nuevo tamaño no está disponible en el clúster de hardware que hospeda la actualmente la máquina virtual.


## <a name="migrate-workloads-from-basic-and-standard-a-series-vms-to-av2-series-vms"></a>Migración de cargas de trabajo de máquinas virtuales de las series Basic y Standard A a máquinas virtuales de la serie Av2 

Puede cambiar el tamaño de las máquinas virtuales a la serie Av2 mediante [Azure Portal](https://portal.azure.com), [PowerShell](windows/resize-vm.md) y la [CLI](linux/change-vm-size.md). A continuación se muestran ejemplos sobre cómo cambiar el tamaño de la máquina virtual mediante Azure Portal y PowerShell. 

> [!IMPORTANT]
> El nuevo tamaño de la máquina virtual dará lugar a un reinicio. Se recomienda realizar acciones que darán lugar a un reinicio en las horas de menos actividad del horario comercial. 

### <a name="azure-portal"></a>Azure Portal 
1. Abra [Azure Portal](https://portal.azure.com).
1. Escriba **máquinas virtuales** en la búsqueda.
1. En **Servicios**, seleccione **Máquinas virtuales**.
1. En la página **Máquinas virtuales**, seleccione la máquina virtual que desea reiniciar.
1. En el menú de la izquierda, seleccione **Tamaño**.
1. Elija un tamaño Av2 nuevo en la lista de tamaños disponibles y seleccione **Cambiar tamaño**.

### <a name="azure-powershell"></a>Azure PowerShell
1. Establezca el grupo de recursos y las variables de nombre de la máquina virtual. Rellene los valores con la información de la máquina virtual a la que desea cambiar el tamaño. 

    ```powershell
    $resourceGroup = "myResourceGroup"
    $vmName = "myVM"
    ```
2. Muestre los tamaños de máquina virtual que están disponibles en el clúster de hardware donde se hospeda la máquina virtual.

    ```powershell
    Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName
    ```

3. Asigne el nuevo tamaño a la máquina virtual.

    ```powershell
    $vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
    $vm.HardwareProfile.VmSize = "<newAv2VMsize>"
    Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="help-and-support"></a>Ayuda y soporte técnico

Si tiene alguna pregunta, diríjase a los expertos de la comunidad en [Microsoft Q&A](/answers/topics/azure-virtual-machines.html). Si tiene un plan de asistencia y necesita ayuda técnica, cree una [solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

1. En Tipo de problema, especifique Técnico.
1. En Suscripción, seleccione la suscripción.
1. En Servicio, haga clic en Mis servicios.
1. En Tipo de servicio, seleccione Máquina virtual que ejecuta Windows o Linux.
1. En Resumen, introduzca un resumen de la solicitud.
1. En Tipo de problema, seleccione Asistencia para cambiar el tamaño de mi máquina virtual.
1. En Subtipo de problema, seleccione la opción que le convenga.

## <a name="next-steps"></a>Pasos siguientes
Más información sobre las [máquinas virtuales de la serie Av2](av2-series.md)
