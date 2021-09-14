---
title: Enumeración, actualización y eliminación de recursos de imagen
description: Enumere, actualice y elimine recursos de imagen en la galería de imágenes compartidas.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: af41cd667da05abcd05d4c04d913e82567eec5ca
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452339"
---
# <a name="list-update-and-delete-image-resources"></a>Enumeración, actualización y eliminación de recursos de imagen 

Puede administrar los recursos de la galería de imágenes compartidas mediante la CLI de Azure o Azure PowerShell.

## <a name="list-information"></a>Visualización de la información

### <a name="cli"></a>[CLI](#tab/cli)

Para obtener la ubicación, el estado y demás información sobre las galerías de imágenes disponibles, use [az sig list](/cli/azure/sig#az_sig_list).

```azurecli-interactive 
az sig list -o table
```

Enumere las definiciones de las imágenes en una galería, incluida la información sobre el tipo de sistema operativo y el estado, con [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Enumere las versiones de imágenes compartidas en una galería, con [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Obtenga el identificador de una versión de imagen con [az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Enumere todas las galerías por nombre.

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

Enumere todas las definiciones de una imagen por nombre.

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

Enumere todas las versiones de una imagen por nombre.

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

Elimine la versión de una imagen. En este ejemplo se elimina la versión de la imagen denominada *1.0.0*.

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```


---


## <a name="update-resources"></a>Actualización de recursos

### <a name="cli"></a>[CLI](#tab/cli)
Existen algunas limitaciones en lo que se puede actualizar. Se pueden actualizar los siguientes elementos: 

Galería de imágenes compartidas:
- Descripción

Definición de la imagen:
- vCPU recomendadas:
- Memoria recomendada
- Descripción
- Fecha final del ciclo de vida

Versión de la imagen:
- Recuento de réplicas regionales
- Regiones de destino
- Exclusión de la versión más reciente
- Fecha final del ciclo de vida

Si planea agregar regiones de réplica, no elimine la imagen administrada de origen. La imagen administrada de origen es necesaria para replicar la versión de la imagen a regiones adicionales. 

Actualice la descripción de una galería con [az sig update](/cli/azure/sig#az_sig_update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Actualice la descripción de una definición de imagen con [az sig image-definition update](/cli/azure/sig/image-definition#az_sig_image_definition_update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Actualice una versión de imagen para agregar una región para replicar con [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update). Este cambio tardará un poco, a medida que la imagen se replica en la nueva región.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

En este ejemplo se muestra cómo usar [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) para impedir que esta versión de imagen se use como la imagen *más reciente*.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

En este ejemplo se muestra cómo usar [az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update) para que esta versión de imagen se considere como imagen *más reciente*.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)


Existen algunas limitaciones en lo que se puede actualizar. Se pueden actualizar los siguientes elementos: 

Galería de imágenes compartidas:
- Descripción

Definición de la imagen:
- vCPU recomendadas:
- Memoria recomendada
- Descripción
- Fecha final del ciclo de vida

Versión de la imagen:
- Recuento de réplicas regionales
- Regiones de destino
- Exclusión de la versión más reciente
- Fecha final del ciclo de vida

Si planea agregar regiones de réplica, no elimine la imagen administrada de origen. La imagen administrada de origen es necesaria para replicar la versión de la imagen a regiones adicionales. 

Para actualizar la descripción de una galería, use [Update-AzGallery](/powershell/module/az.compute/update-azgallery).

```azurepowershell-interactive
Update-AzGallery `
   -Name $gallery.Name ` 
   -ResourceGroupName $resourceGroup.Name
```

En este ejemplo se muestra cómo usar [Update-AzGalleryImageDefinition](/powershell/module/az.compute/update-azgalleryimagedefinition) para actualizar la fecha de vencimiento de nuestra definición de imagen.

```azurepowershell-interactive
Update-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -Name $galleryImage.Name `
   -ResourceGroupName $resourceGroup.Name `
   -EndOfLifeDate 01/01/2030
```

En este ejemplo se muestra cómo usar [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) para impedir que esta versión de imagen se use como la imagen *más reciente*.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest
```

En este ejemplo se muestra cómo usar [Update-AzGalleryImageVersion](/powershell/module/az.compute/update-azgalleryimageversion) para que esta versión de imagen se considere como imagen *más reciente*.

```azurepowershell-interactive
Update-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryName $gallery.Name `
   -Name $galleryVersion.Name `
   -ResourceGroupName $resourceGroup.Name `
   -PublishingProfileExcludeFromLatest:$false
```

---




## <a name="delete-resources"></a>Eliminar recursos

Debe eliminar los recursos en orden inverso, eliminando primero la versión de imagen. Después de eliminar todas las versiones de imagen, puede eliminar la definición de imagen. Después de eliminar todas las definiciones de imagen, puede eliminar la galería. 

### <a name="cli"></a>[CLI](#tab/cli)

Elimine una versión de imagen con [az sig image-version delete](/cli/azure/sig/image-version#az_sig_image_version_delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Elimine una definición de imagen con [az sig image-definition delete](/cli/azure/sig/image-definition#az_sig_image_definition_delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Elimine una galería de imágenes mediante [az sig delete](/cli/azure/sig#az_sig_delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)



```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$gallery = "myGallery"
$imageDefinition = "myImageDefinition"
$imageVersion = "myImageVersion"

Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition `
   -GalleryName $gallery `
   -Name $imageVersion `
   -ResourceGroupName $resourceGroup

Remove-AzGalleryImageDefinition `
   -ResourceGroupName $resourceGroup `
   -GalleryName $gallery `
   -GalleryImageDefinitionName $imageDefinition

Remove-AzGallery `
   -Name $gallery `
   -ResourceGroupName $resourceGroup

Remove-AzResourceGroup -Name $resourceGroup
```

---

## <a name="next-steps"></a>Pasos siguientes

[Azure Image Builder (versión preliminar)](./image-builder-overview.md) puede ayudar a automatizar la creación de versiones de la imagen, incluso se puede usar para actualizar y [crear una nueva versión de la imagen a partir de una versión de imagen existente](./linux/image-builder-gallery-update-image-version.md).