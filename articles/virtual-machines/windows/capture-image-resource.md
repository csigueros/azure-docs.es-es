---
title: Creación de una imagen administrada en Azure
description: Cree una imagen administrada de un VHD o de una VM generalizada en Azure. Se pueden utilizar imágenes para crear varias VM que usan discos administrados.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 09/27/2018
ms.author: cynthn
ms.custom: legacy
ms.collection: windows
ms.openlocfilehash: 3fb298dc8e01c50b562e3891f02227b416596a04
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695258"
---
# <a name="create-a-managed-image-of-a-generalized-vm-in-azure"></a>Captura de una imagen administrada de una máquina virtual generalizada en Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Windows 


Se puede crear un recurso de imagen administrado a partir de una máquina virtual (VM) generalizada que se almacena como un disco administrado o como un disco no administrado en una cuenta de almacenamiento. A partir de ese momento, la imagen se puede utilizar para crear varias máquinas virtuales. Para obtener información sobre cómo se facturan las imágenes administradas, consulte [Precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). 

Una sola imagen administrada admite hasta 20 implementaciones simultáneas. Cuando se intentan crear más de 20 máquinas virtuales simultáneamente, desde una misma imagen administrada, se pueden producir tiempos de espera de aprovisionamiento debido a las limitaciones de rendimiento de almacenamiento de un solo VHD. Para crear más de 20 máquinas virtuales simultáneamente, use una imagen de [Instancias de Shared Image Gallery](../shared-image-galleries.md) configurada con una réplica por cada 20 implementaciones simultáneas de máquina virtual.

## <a name="prerequisites"></a>Requisitos previos

Para crear una imagen, necesita una máquina virtual [generalizada](../generalize.md).

## <a name="create-a-managed-image-in-the-portal"></a>Creación de una imagen administrada en el portal 

1. Vaya a [Azure Portal](https://portal.azure.com) para administrar la imagen de la máquina virtual. Busque y seleccione **Máquinas virtuales**.

2. Seleccione la máquina virtual en la lista.

3. En la página **Máquina virtual** de la máquina virtual, en el menú superior, seleccione **Capturar**.

   Aparece la página **Crear imagen**.

4. En **Nombre**, acepte el nombre con que se rellena el espacio previamente o escriba un nombre que le gustaría que se usara para la imagen.

5. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba un nombre, o seleccione en la lista desplegable el grupo de recursos que desea utilizar.

6. Si desea eliminar la máquina virtual de origen tras haberse creado la imagen, seleccione **Eliminar automáticamente esta máquina virtual después de crear la imagen**.

7. Si desea tener la posibilidad de usar la imagen en cualquier [zona de disponibilidad](../../availability-zones/az-overview.md), seleccione **Activar** para **Resistencia de zona**.

8. Seleccione **Crear** para crear la imagen.

Después de crear la imagen, esta aparecerá como un recurso **Imagen** en la lista de recursos del grupo de recursos.



## <a name="create-an-image-of-a-vm-using-powershell"></a>Creación de una imagen de una VM mediante PowerShell

 

Crear una imagen directamente desde la VM garantiza que la imagen incluya todos los discos asociados a la VM, incluido el disco del SO y todos los discos de datos. En este ejemplo se muestra cómo crear una imagen administrada a partir de una máquina virtual que utiliza discos administrados.

Antes de comenzar, asegúrese de que tiene la última versión del módulo de Azure PowerShell. Para buscar la versión, ejecute `Get-Module -ListAvailable Az` en PowerShell. Si tiene que actualizar, consulte [Install Azure PowerShell on Windows with PowerShellGet](/powershell/azure/install-az-ps) (Instalación de Azure PowerShell en Windows con PowerShellGet). Si PowerShell se ejecuta localmente, ejecute `Connect-AzAccount` para crear una conexión con Azure.


> [!NOTE]
> Si desea almacenar la imagen en un almacenamiento con redundancia de zona, debe crearla en una región que admita [zonas de disponibilidad](../../availability-zones/az-overview.md) e incluir el parámetro `-ZoneResilient` en la configuración de la imagen (comando `New-AzImageConfig`).

Para crear una imagen de VM, siga estos pasos:

1. Cree algunas variables.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Asegúrese de que la VM se ha desasignado.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Establezca el estado de la máquina virtual en **Generalizado**. 
   
    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Obtenga la máquina virtual. 

    ```azurepowershell-interactive
    $vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Cree la configuración de la imagen.

    ```azurepowershell-interactive
    $image = New-AzImageConfig -Location $location -SourceVirtualMachineId $vm.Id 
    ```
6. Cree la imagen.

    ```azurepowershell-interactive
    New-AzImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ``` 

## <a name="create-an-image-from-a-managed-disk-using-powershell"></a>Creación de una imagen a partir de un disco administrado mediante PowerShell

Si desea crear una imagen de solo el disco del SO, especifique el identificador del disco administrado como disco del SO:

    
1. Cree algunas variables. 

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```

2. Obtenga la máquina virtual.

   ```azurepowershell-interactive
   $vm = Get-AzVm -Name $vmName -ResourceGroupName $rgName
   ```

3. Obtenga el id. del disco administrado.

    ```azurepowershell-interactive
    $diskID = $vm.StorageProfile.OsDisk.ManagedDisk.Id
    ```
   
3. Cree la configuración de la imagen.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -ManagedDiskId $diskID
    ```
    
4. Cree la imagen.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-snapshot-using-powershell"></a>Creación de una imagen a partir de una instantánea mediante PowerShell

Puede crear una imagen administrada a partir de una instantánea de una VM generalizada siguiendo estos pasos:

    
1. Cree algunas variables. 

    ```azurepowershell-interactive
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Obtenga la instantánea.

   ```azurepowershell-interactive
   $snapshot = Get-AzSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Cree la configuración de la imagen.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Cree la imagen.

    ```azurepowershell-interactive
    New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ``` 


## <a name="create-an-image-from-a-vm-that-uses-a-storage-account"></a>Creación de una imagen a partir de una máquina virtual que usa una cuenta de almacenamiento

Para crear una imagen administrada a partir de una máquina virtual que no usa discos administrados, se necesita el identificador URI del disco duro virtual del sistema operativo de la cuenta de almacenamiento en el siguiente formato: https://*mystorageaccount*.blob.core.windows.net/*vhdcontainer*/*vhdfilename.vhd*. En este ejemplo, el VHD está en *mystorageaccount*, en un contenedor denominado *vhdcontainer*, y el nombre de archivo de VHD es *vhdfilename.vhd*.


1.  Cree algunas variables.

    ```azurepowershell-interactive
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    $osVhdUri = "https://mystorageaccount.blob.core.windows.net/vhdcontainer/vhdfilename.vhd"
    ```
2. Detenga/desasigne la máquina virtual.

    ```azurepowershell-interactive
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marque la VM como generalizada.

    ```azurepowershell-interactive
    Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized  
    ```
4.  Cree la imagen con el VHD del SO generalizado.

    ```azurepowershell-interactive
    $imageConfig = New-AzImageConfig -Location $location
    $imageConfig = Set-AzImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```

    
## <a name="next-steps"></a>Pasos siguientes
- [Creación de una máquina virtual a partir de una imagen administrada](create-vm-generalized-managed.md) 
