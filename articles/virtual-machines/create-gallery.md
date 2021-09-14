---
title: Creación de una instancia de Shared Image Gallery
description: Información sobre cómo crear una instancia de Shared Image Gallery en Azure
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 07/15/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: c7462886c2f85e07f060caa7a11a39a782e38cea
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452341"
---
# <a name="create-a-gallery-for-storing-and-sharing-images"></a>Creación de una galería para almacenar y compartir imágenes


Una [galería de imágenes compartidas](./shared-image-galleries.md) simplifica el uso compartido de imágenes personalizadas en toda una organización. Las imágenes personalizadas son como las imágenes de Marketplace, pero las puede crear usted mismo. Las imágenes personalizadas se pueden usar para realizar tareas de implementación de arranque, como la carga previa de aplicaciones, configuraciones de aplicaciones y otras configuraciones del sistema operativo. 

La Galería de imágenes compartidas le permite compartir sus imágenes de máquina virtual personalizadas con otros usuarios de su organización, ya sea dentro o entre regiones, dentro de un inquilino de AAD. Elija las imágenes que desea compartir, qué regiones desea que estén disponibles en ellas y con quién desea compartirlas. Puede crear varias galerías que le permitirán agrupar lógicamente las imágenes compartidas. 

La galería es un recurso de nivel superior que proporciona control de acceso basado en roles de Azure (RBAC de Azure). Las imágenes pueden tener varias versiones y se puede optar por replicar cada versión de la imagen en un conjunto diferente de regiones de Azure. La galería solo funciona con imágenes administradas.

La característica de galería de imágenes compartidas tiene varios tipos de recursos. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](./includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="create-a-gallery"></a>Creación de una galería

Una galería de imágenes es el recurso principal que se usa para habilitar el uso compartido de imágenes. Los caracteres permitidos para el nombre de la galería son letras mayúsculas o minúsculas, números y puntos. El nombre de la galería no puede contener guiones. Los nombres de las galerías deben ser únicos dentro de su suscripción. 

Elija una de las opciones siguientes para crear la galería:

### <a name="portal"></a>[Portal](#tab/portal)

En el ejemplo siguiente se crea una galería denominada *myGallery* en el grupo de recursos *myGalleryRG*.

1. Inicie sesión en Azure Portal en https://portal.azure.com.
1. Use el tipo **Galería de imágenes compartidas** en el cuadro de búsqueda y seleccione **Galería de imágenes compartidas** en los resultados.
1. En la página **Galería de imágenes compartidas**, haga clic en **Agregar**.
1. En la página **Create shared image gallery** (Crear galería de imágenes compartidas), seleccione la suscripción correcta.
1. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba *myGalleryRG* como nombre.
1. En **Nombre**, escriba *myGallery* como nombre de la galería.
1. En **Región**, deje el valor predeterminado.
1. Puede escribir una descripción corta de la galería, como *Mi galería de imágenes para prueba.* Después, haga clic en **Revisar y crear**.
1. Una vez pasada la validación, seleccione **Crear**.
1. Cuando la implementación finalice, seleccione **Ir al recurso**.

### <a name="cli"></a>[CLI](#tab/cli)

Cree una galería de imágenes mediante [az sig az create](/cli/azure/sig#az_sig_create). En el ejemplo siguiente se crea un grupo de recursos denominado *myGalleryRG* en la región *Este de EE. UU.* y una galería denominada *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Cree una galería de imágenes con [New-AzGallery](/powershell/module/az.compute/new-azgallery). En el ejemplo siguiente se crea una galería denominada *myGallery* en el grupo de recursos *myGalleryRG*.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


### <a name="rest"></a>[REST](#tab/rest)

Use la [API REST](/rest/api/resources/resource-groups/create-or-update) para crear un grupo de recursos.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}?api-version=2021-04-01

{
  "location": "eastus"
}
```

Use la [API REST](/rest/api/compute/galleries/create-or-update) para crear una galería.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}?api-version=2019-12-01

{
  "properties": {
    "description": "Shared Image Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>Pasos siguientes

Creación de una [definición de imagen y una versión de imagen](image-version.md).

[Azure Image Builder (versión preliminar)](./image-builder-overview.md) puede ayudar a automatizar la creación de versiones de la imagen, incluso se puede usar para actualizar y [crear una nueva versión de la imagen a partir de una versión de imagen existente](./windows/image-builder-gallery-update-image-version.md). 

Puede crear también recursos de galería de imágenes compartidas con plantillas. Hay varias plantillas de Inicio rápido de Azure disponibles: 

- [Creación de una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-create/)
- [Creación de una definición de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Creación de una versión de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-image-version-create/)