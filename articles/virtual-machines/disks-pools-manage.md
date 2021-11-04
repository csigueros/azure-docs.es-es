---
title: Administración de un grupo de discos de Azure (versión preliminar)
description: Aprenda a agregar discos administrados a un grupo de discos de Azure o a deshabilitar la compatibilidad con iSCSI en un disco.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: ignite-fall-2021
ms.openlocfilehash: 59f03b2484bed39a3c562efc6cfb4176b5173964
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083246"
---
# <a name="manage-an-azure-disk-pool-preview"></a>Administración de un grupo de discos de Azure (versión preliminar)

En este artículo se explica cómo agregar un disco administrado a un grupo de discos de Azure (versión preliminar) y cómo deshabilitar la compatibilidad con iSCSI en un disco que se ha agregado a un grupo de discos.

## <a name="add-a-disk-to-a-pool"></a>Adición de un disco a un grupo

Para poder agregar un disco a un grupo de discos, debe cumplir los siguientes requisitos:
- Debe ser un SSD prémium, un SSD estándar o un disco Ultra Disk en la misma región y zona de disponibilidad que el grupo de discos.
    - Los discos Ultra deben tener un tamaño de sector de disco de 512 bytes.
- Debe ser un disco compartido con un valor de maxShares de dos, o más.
- Debe [proporcionar los permisos RBAC del proveedor de recursos StoragePool a los discos que se agregarán al grupo de discos](disks-pools-deploy.md#assign-storagepool-resource-provider-permissions).

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Vaya al grupo de discos y seleccione **Discos** en **Configuración**.
1. Seleccione **Attach existing disk** (Conectar disco existente) y seleccione los discos.
1. Cuando haya elegido todos los discos que desea conectar, seleccione **Guardar**.

    :::image type="content" source="media/disk-pools-manage/manage-disk-pool-add.png" alt-text="Captura de pantalla de la hoja Discos del grupo de discos.":::

    Ahora que ha conectado el disco, debe habilitar sus LUN.

1. En **Configuración**, seleccione **iSCSI**.
1. Seleccione **Add LUN** (Agregar LUN) en **Disks enabled for iSCSI** (Discos habilitados para iSCSI).
1. Seleccione el disco que conectó anteriormente.
1. Seleccione **Guardar**.

    :::image type="content" source="media/disk-pools-manage/enable-disk-luns.png" alt-text="Captura de pantalla de la hoja iSCSI, los LUN de disco agregados y habilitados.":::

Ahora que ha conectado el disco y habilitado el LUN, debe crearlo y asociarlo como un almacén de datos iSCSI a la nube privada de Azure VMware Solution. Consulte [Conexión del LUN iSCSI](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun) para obtener detalles.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

### <a name="prerequisites"></a>Requisitos previos

Instale la [versión 6.1.0 o posterior](/powershell/module/az.diskpool/?view=azps-6.1.0&preserve-view=true) del módulo de Azure Powershell.

Instale el módulo del grupo de discos con el siguiente comando:

```azurepowershell
Install-Module -Name Az.DiskPool -RequiredVersion 0.3.0 -Repository PSGallery
```

### <a name="add-a-disk-pool"></a>Adición de un grupo de discos

El siguiente script agrega un disco adicional al grupo de discos y lo expone a través de iSCSI. Mantiene los discos existentes en el grupo de discos sin ningún cambio.

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<yourDiskName>" #Provide the name of the disk you want to add
$lunName ='<LunName>' #Provide the Lun name of the added disk
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

Ahora que ha conectado el disco y habilitado el LUN, debe crearlo y asociarlo como un almacén de datos iSCSI a la nube privada de Azure VMware Solution. Consulte [Conexión del LUN iSCSI](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun) para obtener detalles.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

### <a name="prerequisites"></a>Prerrequisitos

Instale [la versión más reciente](/cli/azure/disk-pool) de la CLI de Azure.

Si aún no lo ha hecho, instale la extensión del grupo de discos con el siguiente comando:

```azurecli
az extension add -n diskpool
```

### <a name="add-a-disk-pool---cli"></a>Adición de un grupo de discos: CLI

El siguiente script agrega un disco adicional al grupo de discos y lo expone a través de iSCSI. Mantiene los discos existentes en el grupo de discos sin ningún cambio.

```azurecli
# Add a disk to a disk pool

# Initialize parameters
resourceGroupName="<yourResourceGroupName>"
diskPoolName="<yourDiskPoolName>"
iscsiTargetName="<youriSCSITargetName>"
diskName="<yourDiskName>"
lunName="<LunName>"

diskPoolUpdateArgs=("$@")
diskPoolUpdateArgs+=(--resource-group $resourceGroupName --Name $diskPoolName)

diskIds=$(echo $(az disk-pool show --name $diskPoolName --resource-group $resourceGroupName --query disks[].id -o json) | sed -e 's/\[ //g' -e 's/\ ]//g' -e 's/\,//g')
for disk in $diskIds; do
    diskPoolUpdateArgs+=(--disks $(echo $disk | sed 's/"//g'))
done

diskId=$(az disk show --resource-group $resourceGroupName --name $diskName --query id | sed 's/"//g')
diskPoolUpdateArgs+=(--disks $diskId)

az disk-pool update "${diskPoolUpdateArgs[@]}"

# Get existing iSCSI LUNs and expose added disk as a new LUN
targetUpdateArgs=("$@")
targetUpdateArgs+=(--resource-group $resourceGroupName --disk-pool-name $diskPoolName --name $iscsiTargetName)

luns=$(az disk-pool iscsi-target show --name $iscsiTargetName --disk-pool-name $diskPoolName --resource-group $resourceGroupName --query luns)
lunsCounts=$(echo $luns | jq length)

for (( i=0; i < $lunCounts; i++ )); do
    tmpLunName=$(echo $luns | jq .[$i].name | sed 's/"//g')
    tmpLunId=$(echo $luns | jq .[$i].managedDiskAzureResourceId | sed 's/"//g')
    targetUpdateArgs+=(--luns name=$tmpLunName managed-disk-azure-resource-id=$tmpLunId)
done

targetUpdateArgs+=(--luns name=$lunName managed-disk-azure-resource-id=$diskId)

az disk-pool iscsi-target update "${targetUpdateArgs[@]}"
```

Ahora que ha conectado el disco y habilitado el LUN, debe crearlo y asociarlo como un almacén de datos iSCSI a la nube privada de Azure VMware Solution. Consulte [Conexión del LUN iSCSI](../azure-vmware/attach-disk-pools-to-azure-vmware-solution-hosts.md#attach-the-iscsi-lun) para obtener detalles.

---

## <a name="disable-iscsi-on-a-disk-and-remove-it-from-the-pool"></a>Deshabilitación de iSCSI en un disco y eliminación de este del grupo

Para poder deshabilitar la compatibilidad con iSCSI en un disco, confirme que no hay conexiones iSCSI pendientes con el LUN iSCSI en el que se expone el disco. Cuando se quita un disco del grupo de discos, no se elimina automáticamente. Así se evita la pérdida de datos, pero se le seguirá facturando por almacenar los datos. Si no necesita los datos almacenados en un disco, puede eliminarlo manualmente. De esta forma se eliminarán no solo el disco, sino también todos los datos almacenados en él y se evitará cargos adicionales.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Vaya al grupo de discos y seleccione **iSCSI** en **Configuración**.
1. En **Disks enabled for iSCSI** (Discos habilitados para iSCSI), seleccione los discos que desea quitar y seleccione **Remove LUN** (Quitar LUN).
1. Haga clic en **Guardar** y espere a que finalice la operación.

    :::image type="content" source="media/disk-pools-manage/remove-disk-lun.png" alt-text="Captura de pantalla de la hoja iSCSI del grupo de discos, quitando los LUN de disco.":::

    Ahora que ha deshabilitado el LUN, puede quitar los discos del grupo de discos.

1. Seleccione **Discos** en **Configuración**.
1. Seleccione **Remove disk from disk pool** (Quitar disco del grupo de discos) y seleccione los discos.
1. Seleccione **Guardar**.

Cuando se complete la operación, el disco se habrá quitado completamente del grupo de discos.

:::image type="content" source="media/disk-pools-manage/remove-disks-from-pool.png" alt-text="Captura de pantalla de la hoja del disco del grupo de discos. Discos que se quitan del grupo.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
#Initialize input parameters
$resourceGroupName ="<yourResourceGroupName>"
$diskPoolName = "<yourDiskPoolName>"
$iscsiTargetName = "<youriSCSITargetName>"
$diskName ="<NameOfDiskYouWantToRemove>" #Provide the name of the disk you want to remove
$lunName ='<LunForDiskYouWantToRemove>' #Provide the Lun name of the disk you want to remove
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

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
# Disable iSCSI on a disk and remove it from the pool

# Initialize parameters
resourceGroupName="<yourResourceGroupName>"
diskPoolName="<yourDiskPoolName>"
iscsiTargetName="<youriSCSITargetName>"
diskName="<yourDiskName>"
lunName="<LunName>"

# Get existing iSCSI LUNs and remove it from iSCSI target
targetUpdateArgs=("$@")
targetUpdateArgs+=(--resource-group $resourceGroupName --disk-pool-name $diskPoolName --name $iscsiTargetName)

luns=$(az disk-pool iscsi-target show --name $iscsiTargetName --disk-pool-name $diskPoolName --resource-group $resourceGroupName --query luns)
lunCounts=$(echo $luns | jq length)

for (( i=0; i < $lunCounts; i++ )); do
    tmpLunName=$(echo $luns | jq .[$i].name | sed 's/"//g')
    if [ $tmpLunName != $lunName ]; then
        tmpLunId=$(echo $luns | jq .[$i].managedDiskAzureResourceId | sed 's/"//g')
        targetUpdateArgs+=(--luns name=$tmpLunName managed-disk-azure-resource-id=$tmpLunId)
    fi
done

az disk-pool iscsi-target update "${targetUpdateArgs[@]}"

# Remove disk from pool
diskId=$(az disk show --resource-group $resourceGroupName --name $diskName -- query id | sed 's/"//g')

diskPoolUpdateArgs=("$@")
diskPoolUpdateArgs+=(--resource-group $resourceGroupName --name $diskPoolName)

diskIds=$(az disk-pool show --name $diskPoolName --resource-group $resourceGroupName --query disks[].id -o json)
diskLength=$(echo diskIds | jq length)

for (( i=0; i < $diskLength; i++ )); do
    tmpDiskId=$(echo $diskIds | jq .[$i] | sed 's/"//g')

    if [ $tmpDiskId != $diskId ]; then
        diskPoolUpdateArgs+=(--disks $tmpDiskId)
    fi
done

az disk-pool update "${diskPoolUpdateArgs[@]}"
```

---
## <a name="next-steps"></a>Pasos siguientes

- Para aprender a mover un grupo de discos a otra suscripción, consulte [Traslado de un grupo de discos a otra suscripción.](disks-pools-move-resource.md)
- Para aprender a desaprovisionar un grupo de discos, consulte [Desaprovisionamiento de un grupo de discos de Azure](disks-pools-deprovision.md).
