---
title: Establecimiento del nivel de acceso de un blob
titleSuffix: Azure Storage
description: Aprenda a especificar el nivel de acceso de un blob al cargarlo o a cambiar el nivel de acceso de un blob existente.
author: tamram
ms.author: tamram
ms.date: 10/25/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7cd01ff89ffdf01676a9a7c3ef5d41ac1bf138a6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441502"
---
# <a name="set-a-blobs-access-tier"></a>Establecimiento del nivel de acceso de un blob

Puede establecer el nivel de acceso de un blob de cualquiera de las maneras siguientes:

- Al establecer el nivel de acceso en línea predeterminado (frecuente o esporádico) para la cuenta de almacenamiento. Los blobs de la cuenta heredan este nivel de acceso a menos que invalide explícitamente la configuración de un blob individual.
- Al establecer explícitamente el nivel de un blob en la carga. Puede crear un blob en el nivel de acceso frecuente, esporádico o nivel de archivo.
- Al cambiar el nivel de un blob existente con una operación Establecer nivel de blob, normalmente para pasar de un nivel más frecuente a uno más esporádico.
- Al copiar un blob con una operación Copia de blobs, normalmente para pasar de un nivel más esporádico a uno más frecuente.

En este artículo se describe cómo administrar un blob en un nivel de acceso en línea (frecuente o esporádico). Para obtener más información sobre cómo mover un blob al nivel de archivo, consulte [Archivado de un blob](archive-blob.md). Para más información sobre cómo rehidratar un blob desde el nivel de archivo, consulte [Rehidratación de un blob archivado a un nivel en línea](archive-rehydrate-to-online-tier.md).

Para más información acerca de los niveles de acceso para blobs, consulte [Niveles de acceso frecuente, esporádico y de archivo para los datos de blobs](access-tiers-overview.md).

## <a name="set-the-default-access-tier-for-a-storage-account"></a>Establecimiento del nivel de acceso predeterminado de una cuenta de almacenamiento

La configuración predeterminada del nivel de acceso para una cuenta de almacenamiento de uso general v2 determina en qué nivel en línea se crea un nuevo blob de forma predeterminada. Puede establecer el nivel de acceso predeterminado para una cuenta de almacenamiento de uso general v2 en el momento de crear la cuenta o actualizando la configuración de una cuenta existente.

Al cambiar la configuración predeterminada del nivel de acceso para una cuenta de almacenamiento de uso general v2 existente, el cambio se aplica a todos los blobs de la cuenta para la que no se ha establecido explícitamente un nivel de acceso. Cambiar el nivel de acceso predeterminado puede tener un impacto en la facturación. Para obtener más información, consulte [Configuración del nivel de acceso predeterminado de la cuenta](access-tiers-overview.md#default-account-access-tier-setting).

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para establecer el nivel de acceso predeterminado para una cuenta de almacenamiento en el momento de la creación en Azure Portal, siga estos pasos:

1. Vaya a la página **Cuentas de almacenamiento** y seleccione el botón **Crear**.
1. Rellene la pestaña **Datos básicos**.
1. En la pestaña **Opciones avanzadas**, en **Blob Storage**, establezca el **nivel de acceso** en *frecuente* o *esporádico*. La configuración predeterminada es *frecuente*.
1. Seleccione **Revisar y crear** para validar la configuración y crear la cuenta de almacenamiento.

    :::image type="content" source="media/access-tiers-online-manage/set-default-access-tier-create-portal.png" alt-text="Captura de pantalla que muestra cómo establecer el nivel de acceso predeterminado al crear una cuenta de almacenamiento.":::

Para actualizar el nivel de acceso predeterminado para una cuenta de almacenamiento existente en Azure Portal, siga estos pasos:

1. Vaya a la cuenta de almacenamiento en Azure Portal.
1. En **Configuración**, seleccione **Configuración**.
1. Busque la configuración **Nivel de acceso a blobs (valor predeterminado)** y seleccione *frecuente* o *esporádico*. El valor predeterminado es *frecuente* si no ha configurado previamente esta propiedad.
1. Guarde los cambios.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para cambiar la configuración predeterminada del nivel de acceso para una cuenta de almacenamiento con PowerShell, llame al comando [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) y especifique el nuevo nivel de acceso predeterminado.

```azurepowershell
$rgName = <resource-group>
$accountName = <storage-account>

# Change the storage account tier to Cool
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Cool
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para cambiar la configuración predeterminada del nivel de acceso para una cuenta de almacenamiento con PowerShell, llame al comando [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) y especifique el nuevo nivel de acceso predeterminado.

```azurecli
# Change the storage account tier to Cool
az storage account update \
    --resource-group <resource-group> \
    --name <storage-account> \
    --access-tier Cool
```

---

## <a name="set-a-blobs-tier-on-upload"></a>Establecimiento del nivel de un blob en la carga

Al cargar un blob en Azure Storage, tiene dos opciones para establecer el nivel del blob en la carga:

- Puede especificar explícitamente el nivel en el que se creará el blob. Esta configuración invalida el nivel de acceso predeterminado para la cuenta de almacenamiento. Puede establecer el nivel de un blob o un conjunto de blobs al cargarlos en frecuente, esporádico o de archivo.
- Puede cargar un blob sin especificar un nivel. En este caso, el blob se creará en el nivel de acceso predeterminado especificado para la cuenta de almacenamiento (ya sea de frecuente o esporádico).

En las secciones siguientes se describe cómo especificar que un blob se cargue en el nivel frecuente o esporádico. Para más información sobre cómo archivar un blob al cargarlo, consulte [Archive blobs on upload](archive-blob.md#archive-blobs-on-upload) (Archivado de blobs al cargar).

### <a name="upload-a-blob-to-a-specific-online-tier"></a>Cargar un blob a un nivel en línea específico

Para crear un blob en el nivel frecuente o esporádico, especifique el nivel al crear el blob. El nivel de acceso especificado en la carga invalida el nivel de acceso predeterminado para la cuenta de almacenamiento.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para cargar un blob o un conjunto de blobs en un nivel específico desde Azure Portal, siga estos pasos:

1. Vaya al contenedor de destino.
1. Seleccione el botón **Cargar**.
1. Seleccione el archivo o los archivos que desea cargar.
1. Expanda la sección **Opciones avanzadas** y establezca el **nivel de acceso** en *frecuente* o *esporádico*.
1. Seleccione el botón **Cargar**.

    :::image type="content" source="media/access-tiers-online-manage/upload-blob-to-online-tier-portal.png" alt-text="Captura de pantalla que muestra cómo cargar blobs en un nivel en línea en Azure Portal.":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para cargar un blob o un conjunto de blobs en un nivel específico con PowerShell, llame al comando [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) como se muestra en el ejemplo a continuación. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurepowershell
$rgName = <resource-group>
$storageAccount = <storage-account>
$containerName = <container>

# Get context object
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Create new container.
New-AzStorageContainer -Name $containerName -Context $ctx

# Upload a single file named blob1.txt to the Cool tier.
Set-AzStorageBlobContent -Container $containerName `
    -File "blob1.txt" `
    -Blob "blob1.txt" `
    -Context $ctx `
    -StandardBlobTier Cool

# Upload the contents of a sample-blobs directory to the Cool tier, recursively.
Get-ChildItem -Path "C:\sample-blobs" -File -Recurse | 
    Set-AzStorageBlobContent -Container $containerName `
        -Context $ctx `
        -StandardBlobTier Cool
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para cargar un blob en un nivel específico con CLI de Azure, llame al comando [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) como se muestra en el ejemplo a continuación. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --file <file> \
    --tier Cool \
    --auth-mode login
```

Para cargar un conjunto de blobs en un nivel específico con CLI de Azure, llame al comando [az storage blob upload-batch](/cli/azure/storage/blob#az_storage_blob_upload_batch) como se muestra en el ejemplo a continuación. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli
az storage blob upload-batch \
    --destination <container> \
    --source <source-directory> \
    --account-name <storage-account> \
    --tier Cool \
    --auth-mode login
```

---

### <a name="upload-a-blob-to-the-default-tier"></a>Cargar un blob al nivel predeterminado

Las cuentas de almacenamiento tienen una configuración predeterminada del nivel de acceso que indica el nivel en línea en el que se crea un nuevo blob. La configuración predeterminada del nivel de acceso se puede establecer en frecuente o esporádico. El comportamiento de esta configuración es ligeramente diferente en función del tipo de cuenta de almacenamiento:

- El nivel de acceso predeterminado de una nueva cuenta de almacenamiento v2 de uso general se establece en el nivel de acceso frecuente de manera predeterminada. Puede cambiar la configuración predeterminada del nivel de acceso al crear una cuenta de almacenamiento o después de crearla.
- Al crear una cuenta heredada de Blob Storage, debe especificar la configuración de nivel de acceso predeterminada como frecuente o esporádico al crear una cuenta de almacenamiento. Puede cambiar la configuración predeterminada del nivel de acceso para una cuenta de almacenamiento después de crearla.

Un blob que no tiene un nivel asignado explícitamente infiere el nivel de la configuración predeterminada del nivel de acceso de la cuenta. Puede determinar si el nivel de acceso de un blob se deduce mediante Azure Portal, PowerShell o CLI de Azure.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Si el nivel de acceso de un blob se infiere de la configuración predeterminada del nivel de acceso de la cuenta, Azure Portal muestra el nivel de acceso como **Frecuente (inferido)** o **Esporádico (inferido)** .

:::image type="content" source="media/access-tiers-online-manage/default-access-tier-portal.png" alt-text="Captura de pantalla que muestra blobs con el nivel de acceso predeterminado en Azure Portal.":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para determinar el nivel de acceso de un blob y si se deduce de Azure PowerShell, recupere el blob y, a continuación, compruebe sus propiedades **AccessTier** y **AccessTierInferred**.

```azurepowershell
$rgName = "<resource-group>"
$storageAccount = "<storage-account>"
$containerName = "<container>"
$blobName = "<blob>"

# Get the storage account context.
$ctx = New-AzStorageContext -StorageAccountName $storageAccount -UseConnectedAccount

# Get the blob from the service.
$blob = Get-AzStorageBlob -Context $ctx -Container $containerName -Blob $blobName

# Check the AccessTier and AccessTierInferred properties.
# If the access tier is inferred, that property returns true.
$blob.BlobProperties.AccessTier
$blob.BlobProperties.AccessTierInferred
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para determinar el nivel de acceso de un blob y si se deduce de CLI de Azure, recupere el blob y, a continuación, compruebe sus propiedades **blobTier** y **blobTierInferred**.

```azurecli
az storage blob show \
    --container-name <container> \
    --name <blob> \
    --account-name <storage-account> \
    --query '[properties.blobTier, properties.blobTierInferred]' \
    --output tsv \
    --auth-mode login 
```

---

## <a name="move-a-blob-to-a-different-online-tier"></a>Traslado de un blob a un nivel en línea diferente

Puede cambiar el nivel de un blob existente de una de estas dos maneras:

- Mediante una llamada a la operación [Establecer nivel de blob](/rest/api/storageservices/set-blob-tier), ya sea directamente o mediante una directiva de [administración del ciclo de vida,](access-tiers-overview.md#blob-lifecycle-management) para cambiar el nivel del blob.
- Mediante una llamada a la operación [Copy Blob](/rest/api/storageservices/copy-blob) para copiar un blob de un nivel a otro. En este caso, el blob de origen permanece en el nivel original y se crea un nuevo blob en el nivel de destino.

Para obtener más información sobre cada una de estas opciones, consulte [Configuración o cambio del nivel de un blob](access-tiers-overview.md#setting-or-changing-a-blobs-tier).

Use PowerShell, CLI de Azure o una de las bibliotecas cliente de Azure Storage para mover un blob a un nivel diferente.

### <a name="change-a-blobs-tier"></a>Cambio del nivel de un blob

Cuando se cambia el nivel de un blob, se mueve ese blob y todos sus datos al nivel de destino. Llamar a [Establecer nivel de blob](/rest/api/storageservices/set-blob-tier) suele ser la mejor opción para cambiar el nivel de un blob de un nivel más frecuente a uno más esporádico.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para cambiar el nivel de un blob de frecuente o esporádico en Azure Portal, siga estos pasos:

1. Vaya al blob cuyo nivel desea cambiar.
1. Seleccione el blob y, a continuación, seleccione el botón **Cambiar**.
1. En el cuadro de diálogo **Cambiar nivel**, seleccione el nivel de destino.
1. Seleccione el botón **Guardar**.

    :::image type="content" source="media/access-tiers-online-manage/change-blob-tier-portal.png" alt-text="Captura de pantalla que muestra cómo cambiar el nivel de un blob en Azure Portal":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para cambiar el nivel de un blob frecuente a esporádico con PowerShell, use la propiedad **BlobClient** del blob para devolver una referencia de .NET al blob y, luego, llame al método **SetAccessTier** en esa referencia. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

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

# Change the blob's access tier to Cool.
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx
$blob.BlobClient.SetAccessTier("Cool", $null, "Standard")
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para cambiar el nivel de un blob de frecuente a esporádico con la CLI de Azure, llame al comando [az storage blob set-tier](/cli/azure/storage/blob#az_storage_blob_set_tier). No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurecli
az storage blob set-tier \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --tier Cool \
    --auth-mode login
```

---

### <a name="copy-a-blob-to-a-different-online-tier"></a>Copia de un blob a un nivel en línea diferente

Cuando se copia un blob a un nivel diferente, se mueve ese blob y todos sus datos al nivel de destino. Se recomienda llamar a [Copy Blob](/rest/api/storageservices/copy-blob) en la mayoría de los escenarios en los que se va a mover un blob de esporádico a frecuente, o se va a rehidratar un blob desde el nivel de archivo.

# <a name="portal"></a>[Portal](#tab/azure-portal)

N/D

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para copiar un blob de esporádico a frecuente con PowerShell, llame al comando [Start-AzStorageBlobCopy](/powershell/module/az.storage/start-azstorageblobcopy) y especifique el nivel de destino. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

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
    -Context $ctx
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para copiar un blob de esporádico a frecuente con CLI de Azure, llame al comando [az storage blob copy start](/cli/azure/storage/blob/copy#az_storage_blob_copy_start) y especifique el nivel de destino. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurecli
az storage blob copy start \
    --source-container <source-container> \
    --source-blob <source-blob> \
    --destination-container <dest-container> \
    --destination-blob <dest-blob> \
    --account-name <storage-account> \
    --tier hot \
    --auth-mode login
```

---

## <a name="next-steps"></a>Pasos siguientes

- [Administración del nivel de acceso predeterminado en una cuenta de Azure Storage](../common/manage-account-default-access-tier.md)
- [Rehidratación de un blob archivado en un nivel en línea](archive-rehydrate-to-online-tier.md)
