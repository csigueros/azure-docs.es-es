---
title: Cambio de tamaño de una máquina virtual
description: Cambie el tamaño que se usa para una máquina virtual de Azure.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 09/13/2021
ms.author: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 80387608b8c8ab7069ed989078472102e3d9afe8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128699832"
---
# <a name="change-the-size-of-a-virtual-machine"></a>Cambio del tamaño de una máquina virtual 

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles 

En este artículo se muestra cómo mover una máquina virtual a otro [tamaño de máquina virtual](sizes.md).

Después de crear una máquina virtual, puede escalarla o reducirla verticalmente cambiando su tamaño. En algunos casos, hay que desasignarla antes. Esto puede suceder si el nuevo tamaño no está disponible en el clúster de hardware que hospeda la actualmente la máquina virtual.

Si la máquina virtual usa Premium Storage, asegúrese de elegir una versión **s** del tamaño para obtener compatibilidad con este nivel de almacenamiento. Por ejemplo, elija Standard_E4 **s** _v3 en lugar de Standard_E4_v3.

## <a name="change-the-vm-size"></a>Cambio del tamaño de la máquina virtual

### <a name="portal"></a>[Portal](#tab/portal)

1. Abra [Azure Portal](https://portal.azure.com).
1. Abra la página de la máquina virtual.
1. En el menú de la izquierda, seleccione **Tamaño**.
1. Elija un tamaño nuevo en la lista de tamaños disponibles y, después, seleccione **Cambiar tamaño**.


Si la máquina virtual está en ejecución, el cambio de tamaño hará que se reinicie. 

Si la máquina virtual todavía está en ejecución y no puede ver el tamaño que desea en la lista, detenga la máquina para ver más tamaños.

### <a name="cli"></a>[CLI](#tab/cli)

Para cambiar el tamaño de una máquina virtual, necesita tener instalada la última versión de la [CLI de Azure](/cli/azure/install-az-cli2) e iniciar sesión en una cuenta de Azure con [az login](/cli/azure/reference-index).

1. Consulte la lista de tamaños de VM disponibles en el clúster de hardware que hospeda la VM con [az vm list-vm-resize-options](/cli/azure/vm). En el ejemplo siguiente se enumeran los tamaños para la VM denominada `myVM` en la región del grupo de recursos `myResourceGroup`:
   
    ```azurecli-interactive
    az vm list-vm-resize-options \
    --resource-group myResourceGroup \
    --name myVM --output table
    ```

2. Si aparece el tamaño de máquina virtual deseado, cambie el tamaño de la máquina virtual con [az vm resize](/cli/azure/vm). En el ejemplo siguiente se cambia el tamaño de la VM denominada `myVM` por el tamaño `Standard_DS3_v2`:
   
    ```azurecli-interactive
    az vm resize \
    --resource-group myResourceGroup \
    --name myVM \
    --size Standard_DS3_v2
    ```
   
    La VM se reinicia durante este proceso. Tras reiniciar, se reasignan los discos del SO y de datos. Se pierde todo lo que haya en el disco temporal.

3. Si no aparece el tamaño de VM deseado, debe desasignar primero la VM con [az vm deallocate](/cli/azure/vm). Este proceso permite cambiar el tamaño de la VM por otro tamaño disponible que la región admite y, a continuación, se inicia. Los siguientes pasos permiten desasignar, cambiar de tamaño y luego iniciar la VM denominada `myVM` en el grupo de recursos llamado `myResourceGroup`:
   
    ```azurecli-interactive
    # Variables will make this easier. Replace the values with your own.
    resourceGroup=myResourceGroup
    vm=myVM
    size=Standard_DS3_v2

    az vm deallocate \
    --resource-group $resourceGroup \
    --name myVM
    az vm resize \
    --resource-group $resourceGroup \
    --name $vm \
    --size $size
    az vm start \
    --resource-group $resourceGroup \
    --name $vm
    ```
   
   > [!WARNING]
   > Al desasignar la máquina virtual, también se liberan todas las direcciones IP dinámicas asignadas a ella. Esto no afecta a los discos del SO y de datos.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

**Use PowerShell para cambiar el tamaño de una VM que no está en un conjunto de disponibilidad.**

Establezca algunas variables. Reemplace los valores por su propia información.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Muestre los tamaños de máquina virtual que están disponibles en la región donde se hospeda la máquina virtual. 
   
```azurepowershell-interactive
Get-AzVMSize -ResourceGroupName $resourceGroup -VMName $vmName 
```

Si se muestra el tamaño deseado, ejecute los comandos siguientes para cambiar el tamaño de la máquina virtual. Si el tamaño deseado no aparece, vaya al paso 3.
   
```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMsize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

Si el tamaño deseado no se muestra, ejecute los siguientes comandos para desasignar la máquina virtual, cambiar su tamaño y reiniciarla. Sustituya **\<newVMsize>** por el tamaño que desee.
   
```azurepowershell-interactive
Stop-AzVM -ResourceGroupName $resourceGroup -Name $vmName -Force
$vm = Get-AzVM -ResourceGroupName $resourceGroup -VMName $vmName
$vm.HardwareProfile.VmSize = "<newVMSize>"
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
Start-AzVM -ResourceGroupName $resourceGroup -Name $vmName
```

> [!WARNING]
> Al desasignar la máquina virtual, se liberan todas las direcciones IP dinámicas asignadas a ella. Esto no afecta a los discos del SO y de datos. 
> 
> 

**Uso de PowerShell para cambiar el tamaño de una máquina virtual en un conjunto de disponibilidad**

Si el nuevo tamaño de una máquina virtual de un conjunto de disponibilidad no está disponible en el clúster de hardware que hospeda la máquina virtual, habrá que desasignar todas las máquinas virtuales del conjunto de disponibilidad para cambiar el tamaño de la máquina virtual. También tendrá que actualizar el tamaño de otras máquinas virtuales del conjunto de disponibilidad después de cambiar el tamaño de una máquina virtual. Para cambiar el tamaño de una máquina virtual de un conjunto de disponibilidad, siga estos pasos.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$vmName = "myVM"
```

Muestre los tamaños de máquina virtual que están disponibles en el clúster de hardware donde se hospeda la máquina virtual. 
   
```azurepowershell-interactive
Get-AzVMSize `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
```

Si se muestra el tamaño deseado, ejecute el comando siguiente para cambiar el tamaño de la máquina virtual. Si no aparece, vaya a la sección siguiente.
   
```azurepowershell-interactive
$vm = Get-AzVM `
-ResourceGroupName $resourceGroup `
-VMName $vmName 
$vm.HardwareProfile.VmSize = "<newVmSize>"
Update-AzVM `
-VM $vm `
-ResourceGroupName $resourceGroup
```
    
Si el tamaño deseado no aparece, continúe con los pasos siguientes para desasignar todas las máquinas virtuales del conjunto de disponibilidad, cambiar su tamaño y reiniciarlas.

Detenga todas las máquinas virtuales del conjunto de disponibilidad.
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$as = Get-AzAvailabilitySet `
-ResourceGroupName $resourceGroup `
-Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines |  Stop-AzVM -Force -NoWait  
```

Cambie el tamaño de todas las máquinas virtuales del conjunto de disponibilidad y reinícielas.
   
```azurepowershell-interactive
$availabilitySetName = "<availabilitySetName>"
$newSize = "<newVmSize>"
$as = Get-AzAvailabilitySet -ResourceGroupName $resourceGroup -Name $availabilitySetName
$virtualMachines = $as.VirtualMachinesReferences |  Get-AzResource | Get-AzVM
$virtualMachines | Foreach-Object { $_.HardwareProfile.VmSize = $newSize }
$virtualMachines | Update-AzVM
$virtualMachines | Start-AzVM
```

---

## <a name="next-steps"></a>Pasos siguientes

Para obtener una mayor escalabilidad, ejecute varias instancias de VM y escálelas horizontalmente. Para más información, vea [Escalado automático de máquinas en un conjunto de escalado de máquinas virtuales](../virtual-machine-scale-sets/tutorial-autoscale-powershell.md).
