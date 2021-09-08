---
title: Introducción a las máquinas virtuales en el dispositivo de GPU de Azure Stack Edge Pro
description: Describe el uso de máquinas virtuales optimizadas para cargas de trabajo con aceleración de GPU en Azure Stack Edge Pro con GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: alkohli
ms.openlocfilehash: d8660ace369fe0da0da384dceae6bff35594df74
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780581"
---
# <a name="gpu-virtual-machines-for-azure-stack-edge-pro-gpu-devices"></a>Máquinas virtuales de GPU para dispositivos de GPU de Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Las cargas de trabajo con aceleración de GPU en un dispositivo de GPU de Azure Stack Edge Pro requieren una máquina virtual de GPU. En este artículo se proporciona información general sobre las máquinas virtuales de GPU, incluidos los sistemas operativos compatibles, los controladores de GPU y los tamaños de máquina virtual. También se analizan las opciones de implementación de las máquinas virtuales de GPU que se usan con clústeres de Kubernetes.

## <a name="about-gpu-vms"></a>Acerca de las máquinas virtuales con GPU

Los dispositivos Azure Stack Edge están equipados con 1 o 2 GPU Tesla T4 de NVIDIA. Para implementar cargas de trabajo de máquinas virtuales con aceleración de GPU en estos dispositivos, use tamaños de máquina virtual optimizados para GPU. Por ejemplo, se debe usar la serie NC T4 v3 para implementar cargas de trabajo de inferencia que incluyen las GPU T4. Para más información, consulte [Serie NCasT4_v3](../virtual-machines/nct4-v3-series.md).

Para aprovechar las funcionalidades de GPU de las máquinas virtuales de la serie N de Azure, se deben instalar los controladores de GPU de Nvidia. La extensión del controlador de GPU de Nvidia instala los controladores CUDA o GRID de Nvidia adecuados. Puede [instalar las extensiones de GPU mediante plantillas o a través de Azure Portal](#gpu-vm-deployment).

Puede [instalar y administrar la extensión mediante las plantillas de Azure Resource Manager](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md) después de implementar una máquina virtual. En Azure Portal, puede instalar la extensión de GPU mientras implementa una máquina virtual o después de hacerlo. Para obtener instrucciones, consulte [Implementación de máquinas virtuales de GPU en el dispositivo de Azure Stack Edge](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md).

Si el dispositivo tendrá configurado un clúster de Kubernetes, asegúrese de revisar las [consideraciones de implementación de los clústeres de Kubernetes](#gpu-vms-and-kubernetes) antes de implementar máquinas virtuales de GPU.

## <a name="supported-os-and-gpu-drivers"></a>Sistema operativo y controladores de GPU admitidos 

Las extensiones de controlador de GPU de Nvidia para Windows y Linux admiten las siguientes versiones del sistema operativo.

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

## <a name="gpu-vm-deployment"></a>Implementación de máquina virtual de GPU

Puede implementar una máquina virtual de GPU a través de Azure Portal o mediante plantillas de Azure Resource Manager. La extensión de GPU se instala después de que se cree la máquina virtual.<!--Wording still needs work!-->

- **Portal**: en Azure Portal, puede [instalar rápidamente la extensión de GPU al crear una máquina virtual](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms) o [después de implementar la máquina virtual]().<!--Can they remove the GPU extension. Tomorrow, create a new GPU VM to test.-->

- **Plantillas**: con las plantillas de Azure Resource Manager, [cree una máquina virtual](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment) y, luego, [instale la extensión de GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md).


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

## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre cómo [implementar máquinas virtuales de GPU](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md).
- Aprenda a [instalar la extensión de GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md) en las máquinas virtuales con GPU que se ejecutan en el dispositivo.
