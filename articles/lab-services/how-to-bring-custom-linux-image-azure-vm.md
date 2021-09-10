---
title: 'Azure Lab Services: cómo traer una imagen personalizada de Linux desde una máquina virtual de Azure.'
description: Describe cómo traer una imagen personalizada de Linux desde una máquina virtual de Azure.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: edb1620b0051e82df3994982eb069e78e22777bf
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450942"
---
# <a name="bring-a-linux-custom-image-from-an-azure-virtual-machine"></a>Transporte de una imagen personalizada de Linux desde una máquina virtual de Azure

En los pasos de este artículo se muestra cómo importar una imagen personalizada que se inicia desde una [máquina virtual (VM) de Azure](https://azure.microsoft.com/services/virtual-machines/). Con este enfoque se configura una imagen en una máquina virtual de Azure y se importa a una galería de imágenes compartidas para que se pueda usar en Azure Lab Services. Antes de usar este enfoque para crear una imagen personalizada, lea [Enfoques recomendados para crear imágenes personalizadas](approaches-for-custom-image-creation.md) para decidir cuál es el mejor según su escenario.

## <a name="prerequisites"></a>Prerrequisitos

Para completar los pasos de este artículo, necesitará permiso para crear una máquina virtual de Azure en la suscripción de Azure del centro educativo.

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>Preparación de una imagen personalizada en una máquina virtual de Azure

1. Cree una máquina virtual de Azure mediante [Azure Portal](../virtual-machines/windows/quick-create-portal.md), [PowerShell](../virtual-machines/windows/quick-create-powershell.md), la [CLI de Azure](../virtual-machines/windows/quick-create-cli.md) o una [plantilla de Azure Resource Manager](../virtual-machines/windows/quick-create-template.md).
    
    - Al especificar la configuración del disco, asegúrese de que el tamaño del disco *no* sea superior a 128 GB.
    
1. Instale software y realice los cambios de configuración necesarios en la imagen de la máquina virtual de Azure.

1. Opcionalmente, puede generalizar la imagen. Si decide crear una imagen generalizada, siga los pasos descritos en [Paso 1: Desaprovisionar la máquina virtual](../virtual-machines/linux/capture-image.md#step-1-deprovision-the-vm). Cuando se usa el comando **-deprovision+user**, la imagen se generaliza, pero no hay garantía de que se haya borrado toda información confidencial de la imagen ni que sea adecuada para su redistribución.

    De lo contrario, si decide crear una imagen especializada, puede ir directamente al paso siguiente.

    Cree una imagen especializada si quiere conservar la información específica del equipo y los perfiles de usuario. Para obtener más información sobre las diferencias entre las imágenes generalizadas y especializadas, vea [Imágenes generalizadas y especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>Importación de la imagen personalizada en una galería de imágenes compartidas

1. En una galería de imágenes compartidas, [cree una definición de imagen](../virtual-machines/image-version.md) o elija una definición de imagen existente.
     - Elija **Gen 1** para la **generación de máquinas virtuales**.
     - Elija si va a crear una imagen **especializada** o **generalizada** para el **estado del sistema operativo**.

    Para más información sobre los valores que se pueden especificar para una definición de imagen, consulte [Definiciones de imagen](../virtual-machines/shared-image-galleries.md#image-definitions). 
    
    También puede elegir usar una definición de imagen existente y crear una nueva versión para la imagen personalizada.
    
1. [Cree la versión de una imagen](../virtual-machines/image-version.md).
    - La propiedad **Número de versión** usa el formato siguiente: *MajorVersion.MinorVersion.Patch*. 
    - En **Origen**, seleccione **Discos y/o instantáneas** en la lista desplegable.
    - En la propiedad **Disco de SO**, elija el disco de la máquina virtual de Azure que creó en los pasos anteriores.

Los pasos anteriores también se pueden automatizar con PowerShell. Para obtener más información, vea el script y el archivo Léame en [Incluir imagen en un script de galería de imágenes compartidas](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/).

## <a name="create-a-lab"></a>Creación de un laboratorio

[Cree el laboratorio](tutorial-setup-classroom-lab.md) en Lab Services y seleccione la imagen personalizada en la galería de imágenes compartidas.

## <a name="next-steps"></a>Pasos siguientes

* [Información general de Shared Image Galleries](../virtual-machines/shared-image-galleries.md)
* [Asociación o desasociación de una galería de imágenes compartidas](how-to-attach-detach-shared-image-gallery.md)
* [Uso de una galería de imágenes compartidas](how-to-use-shared-image-gallery.md)