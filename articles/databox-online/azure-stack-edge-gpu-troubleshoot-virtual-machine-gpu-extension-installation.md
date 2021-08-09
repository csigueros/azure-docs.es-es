---
title: Solución de problemas de extensión de GPU para VM de GPU en GPU de Azure Stack Edge Pro
description: Describe cómo solucionar problemas de instalación de la extensión de GPU para VM de GPU en GPU de Azure Stack Edge Pro.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/02/2021
ms.author: alkohli
ms.openlocfilehash: 256902ef26328050b4ed52053f465571974ffefe
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111422163"
---
# <a name="troubleshoot-gpu-extension-issues-for-gpu-vms-on-azure-stack-edge-pro-gpu"></a>Solución de problemas de extensión de GPU para VM de GPU en GPU de Azure Stack Edge Pro

[!INCLUDE [applies-to-gpu-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

En este artículo encontrará una guía para resolver los problemas más comunes que hacen que se produzcan errores en la instalación de la extensión de GPU en una VM de GPU en un dispositivo GPU de Azure Stack Edge Pro.

Para ver los pasos de instalación, consulte [Instalación de la extensión de GPU](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux).

## <a name="vm-size-is-not-gpu-vm-size"></a>El tamaño de la VM no es el tamaño de la VM de GPU

**Descripción del error:** Una VM de GPU debe tener el tamaño Standard_NC4as_T4_v3 o Standard_NC8as_T4_v3. Si se usa cualquier otro tamaño de VM, no se podrá conectar la extensión de GPU.

**Solución sugerida:** Cree una VM con el tamaño de VM Standard_NC4as_T4_v3 o Standard_NC8as_T4_v3. Para más información, consulte [Tamaños y tipos de máquina virtual para el dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview). Para información sobre cómo especificar el tamaño, consulte [Creación de VM de GPU](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).


## <a name="image-os-is-not-supported"></a>No se admite el sistema operativo de la imagen

**Descripción del error:** La extensión de GPU no admite el sistema operativo que está instalado en la imagen de la VM. 

**Solución sugerida:** Prepare una nueva imagen de VM que tenga un sistema operativo compatible con la extensión de GPU. 

* Para obtener una lista de los sistemas operativos compatibles, consulte [Sistema operativo y controladores de GPU admitidos para VM de GPU](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#supported-os-and-gpu-drivers).

* Para ver los requisitos de preparación de imágenes para una VM de GPU, consulte [Creación de VM de GPU](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).


## <a name="extension-parameter-is-incorrect"></a>El parámetro de la extensión no es correcto

**Descripción del error:** Se usó una configuración de extensión incorrecta al implementar la extensión de GPU en una VM Linux. 

**Solución sugerida:** Edite el archivo de parámetros antes de implementar la extensión de GPU. Para más información, consulte [Instalación de la extensión de GPU](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux).


## <a name="vm-extension-installation-failed-in-downloading-package"></a>Error en la instalación de la extensión de VM al descargar el paquete

**Descripción del error:** Hubo un error en el aprovisionamiento de la extensión durante la instalación de la extensión o mientras se encontraba en el estado Habilitado.

1. Compruebe el registro de invitados para ver el error asociado. <!--To collect the guest logs, see [Collect guest logs for VMs on an Azure Stack Edge Pro](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md).-->

   En una VM Linux:
   * Busque en `/var/log/waagent.log` o `/var/log/azure/nvidia-vmext-status`.

   En una máquina virtual de Windows:
   * Descubra el estado del error en `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status`.
   * Revise el registro de ejecución completo: `C:\WindowsAzure\Logs\WaAppAgent.txt`.

   Si hubo un error en la instalación durante la descarga del paquete, ese error indica que la VM no pudo acceder a la red pública para descargar el controlador.

**Solución propuesta:**

1.  Habilite el proceso en un puerto que esté conectado a Internet. Para obtener instrucciones, consulte [Creación de VM de GPU](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms).

1.  Detenga la VM en el portal para desasignarla. Para detener la VM, vaya a **Máquina virtual** > **Información general** y seleccione la VM. Luego, en la página de propiedades de la VM, seleccione **Detener**.<!--Follow-up (formatting): Create an include file for stopping a VM. Use it here and in prerequisites for "Use the Azure portal to manage network interfaces on the VMs" (https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal#prerequisites).-->
 
1.  Cree una máquina virtual.


## <a name="vm-extension-failed-with-error-dpkg-is-usedyum-lock-is-used-linux-vm"></a>Error `dpkg is used/yum lock is used` en la extensión de la VM (VM Linux)

**Descripción del error:** Error en la implementación de la extensión de GPU en una VM Linux porque otro proceso estaba usando `dpkg` u otro proceso creó un `yum lock`. 

**Solución sugerida:** Para resolver el problema, siga estos pasos:

1.  Para averiguar qué proceso está aplicando el bloqueo, busque en el registro \var\log\azure\nvidia-vmext-status un error, como "dpkg is used by another process" (Otro proceso está usando dpkg) o "Another app is holding yum lock" (Otra aplicación está manteniendo el bloqueo de yum).

1. Espere a que termine el proceso o finalícelo.

1.  [Instale la extensión de GPU](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux) nuevamente.

1.  Si se vuelve a producir un error en la implementación de la extensión, cree una nueva VM y asegúrese de que el bloqueo no esté presente antes de instalar la extensión de GPU.


## <a name="next-steps"></a>Pasos siguientes

- [Instalación de la extensión de GPU](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)<!--Temporary link until next one can be restored.-->
<!-- Remove link while cmdlet is fixed. - [Collect guest logs, and create a Support package](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)-->
