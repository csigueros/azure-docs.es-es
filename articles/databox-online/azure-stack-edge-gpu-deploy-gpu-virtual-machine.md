---
title: Implementación de máquinas virtuales en el dispositivo GPU de Azure Stack Edge Pro
description: Se describe cómo crear e implementar máquinas virtuales (VM) con GPU de Azure Stack Edge Pro mediante Azure Portal o plantillas.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: 958c87a78551555d2994c37e2b72c75cb989a834
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740823"
---
# <a name="deploy-gpu-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Implementación de máquinas virtuales en el dispositivo GPU de Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

En este artículo se explica cómo crear una máquina virtual de GPU en Azure Portal o mediante las plantillas de Azure Resource Manager.

Use Azure Portal para implementar rápidamente una sola máquina virtual de GPU. Puede instalar la extensión de GPU durante o después de la creación de la máquina virtual. O bien, puede usar Azure Resource Manager para implementar y administrar de forma eficaz varias máquinas virtuales de GPU.

## <a name="create-gpu-vms"></a>Creación de máquinas virtuales con GPU

Puede implementar una máquina virtual de GPU mediante el portal o mediante plantillas de Azure Resource Manager.

Para obtener una lista de los sistemas operativos, controladores y tamaños de máquina virtual admitidos para máquinas virtuales de GPU, consulte [¿Qué son las máquinas virtuales de GPU?](azure-stack-edge-gpu-overview-gpu-virtual-machines.md). Para ver las consideraciones de implementación, consulte [Máquinas virtuales de GPU y Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes).


> [!IMPORTANT]
> Si el dispositivo va a ejecutar Kubernetes, no configure Kubernetes antes de implementar las máquinas virtuales de GPU. Si configura Kubernetes en primer lugar, reclamará todos los recursos de GPU disponibles y se producirá un error en la creación de la máquina virtual con GPU. Para ver las consideraciones de implementación de Kubernetes en dispositivos de 1 GPU y 2 GPU, consulte [Máquinas virtuales de GPU y Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes).

### <a name="portal"></a>[Portal](#tab/portal)

Siga estos pasos al implementar máquinas virtuales con GPU en el dispositivo mediante Azure Portal:

1. Para crear máquinas virtuales de GPU, siga todos los pasos descritos en [Implementación de máquinas virtuales en Azure Stack Edge mediante Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md), con estos requisitos de configuración:

    - En la pestaña **Aspectos básicos**, seleccione un [tamaño de VM de la serie NCasT4-v3-series](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

       ![Captura de pantalla de la pestaña Aspectos básicos para "Agregar una máquina virtual" en Azure Stack Edge. La opción Tamaño, con un tamaño de máquina virtual compatible para máquinas virtuales de GPU, está resaltada.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/basics-vm-size-for-gpu.png)

    - Para instalar la extensión de GPU durante la implementación, en la pestaña **Opciones avanzadas**, elija **Seleccionar una extensión para instalar**. A continuación, seleccione una extensión de GPU para instalar. Las extensiones de GPU solo están disponibles para una máquina virtual con un [tamaño de máquina virtual de la serie NCasT4-v3](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).
        
        > [!NOTE]
        > Si usa una imagen de Red Hat, deberá instalar la extensión de GPU después de la implementación de la máquina virtual. Siga los pasos descritos en [Instalación de la extensión de GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md).
    
       ![Ilustración de los pasos para agregar una extensión de GPU a la pestaña Opciones avanzadas de Agregar una máquina virtual. Las opciones para seleccionar y agregar una extensión están resaltadas.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-01.png)

       En la pestaña **Opciones avanzadas** se muestra la extensión seleccionada.

       ![Captura de pantalla de la pestaña Opciones avanzadas de Agregar una máquina virtual. Una extensión de GPU instalada resaltada.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-02.png)

1. Una vez que la máquina virtual con GPU se crea correctamente, puede ver esta máquina virtual en la lista de máquinas virtuales del recurso de Azure Stack Edge en Azure Portal.

    ![Captura de pantalla de la vista "Máquinas virtuales" en Azure Stack Edge con una máquina virtual GPU recién creada resaltada.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

    Seleccione la máquina virtual y explore en profundidad los detalles. Asegúrese de que la extensión de GPU tiene el estado **Correcto**.

    ![Captura de pantalla del panel Detalles de una máquina virtual de Azure Stack Edge. Las extensiones instaladas, con una extensión de GPU instalada, están resaltadas.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/vm-details-extension-installed.png)


### <a name="templates"></a>[Templates](#tab/templates) (Plantillas [C++])

Siga estos pasos al implementar máquinas virtuales de GPU en el dispositivo mediante plantillas de Azure Resource Manager:

1. [Descargue las plantillas de máquina virtual y los archivos de parámetros](https://aka.ms/ase-vm-templates) en la máquina cliente. Descomprímalos en un directorio que usará como directorio de trabajo.

1. Para poder implementar VM en el dispositivo Azure Stack Edge, debe configurar el cliente para que se conecte al dispositivo mediante Azure Resource Manager con Azure PowerShell. Para consultar las instrucciones detalladas, vaya a [Conexión a Azure Resource Manager en un dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-connect-resource-manager.md).

1. Para crear máquinas virtuales de GPU, siga todos los pasos descritos en [Implementación de máquinas virtuales en Azure Stack Edge mediante plantillas](azure-stack-edge-gpu-deploy-virtual-machine-templates.md), con estos requisitos de configuración: 
            
    - Al especificar los tamaños de la máquina virtual con GPU, asegúrese de usar la serie NCasT4-V3 en el archivo `CreateVM.parameters.json`, ya que se admiten para las máquinas virtuales con GPU. Para más información, consulte [Tamaños y tipos de máquina virtual para el dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

       ```json
           "vmSize": {
         "value": "Standard_NC4as_T4_v3"
       },
       ```

    Una vez que la máquina virtual con GPU se crea correctamente, puede ver esta máquina virtual en la lista de máquinas virtuales del recurso de Azure Stack Edge en Azure Portal.

    ![Captura de pantalla de la vista Máquinas virtuales en Azure Stack Edge. Se resalta una máquina virtual de GPU recién creada.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machines-01.png)

1. Seleccione la máquina virtual y explore en profundidad los detalles. Copie la dirección IP asignada a la máquina virtual.

    ![Captura de pantalla del panel Detalles de una máquina virtual de Azure Stack Edge. La dirección IP, en Redes, está resaltada.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-of-virtual-machine.png)

Una vez creada la máquina virtual, puede [implementar la extensión de GPU mediante la plantilla de extensiones](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux).

---

> [!NOTE]
> Al actualizar la versión del software del dispositivo de la versión 2012 a una posterior, tendrá que detener manualmente las máquinas virtuales con GPU.

## <a name="install-gpu-extension-after-deployment"></a>Instalación de la extensión de GPU después de la implementación

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure, se deben instalar los controladores de GPU de Nvidia. Desde Azure Portal, puede instalar la extensión de GPU durante o después de la implementación de la máquina virtual. Si usa plantillas, instalará la extensión de GPU después de crear la máquina virtual.

---

### <a name="portal"></a>[Portal](#tab/portal)

Si no instaló la extensión de GPU cuando creó la máquina virtual, siga estos pasos para instalarla en la máquina virtual implementada:

1. Vaya a la máquina virtual a la que quiere agregar la extensión de GPU.

    ![Captura de pantalla de la vista "Máquinas virtuales" para un dispositivo de Azure Stack Edge, con una máquina virtual resaltada en la lista de máquinas virtuales.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-01.png)
  
1. En **Detalles**, seleccione **+ Agregar extensión**. A continuación, seleccione una extensión de GPU para instalar.

    Las extensiones de GPU solo están disponibles para una máquina virtual con un [tamaño de máquina virtual de la serie NCasT4-v3](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview). Si lo prefiere, puede [instalar la extensión de GPU después de la implementación](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment).

![Ilustración en la que se muestran 2 pasos para usar el botón "Más agregar extensión" en el panel "Detalles" de la máquina virtual para agregar una extensión de GPU a una máquina virtual en un dispositivo de Azure Stack Edge.](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/add-extension-after-deployment-02.png)

> [!Note]
> No se puede eliminar una extensión de GPU mediante el portal. En su lugar, use el cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension?view=azurermps-6.13.0&preserve-view=true) en Azure PowerShell. Para obtener instrucciones, consulte [Eliminación de la extensión de GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md#remove-gpu-extension).

### <a name="templates"></a>[Templates](#tab/templates) (Plantillas [C++])

Cuando se crea una máquina virtual de GPU mediante plantillas, se instala la extensión de GPU después de la implementación. Para obtener pasos detallados para usar plantillas para implementar una extensión de GPU en una máquina virtual Windows o una máquina virtual Linux, consulte [Instalación de la extensión de GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md).

---

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de implementación de máquinas virtuales](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md)
- [Solución de problemas de extensiones de GPU](azure-stack-edge-gpu-troubleshoot-virtual-machine-gpu-extension-installation.md)
- [Supervisión de la actividad de VM en el dispositivo](azure-stack-edge-gpu-monitor-virtual-machine-activity.md)
- [Supervisión de CPU y memoria en una VM](azure-stack-edge-gpu-monitor-virtual-machine-metrics.md)
