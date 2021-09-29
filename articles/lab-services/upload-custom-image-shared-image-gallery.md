---
title: 'Azure Lab Services: Cómo incorporar una imagen personalizada de Windows desde el entorno de laboratorio físico'
description: Describe cómo incorporar una imagen personalizada de Windows desde el entorno de laboratorio físico.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 50d224f82943a4157c9c6a1eda77af0b151fafa8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736687"
---
# <a name="bring-a-windows-custom-image-from-a-physical-lab-environment"></a>Transporte de una imagen personalizada desde un entorno de laboratorio físico

En los pasos de este artículo se muestra cómo importar una imagen personalizada que se inicia desde su entorno de laboratorio físico.  Con este enfoque, creará un VHD desde su entorno físico e importará el VHD en la galería de imágenes compartidas para que se pueda usar en Lab Services.  Antes de usar este enfoque para crear una imagen personalizada, lea el artículo [Enfoques recomendados para crear imágenes personalizadas](approaches-for-custom-image-creation.md) para decidir el mejor enfoque para su escenario.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará permiso para crear un [disco administrado de Azure](../virtual-machines/managed-disks-overview.md) en la suscripción de Azure de la institución educativa.

Al mover imágenes de un entorno de laboratorio físico a Lab Services, debe reestructurar cada una de ellas para que solo incluya el software necesario para la clase de un laboratorio.  Para obtener más información, lea la entrada de blog [Traslado de un laboratorio físico a Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

## <a name="prepare-a-custom-image-using-hyper-v-manager"></a>Preparación de una imagen personalizada mediante el Administrador de Hyper-V

En los pasos siguientes se muestra cómo crear una imagen de Windows desde una máquina virtual (VM) Hyper-V de Windows mediante el Administrador de Hyper-V:

1. Comience con una máquina virtual de Hyper-V en el entorno de laboratorio físico que se ha creado a partir de la imagen.  Lea el artículo sobre [cómo crear una máquina virtual en Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obtener más información.
    - La máquina virtual debe crearse como una máquina virtual de **generación 1**.
    - Use la configuración de red **Conmutador predeterminado** para permitir que la máquina virtual se conecte a Internet.
    - El disco virtual de la máquina virtual debe ser un VHD de tamaño fijo.  El tamaño del disco *no* debe ser mayor que 128 GB. Al crear la máquina virtual, escriba el tamaño del disco como se muestra en la imagen siguiente.

        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Conectar disco duro virtual":::

    Lab Services *no* admite imágenes con un tamaño de disco superior a 128 GB.

1. Conéctese a la máquina virtual de Hyper-V y [prepárese para Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) siguiendo estos pasos:
    1. [Establecimiento de configuraciones de Windows para Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure).
    1. [Comprobación de los servicios de Windows necesarios para garantizar la conectividad de la máquina virtual](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services).
    1. [Actualización de la configuración del Registro de Escritorio remoto](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings).
    1. [Configuración de las reglas de Firewall de Windows](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules).
    1. [Instalación de actualizaciones de Windows](../virtual-machines/windows/prepare-for-upload-vhd-image.md).
    1. [Instalación del agente de máquinas virtuales de Azure y configuración adicional como se muestra aquí](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations)

    Puede cargar imágenes especializadas o generalizadas en la galería de imágenes compartidas y usarlas para crear laboratorios.  Con los pasos anteriores se creará una imagen especializada. Si en su lugar necesita crear una imagen generalizada, también deberá [ejecutar SysPrep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep).  

    Debe crear una imagen especializada si desea mantener la información específica de la máquina y los perfiles de usuario.  Para obtener más información sobre las diferencias entre las imágenes generalizadas y especializadas, vea [Imágenes generalizadas y especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

1. Como **Hyper-V** crea un archivo **VHDX** de forma predeterminada, debe convertirlo en un archivo VHD.
    1. Vaya a **Administrador de Hyper-V** -> **Acción** -> **Editar disco**.
    1. A continuación, **convierta** el disco de un VHDX a un VHD.  
     - Si expande el tamaño del disco, asegúrese de que *no* supere los 128 GB.
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Elegir acción":::

    Para obtener más información, lea el artículo que muestra cómo [convertir el disco virtual en un VHD de tamaño fijo](../virtual-machines/windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd).

Para redimensionar el VHD y convertirlo en un VHDX, también puede usar los siguientes cmdlets de PowerShell:

- [Resize-VHD](/powershell/module/hyper-v/resize-vhd)
- [Convert-VHD](/powershell/module/hyper-v/convert-vhd)

## <a name="upload-the-custom-image-to-a-shared-image-gallery"></a>Carga de la imagen personalizada en una galería de imágenes compartidas

1. Cargue el VHD en Azure para crear un disco administrado.
    1. Puede usar el Explorador de Storage o AzCopy desde la línea de comandos, como se muestra en [Carga de un VHD en Azure o copia de un disco administrado en otra región](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md).

    1. Cuando haya cargado el VHD, debería tener un disco administrado que pueda ver en Azure Portal.
    Si el equipo entra en suspensión o se bloquea, es posible que el proceso de carga se interrumpa y se produzca un error.  Además, una vez se complete AzCopy, asegúrese de revocar el acceso SAS al disco.  De lo contrario, al intentar crear una imagen desde el disco, verá un error: **Operation 'Create Image' is not supported with disk 'your disk name' in state 'Active Upload'.  Error Code: OperationNotAllowed** (La operación "Crear imagen" no se admite en el disco "nombre del disco" en estado "Active Upload". Código de error: OperationNotAllowed)

    Es posible cambiar el tamaño del disco mediante la pestaña **Tamaño y rendimiento** del disco administrado en Azure Portal. Como se mencionó anteriormente, el tamaño *no* debe ser superior a 128 GB.

1. En una galería de imágenes compartidas, cree una definición y una versión de una imagen:
    1. [Cree la definición de una imagen](../virtual-machines/image-version.md).  
     - Elija **Gen 1** para la **generación de máquinas virtuales**.
     - Elija si va a crear una imagen **especializada** o **generalizada** para el **estado del sistema operativo**.

    Para más información sobre los valores que se pueden especificar para una definición de imagen, consulte [Definiciones de imagen](../virtual-machines/shared-image-galleries.md#image-definitions).

    También puede elegir usar una definición de imagen existente y crear una nueva versión para la imagen personalizada.

1. [Cree la versión de una imagen](../virtual-machines/image-version.md).
    - La propiedad **Número de versión** usa el formato siguiente: *MajorVersion.MinorVersion.Patch*.   Cuando se usa Lab Services para crear un laboratorio y elegir una imagen personalizada, se usa automáticamente la versión más reciente de la imagen.  La versión más reciente se elige en función del valor más alto de MajorVersion, luego MinorVersion y, por último, Patch.
    - En **Origen,** elija **Discos y/o instantáneas** en la lista desplegable.
    - Para la propiedad **Disco de SO**, elija el disco que ha creado en los pasos anteriores.

    Para más información sobre los valores que se pueden especificar para una definición de imagen, consulte [Versiones de imagen](../virtual-machines/shared-image-galleries.md#image-versions).

## <a name="create-a-lab"></a>Creación de un laboratorio

1. [Cree el laboratorio](tutorial-setup-classroom-lab.md) en Lab Services y seleccione la imagen personalizada en la galería de imágenes compartidas.

    Si ha expandido el disco *después* de instalar el sistema operativo en la máquina virtual de Hyper-V original, también deberá ampliar la unidad C en Windows para usar el espacio en disco sin asignar:
    - Inicie sesión en la máquina virtual de la plantilla del laboratorio y siga pasos similares a los que se indican en [Extensión de un volumen básico](/windows-server/storage/disk-management/extend-a-basic-volume).

## <a name="next-steps"></a>Pasos siguientes

- [Información general de Shared Image Galleries](../virtual-machines/shared-image-galleries.md)
- [Asociación o desasociación de una galería de imágenes compartidas](how-to-attach-detach-shared-image-gallery.md)
- [Uso de una galería de imágenes compartidas](how-to-use-shared-image-gallery.md)
