---
title: Exportación de una versión de imagen a un disco administrado
description: Exportar una versión de imagen desde una galería de imágenes compartidas a un disco administrado.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: cynthn
ms.reviewer: olayemio
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fd6a1cf2cae56bf2d10fe4075caed019e2e72b7e
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452256"
---
# <a name="export-an-image-version-to-a-managed-disk"></a>Exportación de una versión de imagen a un disco administrado

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Puede exportar el sistema operativo o un disco de datos individual desde una versión de la imagen como un disco administrado o desde una versión de la imagen almacenada en Shared Image Gallery.


## <a name="cli"></a>CLI

Enumere las versiones de imágenes de una galería mediante [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list). En este ejemplo, vamos a buscar todas las versiones de una imagen que forman parte de la definición de la imagen *myImageDefinition* en la galería de imágenes *myGallery*.

```azurecli-interactive
az sig image-version list \
   --resource-group myResourceGroup\
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

Establezca la variable `source` como identificador de la versión de la imagen y, luego, use [az disk create](/cli/azure/disk#az_disk_create) para crear el disco administrado. 

En este ejemplo, se exporta el disco del sistema operativo de la versión de la imagen para crear un disco administrado denominado *myManagedOSDisk* en la región *EastUS*, en un grupo de recursos denominado *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedOSDisk --gallery-image-reference $source 
```



Si desea exportar un disco de datos desde la versión de la imagen, agregue `--gallery-image-reference-lun` para especificar la ubicación del número de unidad lógica del disco de datos que se va a exportar. 

En este ejemplo, se exporta el disco de datos ubicado en el LUN 0 de la versión de la imagen para crear un disco administrado denominado *myManagedDataDisk* en la región *EastUS*, en un grupo de recursos denominado *myResourceGroup*. 

```azurecli-interactive
source="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<galleryImageDefinition>/versions/<imageVersion>"

az disk create --resource-group myResourceGroup --location EastUS --name myManagedDataDisk --gallery-image-reference $source --gallery-image-reference-lun 0
``` 

## <a name="powershell"></a>PowerShell

Enumere las versiones de imágenes de una galería mediante [Get-AzResource](/powershell/module/az.resources/get-azresource). 

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceId,ResourceGroupName
```

Una vez que tenga toda la información que necesita, puede usar [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion) para obtener la versión de la imagen de origen que quiera usar y asignarla a una variable. En este ejemplo, vamos a obtener la versión de imagen `1.0.0` de la definición `myImageDefinition`, en la galería de origen `myGallery`, en el grupo de recursos `myResourceGroup`.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```

Después de establecer la variable `source` en el identificador de la versión de la imagen, use [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) para crear una configuración de disco y [New-AzDisk](/powershell/module/az.compute/new-azdisk) para crear el disco. 

En este ejemplo, se exporta el disco del sistema operativo de la versión de la imagen para crear un disco administrado denominado *myManagedOSDisk* en la región *EastUS*, en un grupo de recursos denominado *myResourceGroup*. 

Cree una configuración del disco.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id}
```

Cree el disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedOSDisk
```

Si desea exportar un disco de datos en la versión de la imagen, agregue un identificador de LUN a la configuración del disco para especificar la ubicación del número de unidad lógica del disco de datos que se va a exportar. 

En este ejemplo, se exporta el disco de datos ubicado en el LUN 0 de la versión de la imagen para crear un disco administrado denominado *myManagedDataDisk* en la región *EastUS*, en un grupo de recursos denominado *myResourceGroup*. 

Cree una configuración del disco.
```azurepowershell-interactive
$diskConfig = New-AzDiskConfig `
   -Location EastUS `
   -CreateOption FromImage `
   -GalleryImageReference @{Id = $sourceImgVer.Id; Lun=0}
```

Cree el disco.

```azurepowershell-interactive
New-AzDisk -Disk $diskConfig `
   -ResourceGroupName myResourceGroup `
   -DiskName myManagedDataDisk
```

## <a name="next-steps"></a>Pasos siguientes

También puede crear una [versión de la imagen](image-version.md) a partir de un disco administrado.


