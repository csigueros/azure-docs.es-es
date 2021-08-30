---
title: Administración de etiquetas de máquinas virtuales en el dispositivo Azure Stack Edge Pro con GPU a través de Azure PowerShell
description: Se describe cómo crear y administrar etiquetas de máquinas virtuales para máquinas virtuales que se ejecutan en un dispositivo Azure Stack Edge mediante Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/12/2021
ms.author: alkohli
ms.openlocfilehash: 605a438e44220bc4eb97cc3718188d0458ea24d7
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113664510"
---
# <a name="tag-vms-on-azure-stack-edge-via-azure-powershell"></a>Etiquetado de máquinas virtuales en Azure Stack Edge mediante Azure PowerShell

En este artículo se describe cómo etiquetar máquinas virtuales (VM) que se ejecutan en los dispositivos Azure Stack Edge Pro con GPU mediante Azure PowerShell. 

## <a name="about-tags"></a>Acerca de las etiquetas

Las etiquetas son pares clave-valor definidos por el usuario que se pueden asignar a un recurso o a un grupo de recursos. Puede aplicar etiquetas a las máquinas virtuales que se ejecutan en el dispositivo para organizarlas lógicamente en una taxonomía. Las etiquetas se pueden colocar en un recurso en el momento de su creación, o bien se pueden agregar a un recurso existente. Por ejemplo, puede aplicar el nombre `Organization` y el valor `Engineering` a todas las máquinas virtuales que usa el Departamento de ingeniería de su organización.

Para más información sobre las etiquetas, consulte [Administración de etiquetas a través de AzureRM PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).

## <a name="prerequisites"></a>Requisitos previos

Antes de poder implementar una máquina virtual en el dispositivo a través de PowerShell, compruebe lo siguiente:

- Tiene acceso al cliente que se usará para conectarse al dispositivo.
    - El cliente ejecuta un [sistema operativo compatible](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
    - El cliente está configurado para conectarse a la instancia local de Azure Resource Manager del dispositivo según las instrucciones que se indican en [Conexión a Azure Resource Manager en el dispositivo](azure-stack-edge-gpu-connect-resource-manager.md).


## <a name="verify-connection-to-local-azure-resource-manager"></a>Comprobación de la conexión a Azure Resource Manager local

[!INCLUDE [azure-stack-edge-gateway-verify-azure-resource-manager-connection](../../includes/azure-stack-edge-gateway-verify-azure-resource-manager-connection.md)]


## <a name="add-a-tag-to-a-vm"></a>Adición de una etiqueta a una máquina virtual

### <a name="az"></a>[Az](#tab/az)

1. Configure algunos parámetros.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myazvm"
    PS C:\WINDOWS\system32> $VMRG = "myaseazrg"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Obtenga el objeto de máquina virtual y sus etiquetas.

   ```powershell
   $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Agregue la etiqueta y actualice la máquina virtual. La actualización de la máquina virtual puede tardar unos minutos.

    Puede usar la marca **-Force** opcional para ejecutar el comando sin la confirmación del usuario.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    ```

    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzResource -ResourceID $VirtualMachine.ID -Tag $tags -Force   

    Name              : myazvm
    ResourceId        : /subscriptions/d64617ad-6266-4b19-45af-81112d213322/resourceGroups/myas
                        eazrg/providers/Microsoft.Compute/virtualMachines/myazvm
    ResourceName      : myazvm
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    SubscriptionId    : d64617ad-6266-4b19-45af-81112d213322
    Tags              : {Organization}
    Properties        : @{vmId=568a264f-c5d3-477f-a16c-4c5549eafa8c; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=; diagnosticsProfile=;
                        provisioningState=Succeeded}
    ```



### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

1. Configure algunos parámetros.

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
    $TagValue = <Tag Value>   
    ```

    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    PS C:\WINDOWS\system32> $TagName = "Organization"
    PS C:\WINDOWS\system32> $TagValue = "Sales"
    ```

2. Obtenga el objeto de máquina virtual y sus etiquetas.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $tags = $VirtualMachine.Tags
   ```

3. Agregue la etiqueta y actualice la máquina virtual. La actualización de la máquina virtual puede tardar unos minutos.

    Puede usar la marca **-Force** opcional para ejecutar el comando sin la confirmación del usuario.

    ```powershell
    $tags.Add($TagName, $TagValue)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    ```

    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Add($TagName, $TagValue)
    PS C:\WINDOWS\system32> Set-AzureRmResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    
    Name              : myasetestvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
                        lMachines/myasetestvm1
    ResourceName      : myasetestvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg2
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {Organization}
    Properties        : @{vmId=958c0baa-e143-4d8a-82bd-9c6b1ba45e86; hardwareProfile=; storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

Para más información, consulte [Add-AzureRMTag](/powershell/module/azurerm.tags/remove-azurermtag?view=azurermps-6.13.0&preserve-view=true).

---

## <a name="view-tags-of-a-vm"></a>Visualización de las etiquetas de una máquina virtual

### <a name="az"></a>[Az](#tab/az)

Puede ver las etiquetas aplicadas a una máquina virtual específica que se ejecuta en el dispositivo. 

1. Defina los parámetros asociados a la máquina virtual cuyas etiquetas desea ver.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myazvm"
    PS C:\WINDOWS\system32> $VMRG = "myaseazrg"
    ```
1. Obtenga el objeto de máquina virtual y sus etiquetas.

   ```powershell
   $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32>  $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName     
    PS C:\WINDOWS\system32> $VirtualMachine.Tags
    
    Key          Value
    ---          -----
    Organization Sales    
    
    PS C:\WINDOWS\system32>
    ```


### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

Puede ver las etiquetas aplicadas a una máquina virtual específica que se ejecuta en el dispositivo. 

1. Defina los parámetros asociados a la máquina virtual cuyas etiquetas desea ver.

   ```powershell
   $VMName = <VM Name>
   $VMRG = <VM Resource Group>
   ```
    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myasetestvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg2"
    ```
1. Obtenga el objeto de máquina virtual y sus etiquetas.

   ```powershell
   $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
   $VirtualMachine.Tags
   ```
    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine

    ResourceGroupName : myaserg2
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg2/providers/Microsoft.Compute/virtua
    lMachines/myasetestvm1
    VmId              : 958c0baa-e143-4d8a-82bd-9c6b1ba45e86
    Name              : myasetestvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Sales"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
---

## <a name="view-tags-for-all-resources"></a>Visualización de las etiquetas de todos los recursos

### <a name="az"></a>[Az](#tab/az)

Para ver la lista actual de etiquetas de todos los recursos de la suscripción de Azure Resource Manager local (diferente de la suscripción de Azure) del dispositivo, use el comando `Get-AzTag`.


Este es un ejemplo de salida cuando se ejecutan varias máquinas virtuales en el dispositivo y desea ver todas las etiquetas en todas las máquinas virtuales.

```output
PS C:\WINDOWS\system32> Get-AzTag

Name         Count
----         -----
Organization 2

PS C:\WINDOWS\system32>
```

La salida anterior indica que hay dos etiquetas `Organization` en las máquinas virtuales que se ejecutan en el dispositivo.

Para ver más detalles, use el parámetro `-Detailed`.

```output
PS C:\WINDOWS\system32> Get-AzTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Sales        1
              Engineering  1
Count       : 2
Values      : {Sales, Engineering}

PS C:\WINDOWS\system32>
```

La salida anterior indica que, de las dos etiquetas, una máquina virtual se etiqueta como `Engineering` y la otra, como perteneciente a `Sales`.

### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

Para ver la lista actual de etiquetas de todos los recursos de la suscripción de Azure Resource Manager local (diferente de la suscripción de Azure) del dispositivo, use el comando `Get-AzureRMTag`.


Este es un ejemplo de salida cuando se ejecutan varias máquinas virtuales en el dispositivo y desea ver todas las etiquetas en todas las máquinas virtuales.

```powershell
PS C:\WINDOWS\system32> Get-AzureRMTag

Name         Count
----         -----
Organization 3

PS C:\WINDOWS\system32>
```

La salida anterior indica que hay tres etiquetas `Organization` en las máquinas virtuales que se ejecutan en el dispositivo.

Para ver más detalles, use el parámetro `-Detailed`.

```output
PS C:\WINDOWS\system32> Get-AzureRMTag -Detailed |fl

Name        : Organization
ValuesTable :
              Name         Count
              ===========  =====
              Engineering  2
              Sales        1

Count       : 3
Values      : {Engineering, Sales}

PS C:\WINDOWS\system32>
```

La salida anterior indica que fuera de las tres etiquetas, dos máquinas virtuales se etiquetan como `Engineering` y una se etiqueta como perteneciente a `Sales`.

---

## <a name="remove-a-tag-from-a-vm"></a>Eliminación de una etiqueta de una máquina virtual

### <a name="az"></a>[Az](#tab/az)

1. Configure algunos parámetros. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Obtenga el objeto de VM.

    ```powershell
    $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzVM -ResourceGroupName $VMRG -Name $VMName
    PS C:\WINDOWS\system32> $VirtualMachine
    
    ResourceGroupName  : myaseazrg
    Id                 : /subscriptions/d64617ad-6266-4b19-45af-81112d213322/resourceGroups/mya
    seazrg/providers/Microsoft.Compute/virtualMachines/myazvm
    VmId               : 568a264f-c5d3-477f-a16c-4c5549eafa8c
    Name               : myazvm
    Type               : Microsoft.Compute/virtualMachines
    Location           : dbelocal
    Tags               : {"Organization":"Sales"}
    DiagnosticsProfile : {BootDiagnostics}
    HardwareProfile    : {VmSize}
    NetworkProfile     : {NetworkInterfaces}
    OSProfile          : {ComputerName, AdminUsername, LinuxConfiguration, Secrets,
    AllowExtensionOperations, RequireGuestProvisionSignal}
    ProvisioningState  : Succeeded
    StorageProfile     : {ImageReference, OsDisk, DataDisks}
    
    PS C:\WINDOWS\system32>
    ```
3. Quite la etiqueta y actualice la máquina virtual. Use la marca `-Force` opcional para ejecutar el comando sin la confirmación del usuario.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    ```
    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $tags = $VirtualMachine.Tags
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzResource -ResourceId $VirtualMachine.Id -Tag $tags -Force
    
    Name              : myazvm
    ResourceId        : /subscriptions/d64617ad-6266-4b19-45af-81112d213322/resourceGroups/myas
                        eazrg/providers/Microsoft.Compute/virtualMachines/myazvm
    ResourceName      : myazvm
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaseazrg
    Location          : dbelocal
    SubscriptionId    : d64617ad-6266-4b19-45af-81112d213322
    Tags              : {}
    Properties        : @{vmId=568a264f-c5d3-477f-a16c-4c5549eafa8c; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=; diagnosticsProfile=;
                        provisioningState=Succeeded}
    
    PS C:\WINDOWS\system32>
    ```

### <a name="azurerm"></a>[AzureRM](#tab/azurerm)

1. Configure algunos parámetros. 

    ```powershell
    $VMName = <VM Name>
    $VMRG = <VM Resource Group>
    $TagName = <Tag Name>
   ``` 
    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VMName = "myaselinuxvm1"
    PS C:\WINDOWS\system32> $VMRG = "myaserg1"
    PS C:\WINDOWS\system32> $TagName = "Organization" 
    ```
2. Obtenga el objeto de VM.

    ```powershell
    $VirtualMachine = Get-AzureRmVM -ResourceGroupName $VMRG -Name $VMName
    $VirtualMachine   
    ```   

    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $VirtualMachine = Get-AzureRMVM -ResourceGroupName $VMRG -Name $VMName
    ResourceGroupName : myaserg1
    Id                : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGroups/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselinuxvm1
    VmId              : 290b3fdd-0c99-4905-9ea1-cf93cd6f25ee
    Name              : myaselinuxvm1
    Type              : Microsoft.Compute/virtualMachines
    Location          : dbelocal
    Tags              : {"Organization":"Engineering"}
    HardwareProfile   : {VmSize}
    NetworkProfile    : {NetworkInterfaces}
    OSProfile         : {ComputerName, AdminUsername, LinuxConfiguration, Secrets}
    ProvisioningState : Succeeded
    StorageProfile    : {ImageReference, OsDisk, DataDisks}
                                                                              PS C:\WINDOWS\system32> 
    ```
3. Quite la etiqueta y actualice la máquina virtual. Use la marca `-Force` opcional para ejecutar el comando sin la confirmación del usuario.

    ```powershell
    $tags = $VirtualMachine.Tags
    $tags.Remove($TagName)
    Set-AzureRmResource -ResourceId $VirtualMachine.Id -Tag $tags [-Force]
    ```
    A continuación, se incluye una salida de ejemplo:

    ```output
    PS C:\WINDOWS\system32> $tags = $Virtualmachine.Tags
    PS C:\WINDOWS\system32> $tags
    Key          Value
    ---          -----
    Organization Engineering
    PS C:\WINDOWS\system32> $tags.Remove($TagName)
    True
    PS C:\WINDOWS\system32> Set-AzureRMResource -ResourceID $VirtualMachine.ID -Tag $tags -Force
    Name              : myaselinuxvm1
    ResourceId        : /subscriptions/992601bc-b03d-4d72-598e-d24eac232122/resourceGrou
                        ps/myaserg1/providers/Microsoft.Compute/virtualMachines/myaselin
                        uxvm1
    ResourceName      : myaselinuxvm1
    ResourceType      : Microsoft.Compute/virtualMachines
    ResourceGroupName : myaserg1
    Location          : dbelocal
    SubscriptionId    : 992601bc-b03d-4d72-598e-d24eac232122
    Tags              : {}
    Properties        : @{vmId=290b3fdd-0c99-4905-9ea1-cf93cd6f25ee; hardwareProfile=;
                        storageProfile=; osProfile=; networkProfile=;
                        provisioningState=Succeeded}
    PS C:\WINDOWS\system32>
    ```
---

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre el [Etiquetado de una máquina virtual en Azure mediante los cmdlets az en PowerShell](../virtual-machines/tag-powershell.md).
- Obtenga información sobre cómo [Administrar etiquetas mediante los cmdlets de AzureRM en PowerShell](/powershell/module/azurerm.tags/?view=azurermps-6.13.0&preserve-view=true).
