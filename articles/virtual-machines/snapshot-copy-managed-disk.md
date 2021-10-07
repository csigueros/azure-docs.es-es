---
title: Creación de una instantánea de Azure de un disco duro virtual
description: Aprenda a crear una copia de una máquina virtual de Azure para usarla como copia de seguridad o para solucionar problemas mediante el portal o PowerShell o la CLI.
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: b2dadea22326f8b4bf2af6a55d90e392feea8c7a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624773"
---
# <a name="create-a-snapshot-of-a-virtual-hard-disk"></a>Creación de una instantánea de un disco duro virtual

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles

Una instantánea es una copia completa de solo lectura de un disco duro virtual (VHD). Puede usar una instantánea como copia de seguridad de un momento determinado o para ayudar a solucionar problemas de máquina virtual. Puede tomar una instantánea del sistema operativo o de los discos duros virtuales.

## <a name="create-a-snapshot-of-a-vhd"></a>Creación de una instantánea de un VHD

Si desea usar una instantánea para crear una máquina virtual, asegúrese de apagarla primero correctamente. Esta acción borra los procesos que están en curso.

# <a name="portal"></a>[Portal](#tab/portal)

Para crear una instantánea mediante Azure Portal, siga estos pasos.

1. En [Azure Portal](https://portal.azure.com), haga clic en **Crear un recurso**.
1. Busque y seleccione **Snapshot**.
1. En la ventana **Instantánea**, seleccione **Crear**. Aparece la ventana **Crear instantánea**.
1. En **Grupo de recursos**, seleccione un [grupo de recursos](/azure/azure-resource-manager/management/overview#resource-groups) existente o escriba el nombre de uno nuevo.
1. Escriba un **nombre** y, a continuación, seleccione una **región** y un **tipo de instantánea** para la nueva instantánea. Si desea almacenar la instantánea en un almacenamiento resistente a zonas, debe seleccionar una región que admita [zonas de disponibilidad](/azure/availability-zones/az-overview). Para obtener una lista de las regiones compatibles, consulte [Regiones de Azure con Availability Zones](/azure/availability-zones/az-region#azure-regions-with-availability-zones).
1. En **Suscripción de origen**, seleccione la suscripción que contiene el disco administrado del que se va a realizar una copia de seguridad.
1. Como **disco de origen**, seleccione el disco administrado para la instantánea.
1. En **Tipo de almacenamiento**, seleccione **HDD estándar**, a menos que necesite almacenamiento con redundancia de zona o almacenamiento de alto rendimiento para la instantánea.
1. Si es necesario, configure los valores en las pestañas **Cifrado**, **Redes** y **Etiquetas**. De lo contrario, se usará la configuración predeterminada para la instantánea.
1. Seleccione **Revisar + crear**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

En este ejemplo, se requiere el uso de [Cloud Shell](https://shell.azure.com/bash) o tener instalada la [CLI de Azure](/cli/azure/).

Siga estos pasos para tomar una instantánea con los cmdlets `New-AzSnapshotConfig` y `New-AzSnapshot`. En este ejemplo se supone que tiene una máquina virtual denominada *myVM* en el grupo de recursos *myResourceGroup*. El ejemplo de código proporcionado crea una instantánea en el mismo grupo de recursos y en la misma región que la máquina virtual de origen.

En primer lugar, usará el cmdlet [New-AzSnapshotConfig](/powershell/module/az.compute/new-azsnapshotconfig) para crear un objeto de instantánea configurable. Después, puede usar el cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot) para realizar una instantánea del disco.

1. Establezca los parámetros necesarios. Actualice los valores para reflejar su entorno.

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

1. Use el cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) para obtener la máquina virtual que contiene el disco duro virtual que desea copiar.

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

1. Cree la configuración de la instantánea. En el ejemplo, es la instantánea del disco del sistema operativo. De forma predeterminada, la instantánea usa almacenamiento estándar con redundancia local. Se recomienda almacenar las instantáneas en almacenamiento estándar en lugar de premium independientemente del tipo de almacenamiento del disco primario o de destino. Las instantáneas Premium suponen un costo adicional.

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```

   Si desea almacenar la instantánea en un almacenamiento resistente a zonas, debe crearla en una región que admita [zonas de disponibilidad](/azure/availability-zones/az-overview e incluir el parámetro `-SkuName Standard_ZRS`. Para obtener una lista de regiones que admiten zonas de disponibilidad, consulte [Regiones de Azure con Availability Zones](/azure/availability-zones/az-region#azure-regions-with-availability-zones).

1. Tome la instantánea.

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```

1. Use el cmdlet [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot) para comprobar que la instantánea existe.

    ```azurepowershell-interactive
    Get-AzSnapshot `
        -ResourceGroupName $resourceGroupName
    ```

# <a name="azure-cli"></a>[CLI de Azure](#tab/cli)

En este ejemplo, se requiere el uso de [Cloud Shell](https://shell.azure.com/bash) o tener instalada la [CLI de Azure](/cli/azure/).

Siga estos pasos para tomar una instantánea con el comando `az snapshot create` y el parámetro `--source-disk`. En este ejemplo se supone que tiene una máquina virtual denominada *myVM* en el grupo de recursos *myResourceGroup*. El ejemplo de código proporcionado crea una instantánea en el mismo grupo de recursos y en la misma región que la máquina virtual de origen.

1. Obtenga el identificador del disco con [az vm show](/cli/azure/vm#az_vm_show).

    ```azurecli-interactive
    osDiskId=$(az vm show \
       -g myResourceGroup \
       -n myVM \
       --query "storageProfile.osDisk.managedDisk.id" \
       -o tsv)
    ```

1. Realice una instantánea denominada *osDisk-backup* mediante [az snapshot create](/cli/azure/snapshot#az_snapshot_create). En el ejemplo, es la instantánea del disco del sistema operativo. De forma predeterminada, la instantánea usa almacenamiento estándar con redundancia local. Se recomienda almacenar las instantáneas en almacenamiento estándar en lugar de premium independientemente del tipo de almacenamiento del disco primario o de destino. Las instantáneas Premium suponen un costo adicional.

    ```azurecli-interactive
    az snapshot create \
        -g myResourceGroup \
        --source "$osDiskId" \
        --name osDisk-backup
    ```

    Si desea almacenar la instantánea en un almacenamiento resistente a zonas, debe crearla en una región que admita [zonas de disponibilidad ](/azure/availability-zones/az-overview) e incluir el parámetro opcional `--sku Standard_ZRS`. Puede encontrar una lista de [zonas de disponibilidad](/azure/availability-zones/az-region#azure-regions-with-availability-zones) aquí.
    
1. Use [az snapshot list](/cli/azure/snapshot#az_snapshot_list) para comprobar que la instantánea existe.
    
    ```azurecli-interactive
    az snapshot list \
       -g myResourceGroup \
       - table
    ```

---

## <a name="next-steps"></a>Pasos siguientes

Implemente una máquina virtual a partir de una instantánea. Cree un disco administrado a partir de una instantánea y, a continuación, asocie el nuevo disco administrado como disco del sistema operativo.

# <a name="portal"></a>[Portal](#tab/portal)

Para más información, consulte el ejemplo de [Creación de una máquina virtual a partir de un VHD mediante Azure Portal](windows/create-vm-specialized-portal.md).

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para más información, consulte el ejemplo de [Creación de una máquina virtual Windows a partir de un disco especializado mediante PowerShell](windows/create-vm-specialized.md).

# <a name="azure-cli"></a>[CLI de Azure](#tab/cli)

Para más información, consulte el ejemplo de [Creación de una máquina virtual completa de Linux con la CLI de Azure](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-from-snapshot?toc=%2fcli%2fmodule%2ftoc.json).

---
