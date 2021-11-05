---
title: Archivado de un blob
titleSuffix: Azure Storage
description: Aprenda a crear un blob en el nivel de archivo o a mover un blob existente al nivel de archivo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 5b779df91c4f0347e329348b7bdccd49691d0d5d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090841"
---
# <a name="archive-a-blob"></a>Archivado de un blob

El nivel de acceso de archivo es un nivel sin conexión para almacenar datos blob a los que rara vez se accede. El nivel de acceso de archivo tiene el coste de almacenamiento más bajo, pero tiene mayores costes de recuperación de datos y latencia en comparación con los niveles de acceso con conexión (frecuente y esporádico). Los datos deben permanecer en el nivel de archivo durante al menos 180 días o estar sujetos a un cargo por eliminación temprana. Para más información sobre el nivel de archivo, consulte [Niveles de acceso de archivo](access-tiers-overview.md#archive-access-tier).

Un blob no se puede leer ni modificar mientras se encuentre en el nivel de archivo. Para leer o descargar un blob en el nivel de acceso de archivo, primero debe rehidratarlo a un nivel en línea, bien sea de acceso esporádico o de acceso frecuente. Los datos del nivel de archivo pueden tardar hasta 15 horas en rehidratarse, dependiendo de la prioridad que especifique para la operación de rehidratación. Para obtener más información sobre la rehidratación de blobs, consulte [Resumen de la rehidratación de blobs desde el nivel de acceso de archivo](archive-rehydrate-overview.md).

> [!CAUTION]
> Los blob en nivel de acceso de archivo no tienen conexión, &mdash; por lo cual, no se podrán leer ni modificar&mdash; hasta que se hayan rehidratado. El proceso de rehidratación puede tardar varias horas y tiene costes asociados. Antes de mover datos al nivel de archivo, considere si desconectar los datos de blob puede afectar a los flujos de trabajo.

Puede usar las bibliotecas Azure Portal, PowerShell, CLI de Azure o una de las bibliotecas cliente Azure Storage para administrar el archivado de datos.

## <a name="archive-blobs-on-upload"></a>Archivado de blobs al cargar

Para archivar uno o varios blobs durante la carga, cree el blob directamente en el nivel de acceso de archivo.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para archivar un blob o un conjunto de blobs en la carga desde Azure Portal, siga estos pasos:

1. Vaya al contenedor de destino.
1. Seleccione el botón **Cargar**.
1. Seleccione el archivo o los archivos que desea cargar.
1. Expanda la sección **Opciones avanzadas** y establezca el **Nivel de acceso** en *Archivo*.
1. Seleccione el botón **Cargar**.

    :::image type="content" source="media/archive-blob/upload-blobs-archive-portal.png" alt-text="Captura de pantalla que muestra cómo cargar blobs al nivel de acceso de archivo en Azure Portal.":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para archivar un blob o un conjunto de blobs en la carga con PowerShell, llame al comando [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) como se muestra en el ejemplo a continuación. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurepowershell
$rgName = <resource-group>
$storageAccount = <storage-account>
$containerName = <container>

# Get context object
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Create new container.
New-AzStorageContainer -Name $containerName -Context $ctx

# Upload a single file named blob1.txt to the Archive tier.
Set-AzStorageBlobContent -Container $containerName `
    -File "blob1.txt" `
    -Blob "blob1.txt" `
    -Context $ctx `
    -StandardBlobTier Archive

# Upload the contents of a sample-blobs directory to the Archive tier, recursively.
Get-ChildItem -Path "C:\sample-blobs" -File -Recurse | 
    Set-AzStorageBlobContent -Container $containerName `
        -Context $ctx `
        -StandardBlobTier Archive
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para archivar un blob único en la carga con CLI de Azure, llame al comando [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) como se muestra en el ejemplo a continuación. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Archive \
    --auth-mode login
```

Para archivar un conjunto de blobs en la carga con CLI de Azure, llame al comando [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) como se muestra en el ejemplo a continuación. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="archive-an-existing-blob"></a>Archivar un blob existente

Puede mover un blob existente al nivel de acceso de archivo de una de estas dos maneras:

- Puede cambiar el nivel de acceso de un blob con la operación [Establecer nivel del blob](/rest/api/storageservices/set-blob-tier). **Establecer nivel del blob** mueve un blob único de un nivel a otro.

    Tenga en cuenta que, al mover un blob al nivel de acceso de archivo con **Establecer nivel de blob**,no podrá leer ni modificar los datos del blob hasta que rehidrate el blob. Si es posible que tenga que leer o modificar los datos del blob antes de que haya transcurrido el intervalo de eliminación temprana, considere la posibilidad de usar una operación de **Copia de blob** para crear una copia del blob en el nivel de acceso de archivo.

- Puede copiar un blob en un nivel con conexión en el nivel de acceso de archivo utilizando la operación [Copia de blob](/rest/api/storageservices/copy-blob). Puede utilizar la operación **Copia de blob** para copiar un blob desde un nivel con conexión (frecuente o esporádico) al nivel de acceso de archivo. El blob de origen permanece en el nivel con conexión y usted puede seguir leyendo o modificando sus datos en el nivel con conexión.

### <a name="archive-an-existing-blob-by-changing-its-tier"></a>Archivar un blob existente cambiando su nivel

Utilice la operación **Establecer nivel de blob** para mover un blob desde el nivel esporádico o frecuente al nivel de acceso de archivo. La operación **Establecer nivel de blob** es mejor para escenarios en los que no será necesario acceder a los datos archivados antes de que haya transcurrido el intervalo de eliminación temprana.

La operación **Establecer nivel de blob** cambia el nivel de un único blob. Para mover un conjunto de blobs al nivel de acceso de archivo con un rendimiento óptimo, Microsoft recomienda realizar una operación de archivo masivo. La operación de archivo masivo envía un lote de comandos **Establecer nivel de blob** al servicio en una única transacción. Para obtener más información, consulte[Archivo masivo](#bulk-archive).  

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para mover un blob existente al nivel de acceso de archivo en Azure Portal, siga estos pasos:

1. Vaya al contenedor del blob.
1. Seleccione el blob que desea archivar.
1. Seleccione el botón **Cambiar nivel.**
1. Seleccione *Archivo* en la lista desplegable **Nivel de acceso**.
1. Seleccione **Guardar**.

    :::image type="content" source="media/archive-blob/set-blob-tier-archive-portal.png" alt-text="Captura de pantalla que muestra cómo establecer el nivel de un blob en Archivo en Azure Portal":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para cambiar el nivel de un blob de frecuente o esporádico a archivo con PowerShell, use la propiedad **BlobClient** del blob para devolver una referencia de .NET al blob y, luego, llame al método **SetAccessTier** en esa referencia. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurepowershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$containerName = "<container>"
$blobName = "<blob>"

# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName $rgName `
        -Name $accountName).Context

# Change the blob's access tier to Archive.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Archive", $null)
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para cambiar el nivel de un blob de frecuente o esporádico a archivo con la CLI de Azure, llame al comando [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier). No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Archive \
    --auth-mode login
```

---

### <a name="archive-an-existing-blob-with-a-copy-operation"></a>Archivar un blob existente con una operación de copia

Utilice la operación [Copia de blob](/rest/api/storageservices/copy-blob)para copiar un blob desde el nivel esporádico o frecuente al nivel de acceso de archivo. El blob de origen permanece en el nivel de acceso frecuente o esporádico, mientras que el blob de destino se crea en el nivel de acceso de archivo.

La operación **Copia de blob** es mejor para escenarios en los que puede tener que leer o modificar los datos archivados antes de que haya transcurrido el intervalo de eliminación temprana. Puede acceder a los datos del blob de origen sin necesidad de rehidratar el blob archivado.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

N/D

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para copiar un blob desde un nivel con conexión al nivel de acceso de archivo con PowerShell, llame al comando [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) y especifique el nivel de acceso de archivo. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

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
    -StandardBlobTier Archive `
    -Context $ctx
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para copiar un blob desde un nivel con conexión al nivel de acceso de archivo con la CLI de Azure, llame al comando [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) y especifique el nivel de acceso de archivo. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier Archive \
    --auth-mode login
```

---

## <a name="bulk-archive"></a>Archivo masivo

Cuando tenga que mover un gran número de blobs al nivel de acceso archivo, use una operación por lotes para obtener un rendimiento óptimo. Una operación por lotes envía varias llamadas API al servicio con una única solicitud. Las operaciones secundarias admitidas por la operación [Procesar bobs por lotes](/rest/api/storageservices/blob-batch) incluyen [Eliminar blob](/rest/api/storageservices/delete-blob) y [Establecer nivel del blob.](/rest/api/storageservices/set-blob-tier)

Para archivar blobs con una operación por lotes, use una de las bibliotecas Azure Storage cliente. En el ejemplo de código siguiente se muestra cómo realizar una operación por lotes básica con la biblioteca cliente de .NET:

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/AccessTiers.cs" id="Snippet_BulkArchiveContainerContents":::

Para obtener una aplicación de ejemplo detallada que muestra cómo cambiar los niveles con una operación por lotes, consulte [AzBulkSetBlobTier](/samples/azure/azbulksetblobtier/azbulksetblobtier/).

## <a name="see-also"></a>Vea también

- [Niveles de acceso frecuente, esporádico y de archivo de los datos de blob](access-tiers-overview.md)
- [Rehidratación de blobs desde el nivel de acceso de archivo](archive-rehydrate-overview.md)
- [Rehidratación de un blob archivado en un nivel en línea](archive-rehydrate-to-online-tier.md)
