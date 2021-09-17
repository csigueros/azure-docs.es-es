---
title: Creación de una instantánea incremental
description: Conozca las instantáneas incrementales para discos administrados y cómo crearlas mediante Azure Portal, el módulo de Azure PowerShell y Azure Resource Manager.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/10/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8f00a0f69bf00c42ef120250cbc4a770fb1b7a09
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689421"
---
# <a name="create-an-incremental-snapshot-for-managed-disks"></a>Creación de una instantánea incremental para discos administrados

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](../../includes/virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>Restricciones

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](../../includes/virtual-machines-disks-incremental-snapshots-restrictions.md)]

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Puede usar la CLI de Azure para crear una instantánea incremental. Debe instalar la versión más reciente de la CLI de Azure. Consulte los artículos siguientes para obtener información sobre cómo [instalar](/cli/azure/install-azure-cli) o [actualizar](/cli/azure/update-azure-cli) la CLI de Azure.

El siguiente script creará una instantánea incremental de un disco determinado:

```azurecli
# Declare variables
diskName="yourDiskNameHere"
resourceGroupName="yourResourceGroupNameHere"
snapshotName="desiredSnapshotNameHere"

# Get the disk you need to backup
yourDiskID=$(az disk show -n $diskName -g $resourceGroupName --query "id" --output tsv)

# Create the snapshot
az snapshot create -g $resourceGroupName -n $snapshotName --source $yourDiskID --incremental true
```

Puede identificar las instantáneas incrementales del mismo disco con la propiedad `SourceResourceId` de las instantáneas. `SourceResourceId` es el identificador de recursos de Azure Resource Manager del disco principal.

Puede usar `SourceResourceId` para crear una lista de todas las instantáneas asociadas a un disco determinado. Reemplace `yourResourceGroupNameHere` por su valor y, a continuación, puede usar el ejemplo siguiente para enumerar las instantáneas incrementales existentes:


```azurecli
# Declare variables and create snapshot list
subscriptionId="yourSubscriptionId"
resourceGroupName="yourResourceGroupNameHere"
diskName="yourDiskNameHere"

az account set --subscription $subscriptionId

diskId=$(az disk show -n $diskName -g $resourceGroupName --query [id] -o tsv)

az snapshot list --query "[?creationData.sourceResourceId=='$diskId' && incremental]" -g $resourceGroupName --output table
```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Puede usar el módulo Azure PowerShell para crear una instantánea incremental. Necesitará la versión más reciente del módulo Azure PowerShell. El siguiente comando la instalará o actualizará la instalación existente a la versión más reciente:

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Una vez instalada, inicie sesión en PowerShell con `Connect-AzAccount`.

Para crear una instantánea incremental con Azure PowerShell, establezca la configuración con [New-AzSnapShotConfig](/powershell/module/az.compute/new-azsnapshotconfig) con el parámetro `-Incremental` y, después, páselo como una variable a [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) con el parámetro `-Snapshot`.

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

Puede identificar las instantáneas incrementales desde el mismo disco con las propiedades `SourceResourceId` y `SourceUniqueId` de las instantáneas. `SourceResourceId` es el identificador de recursos de Azure Resource Manager del disco principal. `SourceUniqueId` es el valor heredado de la propiedad `UniqueId` del disco. Si fuera a eliminar un disco y después creara otro con el mismo nombre, el valor de la propiedad `UniqueId` cambiaría.

Puede usar `SourceResourceId` y `SourceUniqueId` para crear una lista de todas las instantáneas asociadas a un disco determinado. Reemplace `yourResourceGroupNameHere` por su valor y, a continuación, puede usar el ejemplo siguiente para enumerar las instantáneas incrementales existentes:

```PowerShell
$resourceGroupName = "yourResourceGroupNameHere"
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

# <a name="portal"></a>[Portal](#tab/azure-portal)
[!INCLUDE [virtual-machines-disks-incremental-snapshots-portal](../../includes/virtual-machines-disks-incremental-snapshots-portal.md)]

# <a name="resource-manager-template"></a>[Plantilla de Resource Manager](#tab/azure-resource-manager)

También puede usar plantillas de Azure Resource Manager para crear una instantánea incremental. Deberá asegurarse de que apiVersion esté establecido en **2019-03-01** y que la propiedad incremental también esté establecida en true. El siguiente fragmento de código es un ejemplo de cómo crear una instantánea incremental con las plantillas de Resource Manager:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```
---

## <a name="next-steps"></a>Pasos siguientes

Si quiere ver un código de ejemplo que muestre la funcionalidad diferencial de las instantáneas incrementales con .NET, consulte [Copia de las copias de seguridad de Azure Managed Disks en otra región con una funcionalidad diferencial de instantáneas incrementales](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
