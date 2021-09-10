---
title: 'Azure Lab Services: Cómo incorporar una imagen personalizada de Windows desde una máquina virtual de Azure'
description: Describe cómo incorporar una imagen personalizada de Windows desde una máquina virtual de Azure.
ms.date: 07/27/2021
ms.topic: how-to
ms.openlocfilehash: 465d3c128e68e1c8a0d72f51bcf5f354da9d6818
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831382"
---
# <a name="bring-a-windows-custom-image-from-an-azure-virtual-machine"></a>Transporte de una imagen personalizada de Windows desde una máquina virtual de Azure

En los pasos de este artículo se muestra cómo importar una imagen personalizada que se inicia desde una [máquina virtual (VM) de Azure](https://azure.microsoft.com/services/virtual-machines/). Con este enfoque, configura una imagen en una máquina virtual de Azure y la importa en una galería de imágenes compartidas para que se pueda usar en Azure Lab Services. Antes de usar este enfoque para crear una imagen personalizada, lea [Enfoques recomendados para crear imágenes personalizadas](approaches-for-custom-image-creation.md) para decidir el mejor enfoque para el escenario.

## <a name="prerequisites"></a>Requisitos previos

Necesitará permiso para crear una máquina virtual de Azure en la suscripción de Azure del centro educativo para completar los pasos de este artículo.

## <a name="prepare-a-custom-image-on-an-azure-vm"></a>Preparación de una imagen en una máquina virtual de Azure

1. Cree una máquina virtual de Azure mediante [Azure Portal](../virtual-machines/windows/quick-create-portal.md), [PowerShell](../virtual-machines/windows/quick-create-powershell.md), la [CLI de Azure](../virtual-machines/windows/quick-create-cli.md) o una [plantilla de Azure Resource Manager](../virtual-machines/windows/quick-create-template.md).
    
    - Al especificar la configuración del disco, asegúrese de que el tamaño del disco *no* sea superior a 128 GB.
    
1. Instale software y realice los cambios de configuración necesarios en la imagen de la máquina virtual de Azure.

1. Opcionalmente, puede generalizar la imagen. Ejecute [SysPrep](../virtual-machines/generalize.md#windows) si necesita crear una imagen generalizada. De lo contrario, si va a crear una imagen especializada, puede ir directamente al paso siguiente.

    Cree una imagen especializada si quiere conservar la información específica de la máquina y los perfiles de usuario. Para obtener más información sobre las diferencias entre las imágenes generalizadas y especializadas, vea [Imágenes generalizadas y especializadas](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images).

## <a name="import-the-custom-image-into-a-shared-image-gallery"></a>Importación de la imagen personalizada en una galería de imágenes compartidas

1. En una galería de imágenes compartidas, [cree una definición de imagen](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition) o elija una definición de imagen existente.
     - Elija **Gen 1** para la **generación de máquinas virtuales**.
     - Elija si va a crear una imagen **especializada** o **generalizada** para el **estado del sistema operativo**.

    Para más información sobre los valores que se pueden especificar para una definición de imagen, consulte [Definiciones de imagen](../virtual-machines/shared-image-galleries.md#image-definitions). 
    
    También puede elegir usar una definición de imagen existente y crear una nueva versión para la imagen personalizada.
    
1. [Cree la versión de una imagen](../virtual-machines/windows/shared-images-portal.md#create-an-image-version).
    - La propiedad **Número de versión** usa el formato siguiente: *MajorVersion.MinorVersion.Patch*.   
    - En **Origen**, seleccione **Discos y/o instantáneas** en la lista desplegable.
    - En la propiedad **Disco de SO**, elija el disco de la máquina virtual de Azure que creó en los pasos anteriores.

    También puede importar la imagen personalizada desde una máquina virtual de Azure a la galería de imágenes compartidas mediante PowerShell. Para obtener más información, vea el script y el archivo Léame en [Incluir imagen en el script de galería de imágenes compartidas](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/BringImageToSharedImageGallery/).

## <a name="create-a-lab"></a>Creación de un laboratorio

[Cree el laboratorio](tutorial-setup-classroom-lab.md) en Lab Services y seleccione la imagen personalizada en la galería de imágenes compartidas.

## <a name="next-steps"></a>Pasos siguientes

* [Información general de Shared Image Galleries](../virtual-machines/shared-image-galleries.md)
* [Asociación o desasociación de una galería de imágenes compartidas](how-to-attach-detach-shared-image-gallery.md)
* [Uso de una galería de imágenes compartidas](how-to-use-shared-image-gallery.md)