---
title: 'Azure Lab Services: Cómo transportar una imagen personalizada de Linux desde el entorno de laboratorio físico'
description: Describe cómo transportar una imagen personalizada de Linux desde el entorno de laboratorio físico.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: afaaf1d28043a7b88e627f730a619daf46c59e0d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451698"
---
# <a name="bring-a-linux-custom-image-from-your-physical-lab-environment"></a>Transporte de una imagen personalizada de Linux desde un entorno de laboratorio físico

En los pasos de este artículo se muestra cómo importar una imagen personalizada de Linux que se inicia desde su entorno de laboratorio físico. Con este enfoque, creará un VHD desde su entorno físico e importará el VHD en la galería de imágenes compartidas para que se pueda usar en Azure Lab Services. Antes de usar este enfoque para crear una imagen personalizada, consulte [Enfoques recomendados para crear imágenes personalizadas](approaches-for-custom-image-creation.md) para decidir el mejor enfoque para su escenario.

Azure aprueba una variedad de [distribuciones y versiones](../virtual-machines/linux/endorsed-distros.md#supported-distributions-and-versions). Los pasos para transportar una imagen personalizada de Linux desde un VHD varían para cada distribución. Cada distribución es diferente porque cada una tiene requisitos previos únicos que se deben configurar para ejecutarse en Azure.

En este artículo, mostraremos los pasos para transportar una imagen personalizada de Ubuntu 16.04\18.04\20.04 desde un VHD. Para obtener información sobre cómo usar un VHD para crear imágenes personalizadas para otras distribuciones, consulte [Información para las distribuciones no aprobadas](../virtual-machines/linux/create-upload-generic.md).

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará permiso para crear un [disco administrado de Azure](../virtual-machines/managed-disks-overview.md) en la suscripción de Azure de la institución educativa.

Al mover imágenes de un entorno de laboratorio físico a Lab Services, reestructure cada una de ellas para que solo incluya el software necesario para la clase de un laboratorio. Para obtener más información, lea la entrada de blog [Traslado de un laboratorio físico a Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

## <a name="prepare-a-custom-image-by-using-hyper-v-manager"></a>Preparación de una imagen personalizada mediante el Administrador de Hyper-V

En los pasos siguientes, se muestra cómo crear una imagen de Ubuntu 16.04\18.04\20.04 desde una máquina virtual (VM) de Hyper-V mediante el Administrador de Hyper-V de Windows.

1. Descargue la imagen oficial de [Linux Ubuntu Server](https://ubuntu.com/server/docs) en la máquina host Windows que usará para configurar la imagen personalizada en una máquina virtual de Hyper-V.

   Se recomienda usar una imagen de Ubuntu que *no* tenga instalado el escritorio de la GUI de [GNOME](https://www.gnome.org/). Actualmente, GNOME tiene un conflicto con el Agente Linux de Azure, que es necesario para que la imagen funcione correctamente en Lab Services. Por ejemplo, utilice la imagen de Ubuntu Server e instale otro escritorio de la GUI, como XFCE o MATE.

   Ubuntu también publica [VHD de Azure precompilados para su descarga](https://cloud-images.ubuntu.com/). Estos discos duros virtuales están diseñados para crear imágenes personalizadas desde una máquina host Linux y un hipervisor, como KVM. Estos discos duros virtuales requieren que primero establezca la contraseña de usuario predeterminada, lo que solo se puede realizar mediante herramientas de Linux, como qemu, que no están disponibles para Windows. Como resultado, cuando crea una imagen personalizada mediante Hyper-V de Windows, no podrá conectarse a estos VHD para realizar personalizaciones de imagen. Para más información sobre los discos duros virtuales precompilados de Azure, lea la [documentación de Ubuntu](https://help.ubuntu.com/community/UEC/Images?_ga=2.114783623.1858181609.1624392241-1226151842.1623682781#QEMU_invocation).

1. Comience con una máquina virtual de Hyper-V en el entorno de laboratorio físico que se haya creado a partir de la imagen. Para más información, consulte el artículo [Creación de una máquina virtual en Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v). Establezca la configuración como se muestra aquí:
    - La máquina virtual debe crearse como una máquina virtual de **generación 1**.
    - Use la configuración de red **Conmutador predeterminado** para permitir que la máquina virtual se conecte a Internet.
    - En la configuración **Conectar disco duro virtual**, el **Tamaño** del disco *no* puede superar los 128 GB, tal como se muestra en la imagen siguiente.
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Captura de pantalla que muestra la pantalla Conectar disco duro virtual.":::

    - En la configuración **Opciones de instalación**, seleccione el archivo **.iso** que descargó anteriormente de Ubuntu.

    Lab Services *no* admite imágenes con un tamaño de disco superior a 128 GB.

1. Conéctese a la máquina virtual de Hyper-V y prepárela para Azure según los pasos descritos en [Pasos manuales para crear y cargar un VHD de Ubuntu](../virtual-machines/linux/create-upload-ubuntu.md#manual-steps).

    Los pasos para preparar una imagen de Linux para Azure varían en función de la distribución. Consulte [Distribuciones y versiones admitidas](../virtual-machines/linux/endorsed-distros.md#supported-distributions-and-versions) para más información y los pasos específicos de cada distribución.

    Al seguir los pasos anteriores, hay algunos puntos importantes que resaltar:
    - Los pasos crean una imagen [generalizada](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) cuando ejecuta el comando **deprovision+user**. Pero no hay garantía de que se haya borrado toda información confidencial de la imagen ni que sea adecuada para su redistribución.
    - El último paso es convertir el archivo **VHDX** en un archivo **VHD**. Estos son los pasos equivalentes que muestran cómo hacerlo con el **Administrador de Hyper-V**:
        
        1. Vaya a **Administrador de Hyper-V** > **Acción** > **Editar disco**.
        1. A continuación, **convierta** el disco de un VHDX a un VHD.
        1. En **Tipo de disco**, seleccione **Tamaño fijo**.
            - Si en este punto también opta por expandir el tamaño del disco, asegúrese de *no* superar los 128 GB.
            :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Captura de pantalla que muestra la pantalla Elegir acción.":::

Para redimensionar el VHD y convertirlo en un VHDX, también puede usar los siguientes cmdlets de PowerShell:

- [Resize-VHD](/powershell/module/hyper-v/resize-vhd?view=windowsserver2019-ps)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd?view=windowsserver2019-ps)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>Carga de la imagen personalizada en una galería de imágenes compartidas

1. Cargue el VHD en Azure para crear un disco administrado.
    1. Puede usar el Explorador de Storage o AzCopy desde la línea de comandos, como se muestra en [Carga de un VHD en Azure o copia de un disco administrado en otra región](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md).

    1. Cuando haya cargado el VHD, debería tener un disco administrado que pueda ver en Azure Portal. 
    
    Si el equipo entra en suspensión o se bloquea, es posible que el proceso de carga se interrumpa y se produzca un error. Además, una vez se complete AzCopy, asegúrese de revocar el acceso SAS al disco. De lo contrario, al intentar crear una imagen desde el disco, verá el error "La operación "Create Image" no se admite en el disco "nombre del disco" en estado "Carga activa". Código de error: OperationNotAllowed*."
    
    Utilice la pestaña **Tamaño y rendimiento** del disco administrado en Azure Portal para cambiar el tamaño del disco. Como se mencionó anteriormente, el tamaño *no* debe ser superior a 128 GB.

1. En una galería de imágenes compartidas, cree una definición y una versión de una imagen:
    1. [Cree la definición de una imagen](../virtual-machines/image-version.md):
        - Elija **Gen 1** para la **generación de máquinas virtuales**.
        - Elija **Linux** para el **Sistema operativo**.
        - Elija **Generalizado** para el **Estado del sistema operativo**.
     
    Para más información sobre los valores que se pueden especificar para una definición de imagen, consulte [Definiciones de imagen](../virtual-machines/shared-image-galleries.md#image-definitions). 
    
    También puede elegir usar una definición de imagen existente y crear una nueva versión para la imagen personalizada.
    
1. [Cree la versión de una imagen](../virtual-machines/image-version.md):
   - La propiedad **Número de versión** usa el formato siguiente: *MajorVersion.MinorVersion.Patch*. Cuando se usa Lab Services para crear un laboratorio y elegir una imagen personalizada, se usa automáticamente la versión más reciente de la imagen. La versión más reciente se elige en función del valor más alto de MajorVersion, luego MinorVersion y, por último, Patch.
    - En **Origen**, seleccione **Discos y/o instantáneas** en la lista desplegable.
    - Para la propiedad **Disco de SO**, elija el disco que ha creado en los pasos anteriores.
    
    Para más información sobre los valores que se pueden especificar para una definición de imagen, consulte [Versiones de imagen](../virtual-machines/shared-image-galleries.md#image-versions).

## <a name="create-a-lab"></a>Creación de un laboratorio
   
[Cree el laboratorio](tutorial-setup-classroom-lab.md) en Lab Services y seleccione la imagen personalizada en la galería de imágenes compartidas.

Si ha expandido el disco *después* de instalar el sistema operativo en la máquina virtual de Hyper-V original, también deberá ampliar la partición en el sistema de archivos de Linux para usar el espacio en disco sin asignar:
- Inicie sesión en la VM de plantilla del laboratorio y siga pasos similares a los que se muestran en [Expansión de una partición de disco y del sistema de archivos](../virtual-machines/linux/expand-disks.md#expand-a-disk-partition-and-filesystem).
    
Por lo general, el disco del sistema operativo se encuentra en la partición **/dev/sad2**. Para ver el tamaño actual de la partición del disco del SO, use el comando **df -h**.
    
## <a name="next-steps"></a>Pasos siguientes

* [Información general de Shared Image Galleries](../virtual-machines/shared-image-galleries.md)
* [Asociación o desasociación de una galería de imágenes compartidas](how-to-attach-detach-shared-image-gallery.md)
* [Uso de una galería de imágenes compartidas](how-to-use-shared-image-gallery.md)