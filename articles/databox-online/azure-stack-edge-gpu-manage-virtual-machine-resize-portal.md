---
title: Cambiar tamaño de las máquinas virtuales en Azure Stack Edge Pro GPU, Pro R y Mini R a través del Azure Portal
description: Obtenga información sobre cómo cambiar el tamaño de las máquinas virtuales (VM) que se ejecutan en su Azure Stack GPU de Power Bi Pro, Azure Stack Edge Pro R Azure Stack Edge Mini R a través del Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 5e41edf0cf080d06a0829e31e7105a13a0035f93
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736746"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Uso de Azure Portal para cambiar tamaño de las máquinas virtuales de Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se explica cómo cambiar el tamaño de las máquinas virtuales (VM) implementadas en el dispositivo GPU de Azure Stack Edge Pro.

       
## <a name="about-vm-sizing"></a>Acerca del tamaño de la máquina virtual

El tamaño de la máquina virtual determina la cantidad de recursos de proceso (como memoria, CPU y GPU) que están disponibles para la máquina virtual. Debe crear máquinas virtuales con un tamaño adecuado para la carga de trabajo de la aplicación. 

Aunque todas las máquinas se ejecutarán en el mismo hardware, los tamaños de máquina tienen límites diferentes para el acceso al disco. lo que puede ayudarle a administrar el acceso total al disco en todas las máquinas virtuales. Si una carga de trabajo aumenta, también se puede cambiar el tamaño de una máquina virtual existente.

Para más información, consulte [Tamaños y tipos de máquina virtual para el dispositivo](azure-stack-edge-gpu-virtual-machine-sizes.md).


## <a name="prerequisites"></a>Requisitos previos

Antes de cambiar el tamaño de una máquina virtual que se ejecuta en el dispositivo a través del Azure Portal, asegúrese de que:

1. Tiene al menos una máquina virtual implementada en el dispositivo. Para crear esta máquina virtual, vea las instrucciones de [Implementación de máquinas virtuales en el dispositivo Azure Stack Edge Pro con GPU a través de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).

1. La máquina virtual debería estar en estado **Detenido**. Para detener la máquina virtual, vaya a **Máquinas virtuales > Información general** y seleccione la máquina virtual que quiere detener. En la página de información general, seleccione **Detener** y luego **Sí** cuando se le pida confirmación. Antes de cambiar el tamaño de la máquina virtual, debe detener la máquina virtual.

    ![Captura de la pantalla para detener una VM desde la introducción de Virtual Machines. El botón Sí está resaltado.](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>Cambiar el tamaño de una máquina virtual

Siga estos pasos para cambiar el tamaño de una máquina virtual implementada en el dispositivo. 

1. Vaya a la máquina virtual que ha detenido y seleccione **Tamaño de VM (cambiar)** en la pestaña **Detalles** de la máquina virtual.
    
    ![Captura de pantalla de la pestaña Detalles de una máquina virtual. La pestaña Detalles y la opción Tamaño de VM están resaltadas.](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. En la hoja **Cambiar el tamaño de la máquina virtual**, en la barra de comandos, seleccione **el tamaño de la máquina virtual** y luego seleccione **Cambiar**.

    ![Captura de la pantalla Cambiar tamaño de VM. Se resalta un tamaño de VM, al igual que el botón Cambiar.](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. Verá una notificación de que la máquina virtual está actualizada. Una vez que la máquina virtual se haya actualizado correctamente, se actualizará la página de **información general** para mostrar la máquina virtual cuyo tamaño se ha cambiado.

    ![Captura de pantalla de la página de información general de una VM. El valor de Tamaño de VM de la VM cuyo tamaño ha cambiado está resaltado.](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo implementar máquinas virtuales en el dispositivo de Azure Stack Edge Pro, consulte [Implementación de máquinas virtuales a través de Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
