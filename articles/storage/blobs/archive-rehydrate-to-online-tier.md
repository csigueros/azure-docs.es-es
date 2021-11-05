---
title: Rehidratación de un blob archivado en un nivel en línea
titleSuffix: Azure Storage
description: Para poder leer un blob que se encuentra en el nivel de archivo, debe rehidratarlo al nivel de acceso esporádico o frecuente. Para rehidratar un blob, cópielo desde el nivel de archivo a un nivel en línea, o bien cambie su nivel de archivo a esporádico o frecuente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.subservice: blobs
ms.openlocfilehash: 4c8f1c2f769340cb36832b06bf5a76258b69cd9f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008817"
---
# <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Rehidratación de un blob archivado en un nivel en línea

Para leer un blob que se encuentra en el nivel de archivo, primero debe rehidratarlo a un nivel en línea (esporádico o frecuente). Puede rehidratar un blob de una de estas dos maneras:

- Puede copiarlo en un nuevo blob en el nivel de acceso esporádico o frecuente con la operación [Copiar blob](/rest/api/storageservices/copy-blob). Microsoft recomienda esta opción para la mayoría de los escenarios.
- Puede cambiar su nivel de acceso de archivo a frecuente o esporádico con la operación [Establecer el nivel del blob](/rest/api/storageservices/set-blob-tier).

Al rehidratar un blob, puede especificar la prioridad de la operación en prioridad estándar o alta. Una operación de rehidratación de prioridad estándar puede tardar hasta 15 horas en completarse. Una operación de prioridad alta tiene prioridad sobre las solicitudes de prioridad estándar y puede completarse en menos de una hora para objetos de menos de 10 GB de tamaño. Puede cambiar la prioridad de rehidratación de *Estándar* a *Alta* mientras la operación está pendiente.

Puede configurar Azure Event Grid para que active un evento cuando se complete la rehidratación y ejecutar el código de la aplicación en respuesta. Para aprender a controlar un evento que ejecuta una función de Azure cuando se completa la operación de rehidratación de blobs, consulte [Ejecución de una función de Azure en respuesta a un evento de rehidratación de blobs](archive-rehydrate-handle-event.md).

Para más información sobre la rehidratación de un blob, consulte [Rehidratación de blobs desde el nivel de archivo](archive-rehydrate-overview.md).

## <a name="rehydrate-a-blob-with-a-copy-operation"></a>Rehidratación de un blob con una operación de copia

Para rehidratar un blob desde el nivel de archivo copiándolo en un nivel en línea, use PowerShell, la CLI de Azure o una de las bibliotecas cliente de Azure Storage. Tenga en cuenta que, al copiar un blob archivado en un nivel en línea, los blobs de origen y destino deben tener nombres diferentes.

Una vez completada la operación de copia, el blob de destino aparece en el nivel de archivo. A continuación, el blob de destino se rehidrata al nivel en línea que especificó en la operación de copia. Cuando el blob de destino está totalmente rehidratado, está disponible en el nuevo nivel en línea.

En los ejemplos siguientes se muestra cómo copiar un blob archivado con PowerShell o la CLI de Azure.

### <a name="portal"></a>[Portal](#tab/azure-portal)

N/D

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para copiar un blob archivado en un nivel en línea con PowerShell, llame al comando [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) y especifique el nivel de destino y la prioridad de rehidratación. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurepowershell
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

# Copy the source blob to a new destination blob in Hot tier with Standard priority.
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

Para rehidratar un blob cambiando su nivel de archivo a frecuente o esporádico, use Azure Portal, PowerShell o la CLI de Azure.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para cambiar el nivel de un blob de archivo a frecuente o esporádico en Azure Portal, siga estos pasos:

1. Busque el blob que se va a rehidratar en Azure Portal.
1. Seleccione el botón **Más** en el lado derecho de la página.
1. Seleccione **Cambiar nivel**.
1. Seleccione el nivel de acceso de destino en la lista desplegable **Nivel de acceso**.
1. En la lista desplegable **Prioridad de rehidratación**, seleccione la prioridad de rehidratación deseada. Tenga en cuenta que, al establecer la prioridad de rehidratación en *Alta*, normalmente la rehidratación es más rápida, pero también conlleva un costo mayor.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/rehydrate-change-tier-portal.png" alt-text="Captura de pantalla que muestra cómo rehidratar un blob desde el nivel de archivo en Azure Portal.":::

1. Seleccione el botón **Guardar**.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para cambiar el nivel de un blob de archivo a frecuente o esporádico con PowerShell, use la propiedad **BlobClient** del blob para devolver una referencia de .NET al blob y, luego, llame al método **SetAccessTier** en esa referencia. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<archived-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to Hot with Standard priority.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Hot", $null, "Standard")
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para cambiar el nivel de un blob de archivo a frecuente o esporádico con la CLI de Azure, llame al comando [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier). No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <archived-blob> \
    --tier Hot \
    --rehydrate-priority Standard \
    --auth-mode login
```

---

## <a name="bulk-rehydrate-a-set-of-blobs"></a>Rehidratación masiva de un conjunto de blobs

Para rehidratar un gran número de blobs a la vez, llame a la operación [Blob Batch](/rest/api/storageservices/blob-batch) para llamar a [Set Blob Tier](/rest/api/storageservices/set-blob-tier) como una operación masiva. Para ver un ejemplo de código que muestra cómo realizar la operación por lotes, consulte [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/).

## <a name="check-the-status-of-a-rehydration-operation"></a>Comprobación del estado de una operación de rehidratación

Mientras se rehidrata el blob, puede comprobar su estado y su prioridad de rehidratación mediante Azure Portal, PowerShell o la CLI de Azure. La propiedad de estado puede devolver *rehydrate-pending-to-hot* o *rehydrate-pending-to-cool*, según el nivel de acceso de destino de la operación de rehidratación. La propiedad de prioridad de rehidratación devuelve *Standard* o *High*.

Tenga en cuenta que la rehidratación de un blob archivado puede tardar hasta 15 horas y sondear repetidamente el estado del blob para determinar si la rehidratación está completa resulta ineficaz. El uso de Azure Event Grid para capturar el evento que se activa cuando se completa la rehidratación ofrece una mejor relación costo-rendimiento. Para aprender a ejecutar una función de Azure cuando se produce un evento en la rehidratación de blobs, consulte [Ejecución de una función de Azure en respuesta a un evento de rehidratación de blobs](archive-rehydrate-handle-event.md).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para comprobar el estado y la prioridad de una operación de rehidratación pendiente en Azure Portal, muestre el cuadro de diálogo **Cambiar nivel**:

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-status-portal.png" alt-text="Captura de pantalla que muestra el estado de rehidratación de un blob en Azure Portal.":::

Una vez completada la rehidratación, puede ver que aparece ahora el blob totalmente rehidratado en Azure Portal en el nivel en línea de destino.

:::image type="content" source="media/archive-rehydrate-to-online-tier/set-blob-tier-rehydrated.png" alt-text="Captura de pantalla que muestra el blob rehidratado en el nivel de acceso esporádico y el blob de registro escrito por el controlador de eventos.":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para comprobar el estado y la prioridad de una operación de rehidratación pendiente con PowerShell, llame al comando [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) y compruebe las propiedades **ArchiveStatus** y **RehydratePriority** del blob. Si la rehidratación es una operación de copia, compruebe estas propiedades en el blob de destino. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurepowershell
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
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

## <a name="change-the-rehydration-priority-of-a-pending-operation"></a>Cambio de la prioridad de rehidratación de una operación pendiente

Mientras esté pendiente una operación de rehidratación de prioridad estándar, puede cambiar la configuración de prioridad de rehidratación de un blob de *Estándar* a *Alta* para rehidratar ese blob más rápidamente.

Tenga en cuenta que la configuración de prioridad de rehidratación no se puede reducir de *Alta* a *Estándar* para una operación pendiente. Tenga en cuenta también que cambiar la prioridad de rehidratación puede tener un impacto en la facturación. Para obtener más información, consulte [Rehidratación de blobs desde el nivel de archivo](archive-rehydrate-overview.md).

### <a name="change-the-rehydration-priority-for-a-pending-set-blob-tier-operation"></a>Cambio de la prioridad de rehidratación para una operación Establecer el nivel del blob pendiente

Para cambiar la prioridad de rehidratación mientras está pendiente una operación [Establecer el nivel del blob](/rest/api/storageservices/set-blob-tier) de prioridad estándar, use Azure Portal, PowerShell, la CLI de Azure o una de las bibliotecas cliente de Azure Storage.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para cambiar la prioridad de rehidratación de una operación pendiente con Azure Portal, siga estos pasos:

1. Vaya al blob para el que desea cambiar la prioridad de rehidratación y selecciónelo.
1. Seleccione el botón **Cambiar nivel.**
1. En el cuadro de diálogo **Cambiar nivel**, establezca el nivel de acceso en el nivel de acceso en línea de destino para el blob de rehidratado (esporádico o frecuente). El campo **Estado de archivo** muestra el nivel en línea de destino.
1. En la lista desplegable **Prioridad de rehidratación**, establezca la prioridad en *Alta*.
1. Seleccione **Guardar**.

    :::image type="content" source="media/archive-rehydrate-to-online-tier/update-rehydration-priority-portal.png" alt-text="Captura de pantalla que muestra cómo actualizar la prioridad de rehidratación de un blob de rehidratación en Azure Portal.":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para cambiar la prioridad de rehidratación de una operación pendiente con PowerShell, primero obtenga las propiedades del blob del servicio. Este paso es necesario para asegurarse de que tiene un objeto con la configuración de propiedades más reciente. A continuación, use la propiedad **BlobClient** del blob para devolver una referencia de .NET al blob y, luego, llame al método **SetAccessTier** en esa referencia.

```azurepowershell
# Get the blob from the service.
$rehydratingBlob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

# Verify that the current rehydration priority is Standard. 
if ($rehydratingBlob.BlobProperties.RehydratePriority -eq "Standard")
{
    # Change rehydration priority to High, using the same target tier.
    
    if ($rehydratingBlob.BlobProperties.ArchiveStatus -eq "rehydrate-pending-to-hot")
    {
        $rehydratingBlob.BlobClient.SetAccessTier("Hot", $null, "High")
        "Changing rehydration priority to High for blob moving to Hot tier."
    }
    
    if ($rehydratingBlob.BlobProperties.ArchiveStatus -eq "rehydrate-pending-to-cool")
    {
        $rehydratingBlob.BlobClient.SetAccessTier("Cool", $null, "High")
        "Changing rehydration priority to High for blob moving to Cool tier."
    }
}
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para cambiar la prioridad de rehidratación de una operación pendiente con la CLI de Azure, llame al comando [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) con el parámetro `--rehydrate-priority` establecido en *Alta*. El nivel de destino (esporádico o frecuente) debe ser el mismo nivel que especificó originalmente para la operación de rehidratación. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurecli
# Update the rehydration priority for a blob moving to the Hot tier.
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Hot \
    --rehydrate-priority High \
    --auth-mode login

# Show the updated property values.
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

---

### <a name="change-the-rehydration-priority-for-a-pending-copy-blob-operation"></a>Cambio de la prioridad de rehidratación de una operación Copiar blob pendiente

Al rehidratar un blob copiando el blob archivado en un nivel en línea, Azure Storage inmediatamente crea el blob de destino en el nivel de archivo. A continuación, el blob de destino se rehidrata en el nivel de destino con la prioridad especificada en la operación de copia. Para más información sobre cómo rehidratar un blob archivado con una operación de copia, consulte [Copia de un blob archivado en un nivel en línea](archive-rehydrate-overview.md#copy-an-archived-blob-to-an-online-tier).

Para realizar la operación de copia desde el nivel de archivo a un nivel en línea con prioridad Estándar, use PowerShell, la CLI de Azure o una de las bibliotecas cliente de Azure Storage. Para más información, consulte [Rehidratación de un blob con una operación de copia](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation). A continuación, para cambiar la prioridad de rehidratación de *Estándar* a *Alta* para la rehidratación pendiente, llame a **Establecer el nivel del blob** en el blob de destino y especifique el nivel de destino.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Después de haber iniciado la operación de copia, verá en Azure Portal que tanto el blob de origen como el de destino se encuentran en el nivel de archivo. El blob de destino se está rehidratando con prioridad Estándar.

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-properties-portal-standard-priority.png" alt-text="Captura de pantalla que muestra el blob de destino en el nivel de archivo y la rehidratación con prioridad Estándar.":::

Para cambiar la prioridad de rehidratación del blob de destino, siga estos pasos:

1. Seleccione el blob de destino.
1. Seleccione el botón **Cambiar nivel.**
1. En el cuadro de diálogo **Cambiar nivel**, establezca el nivel de acceso en el nivel de acceso en línea de destino para el blob de rehidratado (esporádico o frecuente). El campo **Estado de archivo** muestra el nivel en línea de destino.
1. En la lista desplegable **Prioridad de rehidratación**, establezca la prioridad en *Alta*.
1. Seleccione **Guardar**.

La página de propiedades del blob de destino ahora muestra que se está rehidratando con prioridad alta.

:::image type="content" source="media/archive-rehydrate-to-online-tier/rehydration-properties-portal-high-priority.png" alt-text="Captura de pantalla que muestra el blob de destino en el nivel de archivo y la rehidratación con prioridad Alta.":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Después de iniciar la operación de copia, compruebe las propiedades del blob de destino. Verá que el blob de destino está en el nivel de archivo y se está rehidratando con prioridad Estándar.

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$destContainerName = "<container>"
$destBlobName = "<destination-blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Get properties for the destination blob.
$destinationBlob = Get-AzStorageBlob -Container $destContainerName `
    -Blob $destBlobName `
    -Context $ctx

$destinationBlob.BlobProperties.AccessTier
$destinationBlob.BlobProperties.ArchiveStatus
$destinationBlob.BlobProperties.RehydratePriority
```

A continuación, llame al método **SetAccessTier** a través de PowerShell para cambiar la prioridad de rehidratación del blob de destino a *Alta*, como se describe en [Cambio de la prioridad de rehidratación para una operación Establecer el nivel del blob pendiente](#change-the-rehydration-priority-for-a-pending-set-blob-tier-operation). El nivel de destino (esporádico o frecuente) debe ser el mismo nivel que especificó originalmente para la operación de rehidratación. Vuelva a comprobar las propiedades para comprobar que el blob se está rehidratando con prioridad alta.

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Después de iniciar la operación de copia, compruebe las propiedades del blob de destino. Verá que el blob de destino está en el nivel de archivo y se está rehidratando con prioridad Estándar.

```azurecli
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query '[rehydratePriority, properties.rehydrationStatus]' \
    --output tsv \
    --auth-mode login
```

Luego, llame al comando [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier) con el parámetro `--rehydrate-priority` establecido en *Alta*, como se describe en [Cambio de la prioridad de rehidratación para una operación Establecer el nivel del blob pendiente](#change-the-rehydration-priority-for-a-pending-set-blob-tier-operation). El nivel de destino (esporádico o frecuente) debe ser el mismo nivel que especificó originalmente para la operación de rehidratación. Vuelva a comprobar las propiedades para comprobar que el blob se está rehidratando con prioridad alta.

---

## <a name="see-also"></a>Consulte también

- [Niveles de acceso frecuente, esporádico y de archivo de los datos de blob](access-tiers-overview.md)
- [Introducción a la rehidratación de blobs desde el nivel de archivo](archive-rehydrate-overview.md)
- [Ejecución de una función de Azure en respuesta a un evento de rehidratación de blobs](archive-rehydrate-handle-event.md)
- [Reacción a eventos de Blob Storage](storage-blob-event-overview.md)
