---
title: Creación de una instancia de Azure Compute Gallery para compartir recursos
description: Aprenda a crear una instancia de Azure Compute Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/05/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 924cce1b23cf86a6e29ef4fd28f862bf2fa12363
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459698"
---
# <a name="create-a-gallery-for-storing-and-sharing-resources"></a>Creación de una galería para almacenar y compartir recursos


Una instancia de [Azure Compute Gallery](./shared-image-galleries.md) (anteriormente denominada Shared Image Gallery) simplifica el uso compartido de recursos, como imágenes y paquetes de aplicación, en toda la organización.  

Azure Compute Gallery permite compartir imágenes de máquina virtual personalizadas y paquetes de aplicación con otros usuarios de la organización, ya sea dentro o entre regiones, dentro de un inquilino de AAD. Elija las imágenes que quiera compartir, en qué regiones quiere que estén disponibles y con quién quiere compartirlas. Puede crear varias galerías que le permitirán agrupar los recursos de forma lógica. 

La galería es un recurso de nivel superior que proporciona control de acceso basado en roles de Azure (RBAC de Azure). 

## <a name="create-a-gallery"></a>Creación de una galería

Los caracteres permitidos para el nombre de la galería son letras mayúsculas o minúsculas, números y puntos. El nombre de la galería no puede contener guiones. Los nombres de las galerías deben ser únicos dentro de su suscripción. 

Elija una de las opciones siguientes para crear la galería:

### <a name="portal"></a>[Portal](#tab/portal)

En el ejemplo siguiente se crea una galería denominada *myGallery* en el grupo de recursos *myGalleryRG*.

1. Inicie sesión en Azure Portal en https://portal.azure.com.
1. Escriba **Azure Compute Gallery** en el cuadro de búsqueda y seleccione **Azure Compute Gallery** en los resultados.
1. En la página **Azure Compute Gallery**, haga clic en **Agregar**.
1. En la página **Create Azure Compute Gallery** (Crear Azure Compute Gallery), seleccione la suscripción correcta.
1. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba *myGalleryRG* como nombre.
1. En **Nombre**, escriba *myGallery* como nombre de la galería.
1. En **Región**, deje el valor predeterminado.
1. Puede escribir una descripción corta de la galería, como *Mi galería para pruebas*. Después, haga clic en **Revisar y crear**.
1. Una vez pasada la validación, seleccione **Crear**.
1. Cuando la implementación finalice, seleccione **Ir al recurso**.

### <a name="cli"></a>[CLI](#tab/cli)

Cree una galería mediante [az sig create](/cli/azure/sig#az_sig_create). En el ejemplo siguiente se crea un grupo de recursos denominado *myGalleryRG* en la región *Este de EE. UU.* y una galería denominada *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Cree una galería mediante [New-AzGallery](/powershell/module/az.compute/new-azgallery). En el ejemplo siguiente se crea una galería denominada *myGallery* en el grupo de recursos *myGalleryRG*.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Azure Compute Gallery for my organization' 
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
    "description": "Azure Compute Gallery for my organization"
  },
  "location": "eastus",
}
```
---

## <a name="next-steps"></a>Pasos siguientes

- Cree una [definición de imagen y una versión de imagen](image-version.md).

- [Cree una aplicación de máquina virtual](vm-applications-how-to.md) en la galería.

- También puede crear Azure Compute Gallery [creación de una instancia de Azure Compute Gallery](https://azure.microsoft.com/resources/templates/sig-create/) mediante una plantilla.

- [Azure Image Builder](./image-builder-overview.md) puede ayudar a automatizar la creación de versiones de la imagen e incluso se puede usar para actualizar y [crear una versión de la imagen a partir de una versión de imagen existente](./windows/image-builder-gallery-update-image-version.md). 
