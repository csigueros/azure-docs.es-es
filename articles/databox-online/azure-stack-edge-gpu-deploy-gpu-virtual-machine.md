---
title: Introducción a las máquinas virtuales con GPU en el dispositivo GPU de Azure Stack Edge Pro y cómo implementarlas
description: Se describe cómo crear y administrar máquinas virtuales (VM) con GPU en un dispositivo GPU de Azure Stack Edge Pro mediante plantillas.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/28/2021
ms.author: alkohli
ms.openlocfilehash: 754cb296d6edebe4a8026df612fc52113e171a1c
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2021
ms.locfileid: "110663882"
---
# <a name="deploy-gpu-vms-on-your-azure-stack-edge-pro-gpu-device"></a>Implementación de máquinas virtuales en el dispositivo GPU de Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

En este artículo se proporciona información general sobre las máquinas virtuales con GPU en el dispositivo GPU de Azure Stack Edge Pro. También se describe cómo crear una máquina virtual con GPU mediante las plantillas de Azure Resource Manager. 


## <a name="about-gpu-vms"></a>Acerca de las máquinas virtuales con GPU

Los dispositivos Azure Stack Edge están equipados con 1 o 2 GPU Tesla T4 de NVIDIA. Para implementar cargas de trabajo de máquinas virtuales con aceleración de GPU en estos dispositivos, use tamaños de máquina virtual optimizados para GPU. Por ejemplo, se debe usar la serie NC T4 v3 para implementar cargas de trabajo de inferencia que incluyen las GPU T4. 

Para más información, consulte [Serie NCasT4_v3](../virtual-machines/nct4-v3-series.md).

## <a name="supported-os-and-gpu-drivers"></a>Sistema operativo y controladores de GPU admitidos 

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure, se deben instalar los controladores de GPU de Nvidia. 

La extensión del controlador de GPU de Nvidia instala los controladores CUDA o GRID de Nvidia adecuados. Puede instalar o administrar la extensión mediante las plantillas de Azure Resource Manager.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Sistema operativo admitido para la extensión de GPU para Windows

Esta extensión admite los siguientes sistemas operativos. Otras versiones pueden funcionar, pero no se han probado internamente en máquinas virtuales con GPU que se ejecutan en dispositivos Azure Stack Edge.

| Distribución | Versión |
|---|---|
| Windows Server 2019 | Core |
| Windows Server 2016 | Core |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Sistema operativo admitido para la extensión de GPU para Linux

Esta extensión admite las siguientes distribuciones del sistema operativo, en función de la compatibilidad del controlador para la versión específica del sistema operativo. Otras versiones pueden funcionar, pero no se han probado internamente en máquinas virtuales con GPU que se ejecutan en dispositivos Azure Stack Edge.


| Distribución | Versión |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |


## <a name="gpu-vms-and-kubernetes"></a>Máquinas virtuales con GPU y Kubernetes

Antes de implementar máquinas virtuales con GPU en el dispositivo y, si Kubernetes está configurado en el dispositivo, revise las siguientes consideraciones.

#### <a name="for-1-gpu-device"></a>Para un dispositivo con 1 GPU: 

- **Cree una máquina virtual con GPU seguida de la configuración de Kubernetes en el dispositivo**: en este escenario, la creación de la máquina virtual con GPU y la configuración de Kubernetes se realizarán correctamente. En este caso, Kubernetes no tendrá acceso a la GPU.

- **Configure Kubernetes en el dispositivo y, después, cree una máquina virtual con GPU**: en este escenario, Kubernetes reclamará la GPU del dispositivo y se producirá un error en la creación de la máquina virtual, ya que no hay ningún recurso de GPU disponible.

#### <a name="for-2-gpu-device"></a>Para un dispositivo con 2 GPU:

- **Cree una máquina virtual con GPU seguida de la configuración de Kubernetes en el dispositivo**: en este escenario, la máquina virtual con GPU que cree reclamará una GPU del dispositivo y la configuración de Kubernetes también se realizará correctamente y reclamará la GPU restante. 

- **Cree dos máquinas virtuales con GPU seguida de la configuración de Kubernetes en el dispositivo**: en este escenario, las dos máquinas virtuales con GPU reclamarán las dos GPU del dispositivo y Kubernetes se configurará correctamente sin GPU. 

- **Configure Kubernetes en el dispositivo y, después, cree una máquina virtual con GPU**: en este escenario, Kubernetes reclamará ambas GPU del dispositivo y se producirá un error en la creación de la máquina virtual, ya que no hay ningún recurso de GPU disponible.

<!--Li indicated that this is fixed. If you have GPU VMs running on your device and Kubernetes is also configured, then anytime the VM is deallocated (when you stop or remove a VM using Stop-AzureRmVM or Remove-AzureRmVM), there is a risk that the Kubernetes cluster will claim all the GPUs available on the device. In such an instance, you will not be able to restart the GPU VMs deployed on your device or create GPU VMs. -->


## <a name="create-gpu-vms"></a>Creación de máquinas virtuales con GPU

Siga estos pasos al implementar máquinas virtuales con GPU en el dispositivo:

1. Identifique si el dispositivo también va a ejecutar Kubernetes. Si el dispositivo va a ejecutar Kubernetes, primero deberá crear la máquina virtual con GPU y luego configurar Kubernetes. Si Kubernetes se configura en primer lugar, reclamará todos los recursos de GPU disponibles y se producirá un error en la creación de la máquina virtual con GPU.

1. [Descargue las plantillas de máquina virtual y los archivos de parámetros](https://aka.ms/ase-vm-templates) en la máquina cliente. Descomprímalos en un directorio que usará como directorio de trabajo.

1. Para poder implementar VM en el dispositivo Azure Stack Edge, debe configurar el cliente para que se conecte al dispositivo mediante Azure Resource Manager con Azure PowerShell. Para consultar las instrucciones detalladas, vaya a [Conexión a Azure Resource Manager en un dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-connect-resource-manager.md).

1. Para crear máquinas virtuales con GPU, siga todos los pasos descritos en [Implementación de máquinas virtuales en Azure Stack Edge mediante plantillas](azure-stack-edge-gpu-deploy-virtual-machine-templates.md) o [Implementación de máquinas virtuales en Azure Stack Edge mediante Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md), excepto por las siguientes diferencias: 

            
    1. Si crea una máquina virtual mediante las plantillas, al especificar los tamaños de máquina virtual con GPU, asegúrese de usar la serie NCasT4-V3 en el archivo `CreateVM.parameters.json`, ya que se admite para máquinas virtuales con GPU. Para más información, consulte [Tamaños y tipos de máquina virtual para el dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview).

        ```json
            "vmSize": {
          "value": "Standard_NC4as_T4_v3"
        },
        ```
        Si usa Azure Portal para crear la máquina virtual, también puede seleccionar un tamaño de máquina virtual de la serie NCasT4-v3.

    1. Una vez que la máquina virtual con GPU se crea correctamente, puede ver esta máquina virtual en la lista de máquinas virtuales del recurso de Azure Stack Edge en Azure Portal.

        ![Máquina virtual con GPU en la lista de máquinas virtuales de Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/list-virtual-machine-1.png)

1. Seleccione la máquina virtual y explore en profundidad los detalles. Copie la dirección IP asignada a la máquina virtual.

    ![Dirección IP asignada a la máquina virtual con GPU en Azure Portal](media/azure-stack-edge-gpu-deploy-gpu-virtual-machine/get-ip-gpu-virtual-machine-1.png)

1. Si es necesario, puede volver a cambiar la red de proceso. 


Una vez creada la máquina virtual, puede implementar la extensión de GPU mediante la plantilla de extensiones.


> [!NOTE]
> Al actualizar la versión del software del dispositivo de la versión 2012 a una posterior, tendrá que detener manualmente las máquinas virtuales con GPU.



## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [instalar la extensión de GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md) en las máquinas virtuales con GPU que se ejecutan en el dispositivo.
