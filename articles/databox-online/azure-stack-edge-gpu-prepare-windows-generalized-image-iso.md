---
title: Preparación de una imagen generalizada a partir de una imagen ISO para implementar máquinas virtuales en Azure Stack Edge Pro con GPU
description: Describe cómo crear una imagen generalizada de máquina virtual Windows a partir de una imagen ISO. Utilice esta imagen generalizada para implementar máquinas virtuales en el dispositivo Azure Stack Edge Pro con GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/25/2021
ms.author: alkohli
ms.openlocfilehash: 22f9c86342428d3d011eb787745f74ac6ea026f8
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112970418"
---
# <a name="prepare-generalized-image-from-iso-to-deploy-vms-on-azure-stack-edge-pro-gpu"></a>Preparación de una imagen generalizada a partir de una imagen ISO para implementar máquinas virtuales en Azure Stack Edge Pro con GPU 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Para implementar máquinas virtuales en el dispositivo Azure Stack Edge Pro con GPU, debe ser capaz de crear imágenes de máquina virtual personalizadas que pueda usar para crear máquinas virtuales. En este artículo, se describe cómo preparar una imagen de máquina virtual Windows mediante medios de instalación ISO y, a continuación, generalizar esa imagen para que pueda usarla para implementar varias máquinas virtuales nuevas en el dispositivo Azure Stack Edge Pro con GPU.

Para preparar una imagen generalizada creada a partir de un archivo VHD o VHDX de Windows, consulte [Uso de una imagen generalizada de un VHD de Windows para crear una imagen de máquina virtual para el dispositivo de Azure Stack Edge Pro](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md).

## <a name="about-vm-images"></a>Acerca de las imágenes de máquinas virtuales

Se puede utilizar un archivo VHD o VHDX de Windows para crear una imagen *especializada* o *generalizada*. En la tabla siguiente se resumen las diferencias principales entre las imágenes *especializadas* y *generalizadas*.

[!INCLUDE [about-vm-images-for-azure-stack-edge](../../includes/azure-stack-edge-about-vm-images.md)]

## <a name="workflow"></a>Flujo de trabajo 

El flujo de trabajo general para crear un VHD de Windows generalizado mediante una imagen ISO es:

1. Prepare la máquina virtual de origen mediante una imagen ISO:
   1. Cree un nuevo VHD de tamaño fijo en blanco en el Administrador de Hyper-V.
   1. Use ese VHD para crear una nueva máquina virtual.
   1. Monte la imagen ISO en la unidad de DVD de la nueva máquina virtual.
1. Inicie la máquina virtual e instale el sistema operativo Windows.
1. Generalice el VHD mediante la utilidad *sysprep*.
1. Copie la imagen generalizada en Azure Blob Storage.

## <a name="prerequisites"></a>Requisitos previos

Para poder crear un VHD de Windows generalizado mediante una imagen ISO, asegúrese de que:

- Tiene una imagen ISO de la versión de Windows compatible que desea convertir en un VHD generalizado. Las imágenes ISO de Windows se pueden descargar desde el [Centro de evaluación de Microsoft](https://www.microsoft.com/en-us/evalcenter/).

- Tiene acceso a un cliente de Windows con el administrador de Hyper-V instalado.

- Tiene acceso a una cuenta de Azure Blob Storage para almacenar el VHD una vez preparado.

## <a name="prepare-source-vm-using-an-iso"></a>Preparación de la máquina virtual de origen mediante una imagen ISO

Cuando se usa una imagen ISO para instalar el sistema operativo en la imagen de máquina virtual, se crea en primer lugar un VHD de tamaño fijo en blanco en el Administrador de Hyper-V. A continuación, se utiliza ese VHD para crear una nueva máquina virtual. Después, conecte la imagen ISO a la máquina virtual.

#### <a name="create-new-vhd-in-hyper-v-manager"></a>Creación de un nuevo VHD en el Administrador de Hyper-V

El primer paso consiste en crear un nuevo VHD de Generación 1 en el Administrador de Hyper-V, que será el VHD de origen para una nueva máquina virtual.

Para crear el VHD, siga estos pasos:

1. Abra el administrador de Hyper-V en el sistema cliente. En el menú **Acción**, seleccione **Nuevo** y, a continuación, **Disco duro**.

   ![Selección de Nuevo y Disco duro](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

1. En **Elegir formato de disco**, seleccione **VHD**. A continuación, seleccione **Siguiente >** . 

   ![Elección de VHD como formato de disco](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

2. En **Elegir tipo de disco**, seleccione **Tamaño fijo**. A continuación, seleccione **Siguiente >** .

   ![Elección de tamaño fijo como tipo de disco](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-03.png)

3. En **Especificar nombre y ubicación**, escriba un nombre y una ubicación para el nuevo disco duro virtual. A continuación, seleccione **Siguiente >** .

   ![Escribir el nombre y la ubicación del VHD](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-04.png)

4. En **Configurar disco**, seleccione **Crear un nuevo disco duro virtual en blanco** y escriba el tamaño del disco que desea crear (normalmente, a partir de 20 GB para Windows Server). A continuación, seleccione **Siguiente >** .

   ![Configuración para crear un nuevo disco duro virtual en blanco y especificar el tamaño](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-05.png)

5. En **Resumen**, revise las selecciones y seleccione **Finalizar** para crear el nuevo disco duro virtual. El proceso tardará cinco minutos o más, en función del tamaño del disco duro virtual creado.

   ![Resumen de la configuración del VHD](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-06.png)

#### <a name="create-hyper-v-vm-from-vhd"></a>Creación de una máquina virtual de Hyper-V a partir del VHD

Ahora, usará el disco duro virtual que acaba de crear para crear una nueva máquina virtual.

Para crear la nueva máquina virtual, siga estos pasos:

1. Abra el Administrador de Hyper-V en el cliente Windows.

2. En el panel **Acciones**, seleccione **Nuevo** y, a continuación, **Máquina virtual**.

   ![Selección de Nuevo y Máquina virtual en el menú de la derecha.](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-07.png)

3. En el asistente para nueva máquina virtual, especifique el nombre y la ubicación de la máquina virtual.

   ![Especificar nombre y ubicación en el asistente para nueva máquina virtual](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-08.png)

4. En **Especificar generación**, seleccione **Generación 1**. A continuación, seleccione **Siguiente >** .

   ![Elegir la generación de la máquina virtual que se va a crear en el asistente para nueva máquina virtual](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-09.png)

5. En **Asignar memoria**, asigne la memoria deseada a la máquina virtual. A continuación, seleccione **Siguiente >** .

   ![Asignar memoria en el asistente para nueva máquina virtual](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-10.png)

6. En **Configurar redes**, escriba la configuración de red. A continuación, seleccione **Siguiente >** .

   ![Configurar redes en el asistente para nueva máquina virtual](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-11.png)

7. En **Conectar disco duro virtual**, seleccione **Usar un disco duro virtual existente** y busque el VHD de tamaño fijo que creó en el procedimiento anterior. A continuación, seleccione **Siguiente >** . 

   ![Seleccionar un disco duro virtual existente como origen en el asistente para nueva máquina virtual](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-12.png)

8. Revise el resumen y seleccione **Finalizar** para crear la máquina virtual. 

#### <a name="mount-iso-image-on-dvd-drive-of-vm"></a>Montaje de una imagen ISO en la unidad de DVD de la máquina virtual

Después de crear la nueva máquina virtual, siga estos pasos para montar la imagen ISO en la unidad de DVD de la máquina virtual:

1. En el Administrador de Hyper-V, seleccione la máquina virtual que acaba de crear y, a continuación, seleccione **Configuración**.
 
   ![Abrir la opción Configuración de la máquina virtual en el Administrador de Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-13.png)

2. En **BIOS**, asegúrese de que **CD** esté en la parte superior de la lista **Orden de inicio**.

   ![En la configuración del BIOS, el primer elemento en Orden de inicio debe ser CD.](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-14.png)


3. En **Unidad de DVD**, seleccione **Archivo de imagen** y vaya a la imagen ISO.  

   ![En la configuración de la unidad de DVD, seleccione el archivo de imagen del disco duro virtual.](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-15.png)

4. Seleccione **Aceptar** para guardar la configuración de la máquina virtual.

## <a name="start-vm-and-complete-os-installation"></a>Inicio de la máquina virtual y finalización de la instalación del sistema operativo

Para terminar de crear la máquina virtual, debe iniciar la máquina virtual y completar la instalación del sistema operativo.

[!INCLUDE [Connect to Hyper-V VM](../../includes/azure-stack-edge-connect-to-hyperv-vm.md)]

> [!NOTE]
> Si instaló el sistema operativo Windows Server 2019 Standard en la máquina virtual, deberá cambiar la configuración del **BIOS** a **IDE** antes de [generalizar el VHD](#generalize-the-vhd). 

## <a name="generalize-the-vhd"></a>Generalización del disco duro virtual

Use la utilidad *sysprep* para generalizar el VHD.

1. Si va a generalizar una máquina virtual Windows Server 2019 Standard, antes de generalizar el VHD, coloque IDE como primera configuración del **BIOS** para la máquina virtual.

    1. En el Administrador de Hyper-V, seleccione la máquina virtual y, a continuación, seleccione **Configuración**.
 
       ![Captura de pantalla que muestra cómo abrir la opción Configuración de una máquina virtual seleccionada en el Administrador de Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-01.png)

     1. En **BIOS**, asegúrese de que **IDE** esté en la parte superior de la lista **Orden de inicio**. A continuación, seleccione **Aceptar** para guardar la configuración.

        ![Captura de pantalla que muestra la opción IDE en la parte superior del orden de inicio de la configuración del BIOS de una máquina virtual en el Administrador de Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-generalized-image-iso/vhd-from-iso-02.png)

1. Dentro de la máquina virtual, abra un símbolo del sistema.

1. Ejecute el siguiente comando para generalizar el VHD. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    
    Para más información, consulte [Introducción a Sysprep (preparación del sistema)](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

1.  Una vez completado el comando, la máquina virtual se apagará. **No reinicie la VM**.

<!--[!INCLUDE [Generalize the VHD](../../includes/azure-stack-edge-generalize-vhd.md)]-->

El VHD se puede usar ahora para crear una imagen generalizada para usarla en Azure Stack Edge Pro con GPU.

## <a name="upload-generalized-vhd-to-azure-blob-storage"></a>Carga del VHD generalizado en Azure Blob Storage

[!INCLUDE [Upload VHD to Blob storage](../../includes/azure-stack-edge-upload-vhd-to-blob-storage.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de máquinas virtuales a partir de una imagen generalizada mediante Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Preparación de una imagen generalizada desde un VHD de Windows para implementar máquinas virtuales en Azure Stack Edge Pro GPU](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Implementación de una máquina virtual a partir de una imagen especializada en el dispositivo Azure Stack Edge Pro GPU a través de Azure PowerShell](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md) 
