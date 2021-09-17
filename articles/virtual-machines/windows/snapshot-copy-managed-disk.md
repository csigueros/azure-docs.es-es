---
title: Creación de una instantánea de una unidad de disco duro virtual mediante el portal o PowerShell
description: Aprenda a crear una copia de una máquina virtual de Azure como copia de seguridad o para solucionar problemas mediante el portal o PowerShell.
author: roygara
ms.author: rogarana
manager: twooley
ms.service: storage
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 10/08/2018
ms.openlocfilehash: 4a4f3ec63b3998dfb6b8030599af100397a49629
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693802"
---
# <a name="create-a-snapshot-using-the-portal-or-powershell"></a>Creación de una instantánea mediante el portal o PowerShell

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles 

Una instantánea es una copia completa de solo lectura de un disco duro virtual (VHD). Tome una instantánea de un disco duro virtual de datos o del sistema operativo para realizar una copia de seguridad o para solucionar problemas de la máquina virtual.

Si va a usar la instantánea para crear una máquina virtual, se recomienda un cierre limpio de la máquina virtual antes de tomar una instantánea para así limpiar cualquier proceso que esté en curso.

## <a name="use-the-azure-portal"></a>Uso de Azure Portal 

Para crear una instantánea, complete los siguientes pasos: 
1.  En [Azure Portal](https://portal.azure.com), seleccione **Crear un recurso**.
2. Busque y seleccione **Snapshot**.
3. En la ventana **Instantánea**, seleccione **Crear**. Aparece la ventana **Crear instantánea**.
4. Escriba un **nombre** para la instantánea.
5. Seleccione un [Grupo de recursos](../../azure-resource-manager/management/overview.md#resource-groups) existente o escriba el nombre de uno nuevo. 
6. Seleccione una **ubicación** del centro de datos de Azure.  
7. Como **disco de origen**, seleccione el disco administrado para la instantánea.
8. Seleccione el **tipo de cuenta** que se usará para almacenar la instantánea. Use **Standard_HDD**, a menos que necesite que la instantánea se almacene en un disco de alto rendimiento.
9. Seleccione **Crear**.

## <a name="use-powershell"></a>Uso de PowerShell

En los pasos siguientes se muestra cómo copiar el disco duro virtual y crear la configuración de la instantánea. Después, puede tomar una instantánea del disco mediante el cmdlet [New-AzSnapshot](/powershell/module/az.compute/new-azsnapshot). 

 

1. Configure algunos parámetros: 

   ```azurepowershell-interactive
   $resourceGroupName = 'myResourceGroup' 
   $location = 'eastus' 
   $vmName = 'myVM'
   $snapshotName = 'mySnapshot'  
   ```

2. Obtenga la máquina virtual:

   ```azurepowershell-interactive
   $vm = Get-AzVM `
       -ResourceGroupName $resourceGroupName `
       -Name $vmName
   ```

3. Cree la configuración de la instantánea. En este ejemplo, es la instantánea del disco del sistema operativo:

   ```azurepowershell-interactive
   $snapshot =  New-AzSnapshotConfig `
       -SourceUri $vm.StorageProfile.OsDisk.ManagedDisk.Id `
       -Location $location `
       -CreateOption copy
   ```
   
   > [!NOTE]
   > Si desea almacenar la instantánea en un almacenamiento resistente a zonas, debe crearla en una región que admita [zonas de disponibilidad ](../../availability-zones/az-overview.md) e incluir el parámetro `-SkuName Standard_ZRS`.   
   
4. Tome la instantánea:

   ```azurepowershell-interactive
   New-AzSnapshot `
       -Snapshot $snapshot `
       -SnapshotName $snapshotName `
       -ResourceGroupName $resourceGroupName 
   ```


## <a name="next-steps"></a>Pasos siguientes

Cree una máquina virtual a partir de una instantánea; para ello, cree primero un disco administrado con la instantánea y conéctelo como disco del sistema operativo. Para más información, consulte el ejemplo de [Creación de una máquina virtual a partir de una instantánea con PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-snapshot).