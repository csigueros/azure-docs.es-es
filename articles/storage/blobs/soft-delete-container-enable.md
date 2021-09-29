---
title: Habilitación y administración de la eliminación temporal para contenedores
titleSuffix: Azure Storage
description: Habilite la eliminación temporal de contenedores para recuperar sus datos con mayor facilidad si estos se modifican o se eliminan de forma errónea.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/06/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 208f4ff6b43a722e14a788d052350117aeac56dc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562502"
---
# <a name="enable-and-manage-soft-delete-for-containers"></a>Habilitación y administración de la eliminación temporal para contenedores

La eliminación temporal de contenedores impide que los datos se modifiquen o eliminen de forma accidental o errónea. Cuando la eliminación temporal de contenedores está habilitada para una cuenta de almacenamiento, un contenedor y su contenido se pueden recuperar después de eliminarse, dentro de un período de retención especificado. Para obtener más información sobre la eliminación temporal de contenedores, consulte [Eliminación temporal para contenedores](soft-delete-container-overview.md).

Para la protección de datos de un extremo a otro, Microsoft recomienda también habilitar la eliminación temporal para blobs y el control de versiones de blobs. Para información sobre cómo habilitar también la eliminación temporal para blobs, consulte [Habilitación y administración de la eliminación temporal para blobs](soft-delete-blob-enable.md). Para información sobre cómo habilitar el control de versiones de blobs, consulte [Control de versiones de blobs](versioning-overview.md).

## <a name="enable-container-soft-delete"></a>Habilitación de la eliminación temporal de contenedores

Puede habilitar o deshabilitar la eliminación temporal de contenedores para la cuenta de almacenamiento en cualquier momento mediante Azure Portal, PowerShell, la CLI de Azure o una plantilla de Azure Resource Manager. Microsoft recomienda establecer el período de retención para la eliminación temporal de contenedores en un mínimo de siete días.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para habilitar la eliminación temporal de contenedores para la cuenta de almacenamiento mediante Azure Portal, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com/), vaya a la cuenta de almacenamiento.
1. Busque la opción **Protección de datos** en **Administración de datos**.
1. Seleccione **Enable soft delete for containers** (Habilitación de la eliminación temporal para contenedores).
1. Especifique un período de retención de entre 1 y 365 días.
1. Guarde los cambios.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Captura de pantalla que muestra cómo habilitar la eliminación temporal de contenedores en Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para habilitar la eliminación temporal de contenedores con PowerShell, primero instale la versión 3.9.0 o posterior del módulo [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage). Después, llame al comando **Enable-AzStorageContainerDeleteRetentionPolicy** y especifique el número de días para el período de retención. No olvide reemplazar los valores entre corchetes angulares por sus propios valores:

```azurepowershell-interactive
Enable-AzStorageContainerDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Para deshabilitar la eliminación temporal de contenedores, llame al comando **Disable-AzStorageContainerDeleteRetentionPolicy**.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para habilitar la eliminación temporal de contenedores con la CLI de Azure, primero instale la CLI de Azure, versión 2.26.0 o posterior. Después, llame al comando [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) y especifique el número de días para el período de retención. No olvide reemplazar los valores entre corchetes angulares por sus propios valores:

```azurecli-interactive
az storage account blob-service-properties update \
    --enable-container-delete-retention true \
    --container-delete-retention-days 7 \
    --account-name <storage-account> \
    --resource-group <resource_group>
```

Para deshabilitar la eliminación temporal de contenedores, especifique `false` para el parámetro `--enable-container-delete-retention`.

# <a name="template"></a>[Plantilla](#tab/template)

Para habilitar la eliminación temporal de contenedores con una plantilla de Azure Resource Manager, cree una plantilla que establezca la propiedad **containerDeleteRetentionPolicy**. En los siguientes pasos se muestra cómo crear una plantilla en Azure Portal.

1. En Azure Portal, elija **Crear un recurso**.
1. En **Buscar en Marketplace**, escriba **implementación de plantillas** y, después, presione **ENTRAR**.
1. Elija **Template Deployment**, elija **Crear** y, luego, **Cree su propia plantilla en el editor**.
1. En el editor de plantillas, pegue el código JSON siguiente. Reemplace el marcador de posición `<account-name>` por el nombre de la cuenta de almacenamiento.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

1. Especifique el período de retención. El valor predeterminado es 7.
1. Guarde la plantilla.
1. Especifique el grupo de recursos de la cuenta y, luego, elija el botón **Revisar y crear** para implementar la plantilla y habilitar la eliminación temporal de contenedores.

---

## <a name="view-soft-deleted-containers"></a>Visualización de contenedores eliminados temporalmente

Cuando la eliminación temporal está habilitada, puede ver los contenedores eliminados temporalmente en Azure Portal. Los contenedores eliminados temporalmente son visibles durante el período de retención especificado. Una vez expirado el período de retención, un contenedor eliminado temporalmente se elimina permanentemente y deja de estar visible.

Para ver los contenedores eliminados temporalmente en Azure Portal, siga estos pasos:

1. Vaya a su cuenta de almacenamiento en Azure Portal y acceda a la lista de contenedores.
1. Alterne el conmutador Show deleted containers (Mostrar contenedores eliminados) para incluir los contenedores eliminados en la lista.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Captura de pantalla que muestra cómo ver los contenedores eliminados temporalmente en Azure Portal":::

## <a name="restore-a-soft-deleted-container"></a>Restauración de un contenedor eliminado temporalmente

Puede restaurar un contenedor eliminado temporalmente y su contenido dentro del período de retención. Para restaurar un contenedor eliminado temporalmente en Azure Portal, siga estos pasos:

1. Vaya a su cuenta de almacenamiento en Azure Portal y acceda a la lista de contenedores.
1. Muestre el menú contextual del contenedor que desea eliminar y elija **Recuperar** en el menú.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Captura de pantalla que muestra cómo restaurar un contenedor eliminado temporalmente en Azure Portal":::

## <a name="next-steps"></a>Pasos siguientes

- [Eliminación temporal de contenedores](soft-delete-container-overview.md)
- [Eliminación temporal para blobs](soft-delete-blob-overview.md)
- [Control de versiones de blobs](versioning-overview.md)
