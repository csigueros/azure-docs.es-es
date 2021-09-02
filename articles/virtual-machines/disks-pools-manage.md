---
title: Administración de un grupo de discos de Azure (versión preliminar)
description: Aprenda a agregar discos administrados a un grupo de discos de Azure o a deshabilitar la compatibilidad con iSCSI en un disco.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4811894a3483bbfce1724b744d904fa328c51ded
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114436993"
---
# <a name="manage-an-azure-disk-pool-preview"></a>Administración de un grupo de discos de Azure (versión preliminar)

En este artículo se explica cómo agregar un disco administrado a un grupo de discos de Azure (versión preliminar) y cómo deshabilitar la compatibilidad con iSCSI en un disco que se ha agregado a un grupo de discos.

## <a name="prerequisites"></a>Requisitos previos

Instale la [versión 6.1.0 o posterior](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true) del módulo de Azure Powershell.

## <a name="add-a-disk-to-a-pool"></a>Adición de un disco a un grupo

Para poder agregar un disco a un grupo de discos, debe cumplir los siguientes requisitos:
- Debe ser un disco SSD prémium o Ultra, y estar en la misma región y zona de disponibilidad que el grupo de discos.
    - Los discos Ultra deben tener un tamaño de sector de disco de 512 bytes.
- Debe ser un disco compartido con un valor de maxShares de dos, o más.
- Debe [proporcionar los permisos RBAC del proveedor de recursos StoragePool a los discos que se agregarán al grupo de discos](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions).

El siguiente script agrega un disco adicional al grupo de discos y lo expone a través de iSCSI. Mantiene los discos existentes en el grupo de discos sin ningún cambio.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='LunName>' #Provide the Lun name of the added disk
$diskIds = @()

#Add the disk to disk pool
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
$diskIds += ($Id)
}

$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$diskIds += ,($disk.Id)
Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds

#Get the existing iSCSI LUNs and add the new disk
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}

$newlun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $disk.Id -Name $lunName
$luns += ,($newlun)
Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns
```

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>Deshabilitación de iSCSI en un disco y eliminación de este del grupo

Para poder deshabilitar la compatibilidad con iSCSI en un disco, confirme que no hay conexiones iSCSI pendientes con el LUN iSCSI en el que se expone el disco. Cuando se quita un disco del grupo de discos, no se elimina automáticamente. Así se evita la pérdida de datos, pero se le seguirá facturando por almacenar los datos. Si no necesita los datos almacenados en un disco, puede eliminarlo manualmente. De esta forma se eliminarán no solo el disco, sino también todos los datos almacenados en él y se evitará cargos adicionales.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LUNForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
$diskIds = @()

#Get the existing iSCSI LUNs and remove it from iSCS target
$target = Get-AzDiskPoolIscsiTarget -name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName
$existingLuns = $target.Lun
$luns = @()
foreach ($lun in $existingLuns)
{
if ($lun.Name -notlike $lunName)
{
$tmpLunName = $lun.Name
$tmpId = $lun.ManagedDiskAzureResourceId
$tmplun = New-AzDiskPoolIscsiLunObject -ManagedDiskAzureResourceId $tmpId -Name $tmpLunName
$luns += ,($tmplun)
}
}

Update-AzDiskPoolIscsiTarget -Name $iscsiTargetName -DiskPoolName $diskPoolName -ResourceGroupName $resourceGroupName -Lun $luns

#Remove the disk from disk pool
$disk = Get-AzDisk -ResourceGroupName $resourceGroupName -DiskName $diskName
$DiskPool = Get-AzDiskPool -Name $diskPoolName -ResourceGroupName $resourceGroupName
$DiskPoolDiskIDs = $DiskPool.Disk.Id
foreach ($Id in $DiskPoolDiskIDs)
{
if ($Id -notlike $disk.Id)
{
$diskIds += ($Id)
}
}

Update-AzDiskPool -ResourceGroupName $resourceGroupName -Name $diskPoolName -DiskId $diskIds
```

## <a name="next-steps"></a>Pasos siguientes

- Para aprender a mover un grupo de discos a otra suscripción, consulte [Traslado de un grupo de discos a otra suscripción.](disks-pools-move-resource.md)
- Para aprender a desaprovisionar un grupo de discos, consulte [Desaprovisionamiento de un grupo de discos de Azure](disks-pools-deprovision.md).