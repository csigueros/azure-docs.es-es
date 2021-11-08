---
title: Expansión de discos duros virtuales conectados a una máquina virtual de Windows Azure
description: Expanda el tamaño del disco duro virtual conectado a una máquina virtual con Azure PowerShell en el modelo de implementación de Resource Manager.
author: kirpasingh
manager: roshar
ms.service: virtual-machines
ms.collection: windows
ms.topic: article
ms.date: 11/02/2021
ms.author: kirpas
ms.subservice: disks
ms.custom: devx-track-azurepowershell, references_regions, ignite-fall-2021
ms.openlocfilehash: d744bf5ec5619985aaa899183657504c7e3afeff
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466682"
---
# <a name="how-to-expand-virtual-hard-disks-attached-to-a-windows-virtual-machine"></a>Cómo expandir discos duros virtuales conectados a una máquina virtual de Windows

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles 

Cuando se crea una nueva máquina virtual (VM) en un grupo de recursos mediante la implementación de una imagen de [Azure Marketplace](https://azure.microsoft.com/marketplace/), la unidad del sistema operativo predeterminada suele tener 127 GB (algunas imágenes son más pequeñas de manera predeterminada). Aunque es posible agregar discos de datos a la máquina virtual (el número depende de la SKU que elija) y se recomienda instalar aplicaciones y cargas de trabajo intensivas de CPU en estos discos anexos, a menudo los clientes necesitan expandir la unidad del sistema operativo para admitir escenarios concretos:

- Para admitir aplicaciones heredadas que instalan componentes en la unidad del sistema operativo.
- Para migrar una VM o un equipo físico del entorno local con una unidad del sistema operativo más grande.

> [!IMPORTANT]
> Para cambiar el tamaño de un disco de datos o de sistema operativo de una máquina virtual de Azure, es necesario desasignar la máquina virtual.
>
> No se admite la reducción de un disco existente, y puede provocar una pérdida de datos.
> 
> Después de expandir los discos, necesita [expandir el volumen dentro del sistema operativo](#expand-the-volume-within-the-os) para aprovechar el disco más grande.

## <a name="resize-without-downtime-preview"></a>Cambio de tamaño sin tiempo de inactividad (versión preliminar)

Ahora puede cambiar el tamaño de los discos administrados sin desasignar la máquina virtual.

La versión preliminar del cambio tiene las limitaciones siguientes:

[!INCLUDE [virtual-machines-disks-expand-without-downtime-restrictions](../../../includes/virtual-machines-disks-expand-without-downtime-restrictions.md)]

Para registrar la característica, utilice el comando siguiente:

```azurepowershell
Register-AzProviderFeature -FeatureName "LiveResize" -ProviderNamespace "Microsoft.Compute"
```

Puede que el proceso de registro tarde unos minutos en completarse. Para confirmar que se ha registrado, use el comando siguiente:

```azurepowershell
Register-AzProviderFeature -FeatureName "LiveResize" -ProviderNamespace "Microsoft.Compute"
```

## <a name="resize-a-managed-disk-in-the-azure-portal"></a>Cambio de tamaño de un disco administrado en Azure Portal

> [!IMPORTANT]
> Si ha habilitado **LiveResize** y el disco cumple los requisitos de [Cambio de tamaño sin tiempo de inactividad (versión preliminar)](#resize-without-downtime-preview), puede omitir los pasos 1 y 3. Para cambiar el tamaño de un disco sin tiempo de inactividad Azure Portal, debe usar el vínculo siguiente: [https://aka.ms/iaasexp/DiskLiveResize](https://aka.ms/iaasexp/DiskLiveResize)

1. En [Azure Portal](https://aka.ms/iaasexp/DiskLiveResize), navegue a la máquina virtual para la que quiere expandir el disco. Seleccione **Detener** para detener o desasignar la máquina virtual.
1. En el menú de la izquierda, en **Configuración**, seleccione **Discos**.

    :::image type="content" source="./media/expand-os-disk/select-disks.png" alt-text="Captura de pantalla que muestra la opción Discos seleccionada en la sección Configuración del menú.":::

 
1. En **Nombre del disco**, seleccione el disco cuyo tamaño quiere cambiar.

    :::image type="content" source="./media/expand-os-disk/disk-name.png" alt-text="Captura de pantalla que muestra el panel Discos con un nombre de disco seleccionado.":::

1. En el menú de la izquierda, en **Configuración**, seleccione **Size + performance** (Tamaño y rendimiento).

    :::image type="content" source="./media/expand-os-disk/configuration.png" alt-text="Captura de pantalla que muestra la opción Size and performance (Tamaño y rendimiento) seleccionada en la sección Configuración del menú.":::

1. En **Size + performance** (Tamaño y rendimiento), seleccione el tamaño de disco que quiera.
   
   > [!WARNING]
   > El nuevo tamaño debe ser mayor que el tamaño de disco existente. El máximo permitido es 4095 GB para los discos del sistema operativo. (El blob de VHD se puede expandir más, pero el sistema operativo solo usa los primeros 4095 GB de espacio).
   > 

    :::image type="content" source="./media/expand-os-disk/size.png" alt-text="Captura de pantalla que muestra el panel Size and performance (Tamaño y rendimiento) con el tamaño de disco seleccionado.":::

1. Seleccione **Cambiar tamaño** en la parte inferior de la página.

    :::image type="content" source="./media/expand-os-disk/save.png" alt-text="Captura de pantalla que muestra el panel Size and performance (Tamaño y rendimiento) con el botón Cambiar tamaño seleccionado.":::


## <a name="resize-a-managed-disk-by-using-powershell"></a>Cambio de tamaño de un disco administrado mediante PowerShell

Abra la ventana de PowerShell o PowerShell ISE en el modo administrativo y siga estos pasos:

1. Inicie sesión en su cuenta de Microsoft Azure en el modo de administración de recursos y seleccione su suscripción:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

1. Establezca el nombre del grupo de recursos y el nombre de la máquina virtual:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

1. Obtenga una referencia a la máquina virtual:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

    > [!IMPORTANT]
    > Si ha habilitado **LiveResize** y el disco cumple los requisitos de [Cambio de tamaño sin tiempo de inactividad (versión preliminar)](#resize-without-downtime-preview), puede omitir los pasos 4 y 6.

1. Detenga la máquina virtual antes de cambiar el tamaño del disco:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

1. Obtenga una referencia al disco del sistema operativo administrado. Configure el tamaño del disco del sistema operativo en el valor deseado y actualice el disco:
   
    ```powershell
    $disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
    $disk.DiskSizeGB = 1023
    Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
    ```   
    > [!WARNING]
    > El nuevo tamaño debe ser mayor que el tamaño de disco existente. El máximo permitido es 4095 GB para los discos del sistema operativo. (El blob de VHD se puede expandir más, pero el sistema operativo solo usa los primeros 4095 GB de espacio).
    > 
         
1. La actualización de la máquina virtual puede tardar unos segundos. Cuando el comando acabe de ejecutarse, reinicie la máquina virtual:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

Eso es todo. Ahora RDP en la máquina virtual, abra Administración de equipos (o Administración de discos) y expanda la unidad utilizando el espacio recién asignado.

## <a name="resize-an-unmanaged-disk-by-using-powershell"></a>Cambio de tamaño de un disco no administrado mediante PowerShell

Abra la ventana de PowerShell o PowerShell ISE en el modo administrativo y siga estos pasos:

1. Inicie sesión en su cuenta de Microsoft Azure en el modo de administración de recursos y seleccione su suscripción:
   
    ```powershell
    Connect-AzAccount
    Select-AzSubscription –SubscriptionName 'my-subscription-name'
    ```

1. Establezca el nombre del grupo de recursos y de la máquina virtual:
   
    ```powershell
    $rgName = 'my-resource-group-name'
    $vmName = 'my-vm-name'
    ```

1. Obtenga una referencia a la máquina virtual:
   
    ```powershell
    $vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

1. Detenga la máquina virtual antes de cambiar el tamaño del disco:
   
    ```powershell
    Stop-AzVM -ResourceGroupName $rgName -Name $vmName
    ```

1. Configure el tamaño del disco del sistema operativo no administrado en el valor deseado y actualice la máquina virtual:
   
    ```powershell
    $vm.StorageProfile.OSDisk.DiskSizeGB = 1023
    Update-AzVM -ResourceGroupName $rgName -VM $vm
    ```
   
    > [!WARNING]
    > El nuevo tamaño debe ser mayor que el tamaño de disco existente. El máximo permitido es 2048 GB para los discos del sistema operativo. (El blob de VHD se puede expandir más, pero el sistema operativo solo podrá trabajar con los primeros 2048 GB).
    > 
    > 
   
1. La actualización de la máquina virtual puede tardar unos segundos. Cuando el comando acabe de ejecutarse, reinicie la máquina virtual:
   
    ```powershell
    Start-AzVM -ResourceGroupName $rgName -Name $vmName
    ```


## <a name="scripts-for-os-disk"></a>Scripts para el disco del sistema operativo

A continuación se muestra el script completo para su referencia tanto para discos administrados como no administrados:


**Discos administrados**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.OsDisk.Name
$disk.DiskSizeGB = 1023
Update-AzDisk -ResourceGroupName $rgName -Disk $disk -DiskName $disk.Name
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

**Discos no administrados**

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName 'my-subscription-name'
$rgName = 'my-resource-group-name'
$vmName = 'my-vm-name'
$vm = Get-AzVM -ResourceGroupName $rgName -Name $vmName
Stop-AzVM -ResourceGroupName $rgName -Name $vmName
$vm.StorageProfile.OSDisk.DiskSizeGB = 1023
Update-AzVM -ResourceGroupName $rgName -VM $vm
Start-AzVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="resizing-data-disks"></a>Cambio de tamaño de los discos de datos

Este artículo se centra principalmente en expandir el disco del sistema operativo de la máquina virtual, pero el script también puede utilizarse para expandir los discos de datos asociados a la máquina virtual. Por ejemplo, para expandir el primer disco de datos conectado a la máquina virtual, reemplace el objeto `OSDisk` de `StorageProfile` por la matriz `DataDisks` y utilice un índice numérico para obtener una referencia al primer disco de datos conectado, como se muestra a continuación:

**Disco administrado**

```powershell
$disk= Get-AzDisk -ResourceGroupName $rgName -DiskName $vm.StorageProfile.DataDisks[0].Name
$disk.DiskSizeGB = 1023
```

**Disco no administrado**

```powershell
$vm.StorageProfile.DataDisks[0].DiskSizeGB = 1023
```

Del mismo modo, puede hacer referencia a otros discos de datos conectados a la máquina virtual, ya sea mediante un índice, como se muestra arriba, o con la propiedad **Name** del disco:


**Disco administrado**

```powershell
(Get-AzDisk -ResourceGroupName $rgName -DiskName ($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).Name).DiskSizeGB = 1023
```

**Disco no administrado**

```powershell
($vm.StorageProfile.DataDisks | Where ({$_.Name -eq 'my-second-data-disk'})).DiskSizeGB = 1023
```

## <a name="expand-the-volume-within-the-os"></a>Expansión del volumen en el sistema operativo

Una vez expandido el disco para la máquina virtual, deberá entrar en el sistema operativo y expandir el volumen para abarcar el nuevo espacio. Existen varios métodos para expandir una partición. En esta sección se trata la conexión de la máquina virtual mediante una conexión RDP para expandir la partición mediante **DiskPart**.

1. Abra una conexión RDP a la máquina virtual.

1. Abra un símbolo del sistema y escriba **diskpart**.

1. En el símbolo del sistema **DISKPART**, escriba `list volume`. Tome nota del volumen que desea extender.

1. En el símbolo del sistema **DISKPART**, escriba `select volume <volumenumber>`. Este comando selecciona el volumen *númeroDeVolumen* que desea extender en el espacio vacío contiguo del mismo disco.

1. En el símbolo del sistema **DISKPART**, escriba `extend [size=<size>]`. Este comando extiende el volumen seleccionado por *tamaño* en megabytes (MB).


## <a name="next-steps"></a>Pasos siguientes

También puede asociar discos mediante [Azure Portal](attach-managed-disk-portal.md).
