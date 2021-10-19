---
title: 'Tutorial: Administración de discos de Azure con Azure PowerShell'
description: En este tutorial, aprenderá a usar Azure PowerShell para crear y administrar discos de Azure para máquinas virtuales
author: roygara
ms.author: rogarana
ms.service: storage
ms.subservice: disks
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.date: 10/08/2021
ms.custom: template-tutorial, devx-track-azurepowershell
ms.openlocfilehash: 52c5f10b0a41ec2362af09c972f29ed079ae70d7
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808095"
---
# <a name="tutorial-manage-disks-with-azure-powershell"></a>Tutorial: Administración de discos con Azure PowerShell

Las máquinas virtuales de Azure usan discos para almacenar sistemas operativos, aplicaciones y datos. Cuando se crea una máquina virtual es importante elegir un tamaño de disco y una configuración adecuados para la carga de trabajo esperada.

En este tutorial se trata la implementación y administración de discos de máquina virtual. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear, asociar e inicializar un disco de datos
> * Comprobar el estado de un disco
> * Inicializar un disco
> * Expandir y actualizar un disco
> * Desasociar y eliminar un disco

## <a name="prerequisites"></a>Requisitos previos

Debe disponer de una cuenta de Azure con una suscripción activa. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-vm"></a>Crear una VM

Los ejercicios de este tutorial requieren una máquina virtual. Siga los pasos descritos en esta sección para crear una.

Antes de comenzar, busque la variable `$azRegion` ubicada en la primera línea del código de ejemplo y actualice el valor para que refleje la región que desee. Por ejemplo, para especificar la región **Centro de EE. UU.** , use `$azRegion = "Central US"`. A continuación, use el código para implementar una máquina virtual dentro de un nuevo grupo de recursos. Se le pedirán los valores de nombre de usuario y contraseña de la cuenta de administrador local de la máquina virtual.

```azurepowershell-interactive
$azRegion = "[Your Region]"
$azResourceGroup = "myDemoResourceGroup"
$azVMName = "myDemoVM"
$azDataDiskName = "myDemoDataDisk"

New-AzVm `
    -Location $azRegion `
    -ResourceGroupName $azResourceGroup `
    -Name $azVMName `
    -Size "Standard_D2s_v3" `
    -VirtualNetworkName "myDemoVnet" `
    -SubnetName "myDemoSubnet" `
    -SecurityGroupName "myDemoNetworkSecurityGroup" `
    -PublicIpAddressName "myDemoPublicIpAddress"
```

La salida confirma la creación correcta de la máquina virtual.

```Output
ResourceGroupName        : myDemoResourceGroup
Id                       : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoTestVM
VmId                     : [{GUID}]
Name                     : myDemoVM
Type                     : Microsoft.Compute/virtualMachines
Location                 : centralus
Tags                     : {}
HardwareProfile          : {VmSize}
NetworkProfile           : {NetworkInterfaces}
OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, AllowExtensionOperations, RequireGuestProvisionSignal}
ProvisioningState        : Succeeded
StorageProfile           : {ImageReference, OsDisk, DataDisks}
FullyQualifiedDomainName : mydemovm-abc123.Central US.cloudapp.azure.com
```

La máquina virtual se aprovisiona, y se crean y asocian automáticamente dos discos.

- Un **disco de sistema operativo**, que hospeda el sistema operativo de la máquina virtual.
- Un **disco temporal**, que se usa principalmente para operaciones como el procesamiento de datos temporales.

## <a name="add-a-data-disk"></a>Agregar un disco de datos

Se recomienda separar los datos de aplicación y usuario de los datos relacionados con el sistema operativo siempre que sea posible. Si necesita almacenar datos de usuario o aplicación en la máquina virtual, normalmente creará y asociará discos de datos adicionales.

Siga los pasos de esta sección para crear, asociar e inicializar un disco de datos en la máquina virtual.

### <a name="create-the-data-disk"></a>Creación del disco de datos

Esta sección le guía a través de la creación de un disco de datos.

1. Para poder crear un disco de datos, primero debe crear un objeto de disco. En el ejemplo de código siguiente se usa el cmdlet [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) para configurar un objeto de disco.

    ```azurepowershell-interactive
    $diskConfig = New-AzDiskConfig `
        -Location $azRegion `
        -CreateOption Empty `
        -DiskSizeGB 128 `
        -SkuName "Standard_LRS"
    ```

1. Una vez creado el objeto de disco, use el cmdlet [New-AzDisk](/powershell/module/az.compute/new-azdisk) para aprovisionar un disco de datos.

    ```azurepowershell-interactive
    $dataDisk = New-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName `
        -Disk $diskConfig
    ```

    Puede usar el cmdlet [Get-AzDisk](/powershell/module/az.compute/get-azdisk) para comprobar que se ha creado el disco.

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $azDataDiskName
    ```

    En este ejemplo, la salida confirma que se creó el disco. Los valores de propiedad `DiskState` y `ManagedBy` confirman que el disco aún no está asociado.

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    :
    ManagedByExtended            : {}
    OsType                       :
    DiskSizeGB                   : 128
    DiskSizeBytes                : 137438953472
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Unattached
    Name                         : myDemoDataDisk
    ```

### <a name="attach-the-data-disk"></a>Conectar el disco de datos

Se debe asociar un disco de datos a una máquina virtual para que esta pueda acceder a él. Realice los pasos de esta sección para crear una referencia para la máquina virtual, conecte el disco y actualice la configuración de la máquina virtual.

1. Obtenga la máquina virtual a la que asociará el disco de datos. En el código de ejemplo siguiente se usa el cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) para crear una referencia a la máquina virtual.

    ```azurepowershell-interactive
    $vm = Get-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

1. A continuación, asocie el disco de datos a la configuración de máquina virtual con el cmdlet [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).

    ```azurepowershell-interactive
    $vm = Add-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName `
        -CreateOption Attach `
        -ManagedDiskId $dataDisk.Id `
        -Lun 1
    ```

1. Por último, actualice la configuración de la máquina virtual con el cmdlet [Update-AzVM](/powershell/module/az.compute/add-azvmdatadisk).

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    Después de una breve pausa, la salida confirma que la asociación se ha realizado correctamente.

    ```Output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

### <a name="initialize-the-data-disk"></a>Inicialización del disco de datos

Después de asociar un disco de datos a la máquina virtual, el sistema operativo debe configurarse para que use el disco. En la sección siguiente se proporcionan instrucciones sobre cómo conectarse a la máquina virtual remota y configurar el primer disco agregado.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Busque la máquina virtual a la que ha asociado el disco de datos. Cree una conexión del protocolo de escritorio remoto (RDP) e inicie sesión como administrador local.

1. Después de establecer una conexión RDP a la máquina virtual remota, seleccione el menú **Inicio** de Windows. Escriba **PowerShell** en el cuadro de búsqueda y seleccione **Windows PowerShell** para abrir una ventana de PowerShell.

    [![Imagen de una ventana de conexión de un escritorio remoto.](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. En la ventana abierta de PowerShell, ejecute el siguiente script.

    ```powershell
    Get-Disk | Where PartitionStyle -eq 'raw' |
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -AssignDriveLetter -UseMaximumSize |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDemoDataDisk" -Confirm:$false
    ```

    La salida confirma una inicialización correcta.

    ```Output
    DriveLetter FileSystemLabel FileSystem DriveType HealthStatus OperationalStatus SizeRemaining   Size
    ----------- --------------- ---------- --------- ------------ ----------------- -------------   ----
    F           myDemoDataDisk  NTFS       Fixed     Healthy      OK                    127.89 GB 128 GB
    ```

## <a name="expand-a-disk"></a>Expansión de un disco

Puede expandir discos de Azure para proporcionar capacidad de almacenamiento adicional cuando la máquina virtual tenga poco espacio disponible en disco.

Algunos escenarios requieren que los datos se almacenen en el disco del sistema operativo. Por ejemplo, es posible que deba admitir aplicaciones heredadas que instalen componentes en la unidad del sistema operativo. También puede tener la necesidad de migrar un equipo físico local o una máquina virtual con una unidad de sistema operativo más grande. En tales casos, puede que sea necesario expandir el disco del sistema operativo de una máquina virtual.

No se admite la reducción de un disco existente, y puede provocar una pérdida de datos.

### <a name="update-the-disks-size"></a>Actualización del tamaño del disco

Siga los pasos que se indican a continuación para cambiar el tamaño del disco del sistema operativo o de un disco de datos.

1. Seleccione la máquina virtual que contiene el disco que va a cambiar de tamaño con el cmdlet `Get-AzVM`.

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. Para poder cambiar el tamaño del disco de una máquina virtual, primero debe detenerla. Use el cmdlet `Stop-AzVM` para detener la máquina virtual. Se le pedirá confirmación.

    > [!IMPORTANT]
    > Antes de iniciar el apagado de una máquina virtual, confirme siempre que no hay recursos o datos importantes que se puedan perder.

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Después de una breve pausa, la salida confirma que la máquina se ha detenido correctamente.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. Una vez detenida la máquina virtual, obtenga una referencia al disco de sistema operativo o de datos asociado a la máquina virtual con el cmdlet `Get-AzDisk`.

    En el ejemplo siguiente se selecciona el disco del sistema operativo de la máquina virtual.

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    En el ejemplo siguiente se selecciona el disco de datos de la máquina virtual.

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. Ahora que tiene una referencia al disco, establezca el tamaño del disco en 250 GiB.

    > [!IMPORTANT]
    > El nuevo tamaño debe ser mayor que el tamaño de disco existente. El máximo permitido es 4095 GiB para los discos del sistema operativo.

    ```azurepowershell-interactive
    $disk.DiskSizeGB = 250
    ```

1. A continuación, actualice la imagen del disco con el cmdlet `Update-AzDisk`.

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    La imagen del disco se actualiza y la salida confirma el nuevo tamaño del disco.

    ```Output
    ResourceGroupName            : myDemoResourceGroup
    ManagedBy                    : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/virtualMachines/myDemoVM
    Sku                          : Microsoft.Azure.Management.Compute.Models.DiskSku
    TimeCreated                  : 9/135/2021 6:41:10 PM
    CreationData                 : Microsoft.Azure.Management.Compute.Models.CreationData
    DiskSizeGB                   : 250
    DiskSizeBytes                : 268435456000
    UniqueId                     : {GUID}
    ProvisioningState            : Succeeded
    DiskIOPSReadWrite            : 500
    DiskMBpsReadWrite            : 60
    DiskState                    : Reserved
    Encryption                   : Microsoft.Azure.Management.Compute.Models.Encryption
    Id                           : /subscriptions/{GUID}/resourceGroups/myDemoResourceGroup/providers/Microsoft.Compute/disks/myDemoDataDisk
    Name                         : myDemoDataDisk
    Type                         : Microsoft.Compute/disks
    Location                     : centralus

1. Finally, restart the VM with the `Start-AzVM` cmdlet.

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Después de una breve pausa, la salida confirma que la máquina se ha iniciado correctamente.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

### <a name="expand-the-disk-volume-in-the-os"></a>Expansión del volumen del disco en el sistema operativo

Para poder aprovechar el nuevo tamaño de disco, debe expandir el volumen dentro del sistema operativo. Siga los pasos que se indican a continuación para expandir el volumen de disco y aprovechar las ventajas del nuevo tamaño de disco.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Busque la máquina virtual a la que ha asociado el disco de datos. Cree una conexión del protocolo de escritorio remoto (RDP) e inicie sesión. Si ya no tiene acceso a una cuenta administrativa, cree un objeto de credencial para un nombre de usuario y una contraseña especificados con el cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential).

1. Después de que se haya establecido una conexión RDP a la máquina virtual remota, seleccione el menú **Inicio** de Windows. Escriba **PowerShell** en el cuadro de búsqueda y seleccione **Windows PowerShell** para abrir una ventana de PowerShell.

    [![Imagen de una ventana de conexión de un escritorio remoto.](media\tutorial-manage-data-disk\initialize-disk-sml.png)](media\tutorial-manage-data-disk\initialize-disk-lrg.png#lightbox)

1. Abra PowerShell y ejecute el siguiente script. Cambie el valor de la variable `-DriveLetter` según corresponda. Por ejemplo, para cambiar el tamaño de la partición en la unidad **F:** , use `$driveLetter = "F"`.

    ```powershell
    $driveLetter = "[Drive Letter]" `
    $size = (Get-PartitionSupportedSize -DriveLetter $driveLetter) `
    Resize-Partition `
        -DriveLetter $driveLetter `
        -Size $size.SizeMax
    ```

1. Minimice la ventana RDP y vuelva a Azure Cloud Shell. Use el cmdlet `Get-AzDisk` para comprobar que el tamaño del disco se ha cambiado correctamente.

    ```azurepowershell-interactive
    Get-AzDisk `
        -ResourceGroupName $azResourceGroup | Out-Host -Paging
    ```

## <a name="upgrade-a-disk"></a>Actualización de un disco

Hay varias maneras de dar respuesta a los cambios en las cargas de trabajo de la organización. Por ejemplo, puede optar por actualizar un disco HDD estándar a un SSD prémium para controlar el aumento de la demanda.

Siga los pasos de esta sección para actualizar un disco administrado de estándar a prémium.

1. Seleccione la máquina virtual que contiene el disco que va a actualizar con el cmdlet `Get-AzVM`.

    ```azurepowershell-interactive
     $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. Para poder actualizar un disco de una máquina virtual, primero debe detenerla. Use el cmdlet `Stop-AzVM` para detener la máquina virtual. Se le pedirá confirmación.

    > [!IMPORTANT]
    > Antes de iniciar el apagado de una máquina virtual, confirme siempre que no hay recursos o datos importantes que se puedan perder.

    ```azurepowershell-interactive
    Stop-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Después de una breve pausa, la salida confirma que la máquina se ha detenido correctamente.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:10:23 PM
    EndTime     : 9/13/2021 7:11:12 PM
    Error       :
    ```

1. Una vez detenida la máquina virtual, obtenga una referencia al disco de sistema operativo o de datos asociado a la máquina virtual con el cmdlet `Get-AzDisk`.

    En el ejemplo siguiente se selecciona el disco del sistema operativo de la máquina virtual.

    ```azurepowershell-interactive
    $disk= Get-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -DiskName $vm.StorageProfile.OsDisk.Name
    ```

    En el ejemplo siguiente se selecciona el disco de datos de la máquina virtual.

    ```azurepowershell-interactive
        $disk= Get-AzDisk `
            -ResourceGroupName $azResourceGroup `
            -DiskName $vm.StorageProfile.DataDisks[0].Name
    ```

1. Ahora que tiene una referencia al disco, establezca el tamaño de SKU del disco en **Premium_LRS**.

    ```azurepowershell-interactive
    $disk.Sku = [Microsoft.Azure.Management.Compute.Models.DiskSku]::new('Premium_LRS')
    ```

1. A continuación, actualice la imagen del disco con el cmdlet `Update-AzDisk`.

    ```azurepowershell-interactive
    Update-AzDisk `
        -ResourceGroupName $azResourceGroup `
        -Disk $disk -DiskName $disk.Name
    ```

    La imagen del disco se actualiza. Use el código de ejemplo siguiente para comprobar que se ha actualizado la SKU del disco.

    ```azurepowershell-interactive
    $disk.Sku.Name
    ```

    La salida confirma la nueva SKU del disco.

    ```Output
    Premium_LRS
    ```

1. Por último, reinicie la máquina virtual con el cmdlet `Start-AzVM`.

    ```azurepowershell-interactive
    Start-AzVM `
        -ResourceGroupName $azResourceGroup `
        -Name $azVMName
    ```

    Después de una breve pausa, la salida confirma que la máquina se ha iniciado correctamente.

    ```Output
    OperationId : abcd1234-ab12-cd34-123456abcdef
    Status      : Succeeded
    StartTime   : 9/13/2021 7:44:54 PM
    EndTime     : 9/13/2021 7:45:15 PM
    Error       :
    ```

## <a name="detach-a-data-disk"></a>Desacoplar un disco de datos

Puede desasociar un disco de datos de una máquina virtual si quiere asociarlo a otra máquina virtual o si ya no es necesario. De forma predeterminada, los discos desasociados no se eliminan para evitar la pérdida involuntaria de datos. Un disco desasociado seguirá incurriendo en cargos por almacenamiento hasta que se elimine.

1. En primer lugar, seleccione la máquina virtual a la que está asociado el disco con el cmdlet `Get-AzVM`.

    ```azurepowershell-interactive
    $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. Seleccione el disco que desea eliminar con el cmdlet `Get-AzDisk`.

     ```azurepowershell-interactive
    $vm = Get-AzVM `
       -ResourceGroupName $azResourceGroup `
       -Name $azVMName
    ```

1. A continuación, desasocie el disco de la máquina virtual con el cmdlet `Remove-AzVMDataDisk`.

    ```azurepowershell-interactive
    Remove-AzVMDataDisk `
        -VM $vm `
        -Name $azDataDiskName
    ```

1. Actualice el estado de la máquina virtual con el cmdlet `Update-AzVM` para eliminar el disco de datos.

    ```azurepowershell-interactive
    Update-AzVM `
        -ResourceGroupName $azResourceGroup `
        -VM $vm
    ```

    Después de una breve pausa, la salida confirma que la máquina virtual se ha actualizado correctamente.

    ```output
    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK
    ```

## <a name="delete-a-data-disk"></a>Eliminar un disco de datos

Al eliminar una máquina virtual, los discos de datos asociados a esta permanecen aprovisionados y siguen incurriendo en cargos hasta que se eliminan. Este comportamiento predeterminado ayuda a evitar la pérdida de datos causada por una eliminación involuntaria.

Puede usar el siguiente script de PowerShell de ejemplo para eliminar discos no asociados. La recuperación de discos se limita a **myDemoResourceGroup** porque el modificador `-ResourceGroupName` se usa con el cmdlet `Get-AzDisk`.

```azurepowershell
# Get all disks in resource group $azResourceGroup
$allDisks = Get-AzDisk -ResourceGroupName $azResourceGroup

# Determine the number of disks in the collection
if($allDisks.Count -ne 0) {

    Write-Host "Found $($allDisks.Count) disks."

    # Iterate through the collection
    foreach ($disk in $allDisks) {

        # Use the disk's "ManagedBy" property to determine if it is unattached
        if($disk.ManagedBy -eq $null) {

            # Confirm that the disk can be deleted
            Write-Host "Deleting unattached disk $($disk.Name)."
            $confirm = Read-Host "Continue? (Y/N)"
            if ($confirm.ToUpper() -ne 'Y') { break }
            else {

                # Delete the disk
                $disk | Remove-AzDisk -Force 
                Write-Host "Unattached disk $($disk.Name) deleted."
            }
        }
    }
}
```

El disco de datos no asociado se elimina como se muestra en la salida.

```output
Name      : abcd1234-ab12-cd34-ef56-abcdef123456
StartTime : 9/13/2021 10:14:05 AM
EndTime   : 9/13/2021 10:14:35 AM
Status    : Succeeded
Error     :
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no los necesite, elimine el grupo de recursos, la máquina virtual y todos los recursos relacionados. Puede usar el siguiente script de PowerShell de ejemplo para eliminar el grupo de recursos creado anteriormente en este tutorial.  

> [!CAUTION]
> Tenga cuidado al eliminar un grupo de recursos. Para evitar la pérdida de datos importantes, confirme siempre que no hay recursos o datos importantes en el grupo de recursos antes de eliminarlo.

```azurepowershell-interactive
    Remove-AzResourceGroup -Name $azResourceGroup
```

Se le pedirá confirmación. Después de una breve pausa, la respuesta `True` confirma que **myDemoResourceGroup** se ha eliminado correctamente.
    
```Output
Confirm
Are you sure you want to remove resource group 'myDemoResourceGroup'
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y
True
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

> [!div class="checklist"]
> * Crear, asociar e inicializar un disco de datos
> * Comprobar el estado de un disco
> * Inicializar un disco
> * Expandir y actualizar un disco
> * Desasociar y eliminar un disco

Siga con el siguiente tutorial para aprender sobre la automatización de la configuración de la máquina virtual.

> [!div class="nextstepaction"]
> [Automatización de la configuración de máquinas virtuales](./tutorial-automate-vm-deployment.md)
