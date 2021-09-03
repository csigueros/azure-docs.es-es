---
title: 'Habilitación de la eliminación temporal: recursos compartidos de archivos de Azure'
description: Obtenga información sobre cómo habilitar la eliminación temporal en recursos compartidos de archivos de Azure para la recuperación de datos y para evitar la eliminación accidental.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
services: storage
ms.openlocfilehash: 314d1ee8ee957b21679735594ad2f7d7f50f4d38
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112118313"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Habilitación de la eliminación temporal en recursos compartidos de archivos de Azure
Azure Files ofrece la posibilidad de eliminar temporalmente recursos compartidos de archivos, con el fin de que pueda recuperar más fácilmente los datos cuando una aplicación u otro usuario de la cuenta de almacenamiento los hayan eliminado por error. Para más información sobre la eliminación temporal, consulte [Cómo evitar la eliminación accidental de recursos compartidos de archivos de Azure](storage-files-prevent-file-share-deletion.md).

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>Requisitos previos
- Si planea usar Azure PowerShell, [instale la versión más reciente](/powershell/azure/install-az-ps).
- Si planea usar la CLI de Azure, [instale la versión más reciente](/cli/azure/install-azure-cli).

## <a name="getting-started"></a>Introducción
En las secciones siguientes se muestra cómo habilitar y usar la eliminación temporal para recursos compartidos de archivos de Azure en una cuenta de almacenamiento existente:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
1. Vaya a la cuenta de almacenamiento y seleccione **Recursos compartidos de archivos** en **Almacenamiento de datos**.
1. Seleccione **Habilitado** junto a **Eliminación temporal**.
1. Seleccione **Enabled** (Habilitado) para **Soft delete for all file shares** (Eliminación temporal para todos los recursos compartidos de archivos).
1. Seleccione **File share retention period in days** (Período de retención del recurso compartido de archivos en días) y escriba el número que prefiera.
1. Seleccione **Guardar** para confirmar la configuración de retención de datos.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="Captura de pantalla del panel de configuración de la eliminación temporal de la cuenta de almacenamiento. Se resaltan la sección de eliminación temporal de recursos compartidos de archivos, el botón de alternancia para habilitar, se establece un período de retención y se guarda. De esta forma, se habilitará la eliminación temporal para todos los recursos compartidos de archivos de la cuenta de almacenamiento.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para habilitar la eliminación automática, debe actualizar la configuración de todos los recursos compartidos de archivos de Azure, también conocidos como propiedades `FileService`. En el ejemplo siguiente se habilita la eliminación temporal para todos los recursos compartidos de archivos de una cuenta de almacenamiento. No olvide reemplazar `<resource-group>` y `<storage-account>` por los valores correctos para el entorno.

```PowerShell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"

Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $true `
    -ShareRetentionDays 7
```

Puede comprobar si la eliminación temporal está habilitada y ver su directiva de retención con el siguiente comando:

```PowerShell
Get-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
Para habilitar la eliminación temporal, debe actualizar las propiedades de servicio de un cliente de archivo. En el ejemplo siguiente se habilita la eliminación temporal para todos los recursos compartidos de archivos de una cuenta de almacenamiento. No olvide reemplazar `<resource-group>` y `<storage-account>` por los valores correctos para el entorno.

```bash
resourceGroupName="<resource-group>"
storageAccountName="<storage-account>"

az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --account-name $storageAccountName \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Puede comprobar si la eliminación temporal está habilitada y ver su directiva de retención con el siguiente comando:

```bash
az storage account file-service-properties show \
    -resource-group $resourceGroupName \
    -account-name $storageAccountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Restauración del recurso compartido de archivos eliminado temporalmente

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para restaurar un recurso compartido de archivos eliminado temporalmente:

1. Vaya a la cuenta de almacenamiento y seleccione **Recursos compartidos de archivos**.
1. En la hoja del recurso compartido de archivos, habilite **Show deleted shares** (Mostrar recursos compartidos eliminados) para mostrar los recursos compartidos que se han eliminado temporalmente.

    Se mostrarán los recursos compartidos que se encuentren en estado **Eliminado**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Si la columna de estado, situada junto a la columna de nombre, se establece en Eliminado, el recurso compartido de archivos se encuentra en un estado de eliminación temporal. Se eliminará de forma permanente después del período de retención especificado.":::

1. Seleccione el recurso compartido y **recuperar**; de esta forma se restaurará el recurso compartido.

    Puede confirmar que el recurso compartido se haya restaurado desde que su estado cambia a **Activo**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Si la columna de estado, la columna situada junto a la columna de nombre, se establece en Activo, el recurso compartido de archivos se ha restaurado.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para restaurar un recurso compartido de archivos eliminado temporalmente, primero debe obtener el valor `-DeletedShareVersion` del recurso compartido. A fin de obtener ese valor, use el comando siguiente para enumerar todos los recursos compartidos eliminados de la cuenta de almacenamiento.

```PowerShell
Get-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -IncludeDeleted
```

Una vez que haya identificado el recurso compartido que desea restaurar, puede usarlo con el siguiente comando para restaurarlo:

```PowerShell
Restore-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -DeletedShareVersion 01D5E2783BDCDA97 # replace with your deleted version number
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
Para restaurar un recurso compartido de archivos eliminado temporalmente, primero debe obtener el valor `--deleted-version` del recurso compartido. A fin de obtener ese valor, use el comando siguiente para enumerar todos los recursos compartidos eliminados de la cuenta de almacenamiento.

```bash
az storage share-rm list \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --include-deleted
```

Una vez que haya identificado el recurso compartido que desea restaurar, puede usarlo con el siguiente comando para restaurarlo:

```bash
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

---

## <a name="disable-soft-delete"></a>Deshabilitación de la eliminación temporal
Si desea dejar de usar la eliminación temporal, siga estas instrucciones. Para eliminar de forma permanente un recurso compartido de archivos que se ha eliminado temporalmente, debe recuperarlo, deshabilitar la eliminación temporal y, a continuación, eliminarlo de nuevo. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Vaya a la cuenta de almacenamiento y seleccione **Recursos compartidos de archivos** en **Almacenamiento de datos**.
1. Seleccione el vínculo situado junto a **Eliminación temporal**.
1. Seleccione **Disabled** (Deshabilitado) para **Soft delete for all file shares** (Eliminación temporal para todos los recursos compartidos de archivos).
1. Seleccione **Guardar** para confirmar la configuración de retención de datos.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="Deshabilitar la eliminación temporal le permitirá eliminar de forma inmediata y permanente todos los recursos compartidos de archivos de la cuenta de almacenamiento cuando descanse.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Puede usar el siguiente comando para deshabilitar la eliminación temporal en la cuenta de almacenamiento.

```PowerShell
Update-AzStorageFileServiceProperty `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -EnableShareDeleteRetentionPolicy $false
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
Puede usar el siguiente comando para deshabilitar la eliminación temporal en la cuenta de almacenamiento.

```bash
az storage account file-service-properties update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --enable-delete-retention false
```

---

## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre otra forma de protección y recuperación de datos, consulte nuestro artículo [Introducción a las instantáneas de recursos compartidos de Azure Files](storage-snapshots-files.md).
