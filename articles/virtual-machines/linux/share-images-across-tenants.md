---
title: Uso compartido de imágenes de la galería entre inquilinos
description: Aprenda a compartir imágenes de máquina virtual entre inquilinos de Azure mediante instancias de Azure Compute Gallery y la CLI de Azure.
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 525e1f51629fe698d1c9f2c3cb9454ddd0a5d183
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448906"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-the-azure-cli"></a>Uso compartido de imágenes de VM de la galería entre inquilinos de Azure mediante la CLI de Azure

Las instancias de Azure Compute Gallery permiten compartir imágenes mediante RBAC de Azure. Puede usar Azure RBAC para compartir imágenes dentro de su inquilino e incluso con personas de fuera de él. Para más información sobre esta opción de uso compartido simple, consulte [Compartir la galería](./shared-images-portal.md#share-the-gallery).

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> No se puede usar el portal para implementar una VM desde una imagen en otro inquilino de Azure. Para crear una VM desde una imagen que se comparte entre los inquilinos, debe usar la CLI de Azure o [Powershell](../windows/share-images-across-tenants.md).

## <a name="create-a-vm-using-azure-cli"></a>Creación de una máquina virtual mediante la CLI de Azure

Inicie sesión en la entidad de servicio del inquilino 1 mediante el id. de la aplicación, la clave de aplicación y el identificador del inquilino 1. Puede usar `az account show --query "tenantId"` para obtener el identificador del inquilino, si es necesario.

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
Inicie sesión en la entidad de servicio del inquilino 2 mediante el id. de la aplicación, la clave de aplicación y el identificador del inquilino 2:

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

Cree la máquina virtual. Reemplace la información del ejemplo por la suya.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>Pasos siguientes

Si experimenta algún problema, puede [solucionar problemas de las galerías](../troubleshooting-shared-images.md).
