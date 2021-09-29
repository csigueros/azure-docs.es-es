---
title: Habilitación de los informes de inventario de blobs de Azure Storage
description: Obtenga información general sobre los contenedores, blobs, instantáneas y versiones de blobs dentro de una cuenta de almacenamiento.
services: storage
author: normesta
ms.service: storage
ms.date: 08/16/2021
ms.topic: how-to
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.openlocfilehash: 67bd943028ba321aa4fa3a5acca30e80cfc36a32
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128615574"
---
# <a name="enable-azure-storage-blob-inventory-reports"></a>Habilitación de los informes de inventario de blobs de Azure Storage

La característica de inventario de blobs de Azure Storage proporciona información general de los contenedores, blobs, instantáneas y versiones de blob dentro de una cuenta de almacenamiento. Use el informe de inventario para conocer diversos atributos de blobs y contenedores, como el tamaño total de los datos, la antigüedad, el estado de cifrado, la directiva de inmutabilidad y la suspensión legal, entre otros. El informe proporciona información general de los datos para los requisitos empresariales y de cumplimiento.

Para más información sobre los informes de inventario de blobs, consulte [Inventario de blobs de Azure Storage](blob-inventory.md).

Para habilitar los informes de inventario de blobs, agregue una directiva con una o más reglas a su cuenta de almacenamiento. Agregue, edite o quite una directiva mediante [Azure Portal](https://portal.azure.com/).

## <a name="enable-inventory-reports"></a>Habilitación de informes de inventario

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) para empezar a trabajar.

2. Busque la cuenta de almacenamiento y muestre la información general de la cuenta.

3. En **Administración de datos**, seleccione **Inventario de blobs**.

4. Seleccione **Add your first inventory rule** (Agregar la primera regla de inventario).

   Aparecerá la página **Add a rule** (Agregar una regla).

5. En la página **Add a rule** (Agregar una regla), asigne un nombre a la nueva regla.

6. Elija un contenedor.

7. En **Object type to inventory** (Tipo de objeto a inventario), seleccione si desea crear un informe para blobs o contenedores.

   Si selecciona **Blob**, en **Blob subtype** (Subtipo de blob), elija los tipos de blobs que desea incluir en el informe y si desea incluir versiones e instantáneas de blobs en el informe de inventario.

   > [!NOTE]
   > Las versiones y las instantáneas deben estar habilitadas en la cuenta para guardar una nueva regla con la opción correspondiente habilitada.

8. Seleccione los campos que desea incluir en el informe y el formato de los informes.

9. Elija la frecuencia con la que desea generar informes.

9. Opcionalmente, agregue una coincidencia de prefijo para filtrar blobs en el informe de inventario.

10. Seleccione **Guardar**.

    :::image type="content" source="./media/blob-inventory-how-to/portal-blob-inventory.png" alt-text="Captura de pantalla que muestra cómo agregar una regla de inventario de blobs mediante Azure Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Puede habilitar el hospedaje de sitios web estáticos con el módulo de Azure PowerShell.

1. Abra una ventana de comando de Windows PowerShell.

2. Asegúrese de que tiene el módulo más reciente de Azure PowerShell. Consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-Az-ps).

3. Inicie sesión en la suscripción a Azure con el comando `Connect-AzAccount` y siga las instrucciones de la pantalla.

   ```powershell
   Connect-AzAccount
   ```

4. Si su identidad se asocia a más de una suscripción, establezca su suscripción activa en la suscripción de la cuenta de almacenamiento que hospedará el sitio web estático.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Reemplace el valor de marcador de posición `<subscription-id>` por el identificador de la suscripción.

5. Obtenga el contexto de la cuenta de almacenamiento que define la cuenta de almacenamiento que desea usar.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   - Reemplace el marcador de posición `<resource-group-name>` por el nombre del grupo de recursos.

   - Reemplace el valor de marcador de posición `<storage-account-name>` por el nombre de la cuenta de almacenamiento.

6. Cree reglas de inventario con el comando [New-AzStorageBlobInventoryPolicyRule](/powershell/module/az.storage/new-azstorageblobinventorypolicyrule). Cada regla enumera los campos de informe. Para obtener una lista completa de los campos de informe, consulte [Inventario de blobs de Azure Storage](blob-inventory.md).

   ```powershell
    $containerName = "my-container"

    $rule1 = New-AzStorageBlobInventoryPolicyRule -Name Test1 -Destination $containerName -Disabled -Format Csv -Schedule Daily -PrefixMatch con1,con2 `
                -ContainerSchemaField Name,Metadata,PublicAccess,Last-modified,LeaseStatus,LeaseState,LeaseDuration,HasImmutabilityPolicy,HasLegalHold

    $rule2 = New-AzStorageBlobInventoryPolicyRule -Name test2 -Destination $containerName -Format Parquet -Schedule Weekly  -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Last-Modified,Metadata,LastAccessTime

    $rule3 = New-AzStorageBlobInventoryPolicyRule -Name Test3 -Destination $containerName -Format Parquet -Schedule Weekly -IncludeBlobVersion -IncludeSnapshot -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Creation-Time,Last-Modified,Content-Length,Content-MD5,BlobType,AccessTier,AccessTierChangeTime,Expiry-Time,hdi_isfolder,Owner,Group,Permissions,Acl,Metadata,LastAccessTime

    $rule4 = New-AzStorageBlobInventoryPolicyRule -Name test4 -Destination $containerName -Format Csv -Schedule Weekly -BlobType blockBlob -BlobSchemaField Name,BlobType,Content-Length,Creation-Time

   ```

7. Utilice el comando [Set-AzStorageBlobInventoryPolicy](/powershell/module/az.storage/set-azstorageblobinventorypolicy) para crear una directiva de inventario de blobs. Pase reglas a este comando mediante el parámetro `-Rule`.

   ```powershell
   $policy = Set-AzStorageBlobInventoryPolicy -StorageAccount $storageAccount -Rule $rule1,$rule2,$rule3,$rule4  
   ```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

<a id="cli"></a>

Puede habilitar el hospedaje de sitios web estáticos mediante la [Interfaz de la línea de comandos (CLI) de Azure](/cli/azure/).

1. En primer lugar, abra [Azure Cloud Shell](../../cloud-shell/overview.md) o, si ha [instalado](/cli/azure/install-azure-cli) la CLI de Azure localmente, abra una aplicación de consola de comandos como Windows PowerShell.

2. Si su identidad se asocia a más de una suscripción, establezca su suscripción activa en la suscripción de la cuenta de almacenamiento que hospedará el sitio web estático.

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   Reemplace el valor de marcador de posición `<subscription-id>` por el identificador de la suscripción.

3. Defina las reglas de la directiva en un documento JSON. A continuación se muestra el contenido de un archivo JSON de ejemplo denominado `policy.json`.

    ```json
    {
    "enabled": true,
    "type": "Inventory",
    "rules": [
      {
        "enabled": true,
        "name": "inventoryPolicyRule2",
        "destination": "mycontainer",
        "definition": {
          "filters": {
            "blobTypes": [
              "blockBlob"
            ],
            "prefixMatch": [
              "inventoryprefix1",
              "inventoryprefix2"
            ],
            "includeSnapshots": true,
            "includeBlobVersions": true
          },
          "format": "Csv",
          "schedule": "Daily",
          "objectType": "Blob",
          "schemaFields": [
            "Name",
            "Creation-Time",
            "Last-Modified",
            "Content-Length",
            "Content-MD5",
            "BlobType",
            "AccessTier",
            "AccessTierChangeTime",
            "Snapshot",
            "VersionId",
            "IsCurrentVersion",
            "Metadata"
          ]
        }
      }
     ]
   }

   ```

4. Cree una directiva de inventario de blobs mediante el comando [az storage account blob-inventory-policy create](/cli/azure/storage/account/blob-inventory-policy#az_storage_account_blob_inventory_policy_create). Proporcione el nombre del documento JSON mediante el parámetro `--policy`.

   ```azurecli
   az storage account blob-inventory-policy create -g myresourcegroup --account-name mystorageaccount --policy @policy.json
   ```

---

## <a name="next-steps"></a>Pasos siguientes

- [Cálculo del recuento y el tamaño total de los blobs por contenedor](calculate-blob-count-size.md)
- [Administración del ciclo de vida de Azure Blob Storage](./lifecycle-management-overview.md)
