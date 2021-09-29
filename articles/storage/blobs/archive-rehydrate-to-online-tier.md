---
title: Rehidratación de un blob archivado en un nivel en línea
titleSuffix: Azure Storage
description: Para poder leer un blob que se encuentra en el nivel de archivo, debe rehidratarlo al nivel de acceso esporádico o frecuente. Para rehidratar un blob, cópielo desde el nivel de archivo a un nivel en línea, o bien cambie su nivel de archivo a esporádico o frecuente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: a1681640b97d6ee183eefd7f65edd9b2d944f0f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633875"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Rehidratación de un blob archivado en un nivel en línea

Para leer un blob que se encuentra en el nivel de archivo, primero debe rehidratarlo al nivel de acceso esporádico o frecuente. Puede rehidratar un blob de una de estas dos maneras:

- Puede copiarlo en un nuevo blob en el nivel de acceso esporádico o frecuente con la operación [Copiar blob](/rest/api/storageservices/copy-blob) o [Copy Blob from URL](/rest/api/storageservices/copy-blob-from-url) (Copiar blob desde URL). Microsoft recomienda esta opción para la mayoría de los escenarios.
- Puede cambiar su nivel de acceso de archivo a frecuente o esporádico con la operación [Establecer el nivel del blob](/rest/api/storageservices/set-blob-tier).

Esta operación puede tardar hasta 15 horas en completarse. Puede configurar Azure Event Grid para que active un evento cuando se complete la rehidratación y ejecutar el código de la aplicación en respuesta. Para aprender a controlar un evento que ejecuta una función de Azure cuando se completa la operación de rehidratación de blobs, consulte [Ejecución de una función de Azure en respuesta a un evento de rehidratación de blobs](archive-rehydrate-handle-event.md).

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>Rehidratación de un blob con una operación de copia

Para rehidratar un blob desde el nivel de archivo copiándolo en un nivel en línea, use PowerShell, la CLI de Azure o una de las bibliotecas cliente de Azure Storage. Tenga en cuenta que, al copiar un blob archivado en un nivel en línea, los blobs de origen y destino deben tener nombres diferentes.

Una vez completada la operación de copia, el blob de destino aparece en el nivel de archivo. A continuación, el blob de destino se rehidrata al nivel en línea que especificó en la operación de copia. Cuando el blob de destino está totalmente rehidratado, está disponible en el nuevo nivel en línea.

En los ejemplos siguientes se muestra cómo copiar un blob archivado con PowerShell o la CLI de Azure.

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

N/D

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Para copiar un blob archivado en un nivel en línea con PowerShell, llame al comando [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) y especifique el nivel de destino y la prioridad de rehidratación. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$srcContainerName = "<source-container>"
$destContainerName = "<dest-container>"
$srcBlobName = "<source-blob>"
$destBlobName = "<dest-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Copy the source blob to a new destination blob in hot tier with standard priority.
Start-AzStorageBlobCopy -SrcContainer $srcContainerName `
    -SrcBlob $srcBlobName `
    -DestContainer $destContainerName `
    -DestBlob $destBlobName `
    -StandardBlobTier Hot `
    -RehydratePriority Standard `
    -Context $ctx
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para copiar un blob archivado en un nivel en línea con la CLI de Azure, llame al comando [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) y especifique el nivel de destino y la prioridad de rehidratación. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --rehydrate-priority standard \
    --auth-mode login
```

---

## <a name="rehydrate-a-blob-by-changing-its-tier"></a>Rehidratación de un blob cambiando su nivel

Para rehidratar un blob cambiando su nivel de archivo a operación frecuente o esporádica, use Azure Portal, PowerShell o la CLI de Azure.

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Para cambiar el nivel de un blob de archivo a frecuente o esporádico en Azure Portal, siga estos pasos:

1. Busque el blob que se va a rehidratar en Azure Portal.
1. Seleccione el botón **Más** en el lado derecho de la página.
1. Seleccione **Cambiar nivel**.
1. Seleccione el nivel de acceso de destino en la lista desplegable **Nivel de acceso**.
1. En la lista desplegable **Prioridad de rehidratación**, seleccione la prioridad de rehidratación deseada. Tenga en cuenta que, al establecer la prioridad de rehidratación en *Alta*, normalmente la rehidratación es más rápida, pero también conlleva un costo mayor.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="Captura de pantalla que muestra cómo rehidratar un blob desde el nivel de archivo en Azure Portal.":::

1. Seleccione el botón **Guardar**.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Para cambiar el nivel de un blob de archivo a frecuente o esporádico con PowerShell, use la propiedad **ICloudBlob** del blob para devolver una referencia de .NET al blob y, luego, llame al método **SetStandardBlobTier** en esa referencia. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to hot with standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "High")
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para cambiar el nivel de un blob de archivo a frecuente o esporádico con la CLI de Azure, llame al comando [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier). No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurecli
az storage blob set-tier \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --account-name <account-name> \
    --rehydrate-priority High \
    --auth-mode login
```

---

## <a name="rehydrate-a-large-number-of-blobs"></a>Rehidratación de un gran número de blobs

Para rehidratar un gran número de blobs a la vez, llame a la operación [Blob Batch](/rest/api/storageservices/blob-batch) para llamar a [Set Blob Tier](/rest/api/storageservices/set-blob-tier) como una operación masiva. Para ver un ejemplo de código que muestra cómo realizar la operación por lotes, consulte [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/).

## <a name="check-the-status-of-a-rehydration-operation"></a>Comprobación del estado de una operación de rehidratación

Mientras se rehidrata el blob, puede comprobar su estado y su prioridad de rehidratación mediante Azure Portal, PowerShell o la CLI de Azure. La propiedad de estado puede devolver *rehydrate-pending-to-hot* o *rehydrate-pending-to-cool*, según el nivel de acceso de destino de la operación de rehidratación. La propiedad de prioridad de rehidratación devuelve *Standard* o *High*.

Tenga en cuenta que la rehidratación de un blob archivado puede tardar hasta 15 horas y sondear repetidamente el estado del blob para determinar si la rehidratación está completa resulta ineficaz. El uso de Azure Event Grid para capturar el evento que se activa cuando se completa la rehidratación ofrece una mejor relación costo-rendimiento. Para aprender a ejecutar una función de Azure cuando se produce un evento en la rehidratación de blobs, consulte [Ejecución de una función de Azure en respuesta a un evento de rehidratación de blobs](archive-rehydrate-handle-event.md).

### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Para comprobar el estado y la prioridad de una operación de rehidratación pendiente en Azure Portal, muestre el cuadro de diálogo **Cambiar nivel**:

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="Captura de pantalla que muestra el estado de rehidratación de un blob en Azure Portal.":::

Una vez completada la rehidratación, puede ver que aparece ahora el blob totalmente rehidratado en Azure Portal en el nivel en línea de destino.

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="Captura de pantalla que muestra el blob rehidratado en el nivel de acceso esporádico y el blob de registro escrito por el controlador de eventos.":::

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Para comprobar el estado y la prioridad de una operación de rehidratación pendiente con PowerShell, llame al comando [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) y compruebe las propiedades **ArchiveStatus** y **RehydratePriority** del blob. Si la rehidratación es una operación de copia, compruebe estas propiedades en el blob de destino. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```powershell
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$rehydratingBlob.BlobProperties.ArchiveStatus
$rehydratingBlob.BlobProperties.RehydratePriority
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para comprobar el estado y la prioridad de una operación de rehidratación pendiente con la CLI de Azure, llame al comando [az storage blob show](/cli/azure/storage/blob#az_storage_blob_show) y compruebe las propiedades **rehydrationStatus** y **rehydratePriority** del blob de destino. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <destination-blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="see-also"></a>Vea también

- [Azure Blob Storage: niveles de acceso frecuente, esporádico y de archivo](storage-blob-storage-tiers.md).
- [Introducción a la rehidratación de blobs desde el nivel de archivo](archive-rehydrate-overview.md)
- [Ejecución de una función de Azure en respuesta a un evento de rehidratación de blobs](archive-rehydrate-handle-event.md)
- [Reacción a eventos de Blob Storage](storage-blob-event-overview.md)
