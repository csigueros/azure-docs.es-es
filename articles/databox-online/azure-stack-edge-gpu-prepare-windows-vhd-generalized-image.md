---
title: Preparación de una imagen generalizada a partir de un disco VHD con Windows para implementar máquinas virtuales en Azure Stack Edge Pro GPU
description: Explica cómo crear una imagen de máquina virtual generalizada a partir de un disco VHD o VHDX con Windows. Utilice esta imagen de máquina virtual generalizada para implementar máquinas virtuales en un dispositivo de Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/18/2021
ms.author: alkohli
ms.openlocfilehash: e60f7e7195ef81b3bf17a85c5662824df945ef91
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112968466"
---
# <a name="prepare-generalized-image-from-windows-vhd-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>Preparación de una imagen generalizada a partir de un disco VHD con Windows para implementar máquinas virtuales en Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Para implementar máquinas virtuales en un dispositivo Azure Stack Edge Pro GPU, debe poder crear imágenes de máquina virtual personalizadas que puede usar para crear máquinas virtuales. En este artículo, se explica cómo preparar una imagen generalizada a partir de un disco VHD o VHDX con Windows, que puede usar para implementar máquinas virtuales en dispositivos Stack Edge Pro GPU con Windows.

Para preparar una imagen de máquina virtual generalizada usando una imagen ISO, vea [Preparación de una imagen generalizada a partir de una imagen ISO para implementar máquinas virtuales en Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md).

## <a name="about-vm-images"></a>Acerca de las imágenes de máquinas virtuales

Se puede utilizar un archivo VHD o VHDX de Windows para crear una imagen *especializada* o *generalizada*. En la tabla siguiente se resumen las diferencias principales entre las imágenes *especializadas* y *generalizadas*.

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Flujo de trabajo

En líneas generales, el flujo de trabajo para preparar un disco VHD con Windows con el fin de usarlo como una imagen generalizada, a partir del disco VHD o VHDX de una máquina virtual, tiene los siguientes pasos:

1. Preparar la máquina virtual de origen a partir del disco VHD con Windows:
   1. Convertir el disco VHD o VHDX de origen en un disco VHD de tamaño fijo.
   1. Usar ese disco VHD para crear una nueva máquina virtual.<!--Can this procedure be generalized and moved to an include file?-->
1. Iniciar la máquina virtual e instalar el sistema operativo Windows.
1. Generalice el VHD mediante la utilidad *sysprep*.
1. Copie la imagen generalizada en Blob Storage.

## <a name="prerequisites"></a>Requisitos previos

Antes de preparar un disco VHD con Windows para usarlo como imagen generalizada en un dispositivo Azure Stack Edge Pro GPU, asegúrese de que:

- Tiene un VHD o un VHDX que contenga una versión compatible de Windows. 
- Tiene acceso a un cliente de Windows con el administrador de Hyper-V instalado. 
- Tiene acceso a una cuenta de Azure Blob Storage para almacenar el VHD una vez preparado.

## <a name="prepare-source-vm-from-windows-vhd"></a>Preparación de la máquina virtual de origen a partir de un disco VHD con Windows

Cuando el origen de la máquina virtual es un disco VHD o VHDX con Windows, primero debe convertir el disco en un disco VHD de tamaño fijo. Usará el disco VHD de tamaño fijo para crear una nueva máquina virtual.

> [!IMPORTANT]
> Estos procedimientos no cubren los casos en los que el disco VHD de origen está configurado con opciones personalizadas. Por ejemplo, es posible que se necesiten acciones adicionales para generalizar un VHD que contenga reglas de firewall personalizadas o valores de proxy. Para más información sobre estas acciones adicionales, consulte el artículo sobre la [preparación de un VHD de Windows para cargarlo en Azure - Azure Virtual Machines](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

#### <a name="convert-source-vhd-to-a-fixed-size-vhd"></a>Conversión del disco VHD de origen en un disco VHD de tamaño fijo

En su dispositivo, necesitará VHD de tamaño fijo para crear imágenes de máquina virtual. Deberá convertir el VHD o el VHDX de Windows de origen a un VHD fijo. 

Siga estos pasos:

1. Abra el administrador de Hyper-V en el sistema cliente. Vaya a **Editar disco**.

    ![Apertura del administrador de Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. En la página **Antes de comenzar**, seleccione **Siguiente>** .

1. En la página **Localizar disco duro virtual**, busque la ubicación del VHD o el VHDX de origen de Windows que desee convertir. Seleccione **Siguiente>** .

    ![Página Localizar disco duro virtual](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. En la página **Elegir acción**, seleccione **Convertir** y, después, **Siguiente>** .

    ![Página Elegir acción](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. En la página **Elegir formato de disco**, seleccione el formato **VHD** y, a continuación, **Siguiente>** .

   ![Página Elegir formato de disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)

1. En la página **Elegir tipo de disco**, seleccione **Tamaño fijo** y, después, **Siguiente>** .

   ![Página Elegir tipo de disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)

1. En la página **Configurar disco**, busque la ubicación y especifique un nombre para el disco VHD de tamaño fijo. Seleccione **Siguiente>** .

   ![Página Configurar disco](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)

1. Revise el resumen y seleccione **Finalizar**. La conversión de VHD o VHDX tarda unos minutos. El tiempo de conversión depende del tamaño del disco de origen.

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Usará este disco VHD de tamaño fijo para todos los pasos siguientes de este artículo.

#### <a name="create-hyper-v-vm-from-the-fixed-size-vhd"></a>Creación de una máquina virtual Hyper-V a partir del disco VHD de tamaño fijo

1. En el **Administrador de Hyper-V**, en el panel de ámbito, haga clic con el botón derecho en el nodo del sistema para abrir el menú contextual y seleccione luego **Nuevo** > **Máquina virtual**.

    ![Selección de una nueva máquina virtual en el panel de ámbito](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. En la página **Antes de comenzar** del Asistente para nueva máquina virtual, seleccione **Siguiente**.

1. En la página **Especificar nombre y ubicación**, escriba un **nombre** y una **ubicación** para la máquina virtual. Seleccione **Next** (Siguiente).

    ![Especificación del nombre y de la ubicación de la máquina virtual](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. En la página **Especificar generación**, elija **Generación 1** como tipo de imagen del dispositivo .vhd y haga clic en **Siguiente**.    

    ![Especificación de la generación](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Asigne las configuraciones de red y memoria que desee.

1. En la página **Conectar disco duro virtual**, elija **Usar un disco duro virtual existente**, especifique la ubicación del VHD fijo de Windows anteriormente creado y haga clic en **Siguiente**.

    ![Página Conectar disco duro virtual](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Revise la sección **Resumen** y seleccione luego **Finalizar** para crear la máquina virtual.

La creación de la máquina virtual tarda varios minutos.

La máquina virtual se muestra en la lista de máquinas virtuales del sistema cliente.

## <a name="start-vm-and-install-operating-system"></a>Inicio de la máquina virtual e instalación del sistema operativo

Para terminar de crear la máquina virtual, debe iniciarla e instalar el sistema operativo.

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

Una vez conectado a la máquina virtual, complete el asistente para la configuración de la máquina y, después, inicie sesión en la máquina virtual.<!--It's not clear what they are doing here. Where does the Machine setup wizard come in?-->

## <a name="generalize-the-vhd"></a>Generalización del disco duro virtual

Use la utilidad *sysprep* para generalizar el VHD. 

[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]

El disco VHD se puede usar ahora para crear una imagen generalizada para usarla en Azure Stack Edge Pro GPU.

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>Carga del disco VHD generalizado en Azure Blob Storage

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->

## <a name="next-steps"></a>Pasos siguientes

En función de la naturaleza de la implementación, puede elegir uno de los procedimientos siguientes.

- [Implementación de máquinas virtuales a partir de una imagen generalizada mediante Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Preparación de una imagen generalizada a partir de una imagen ISO para implementar máquinas virtuales en Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [Implementación de una máquina virtual a partir de una imagen especializada en el dispositivo Azure Stack Edge Pro GPU a través de Azure PowerShell](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
