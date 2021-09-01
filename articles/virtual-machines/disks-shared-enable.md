---
title: Habilitación de los discos compartidos para Azure Managed Disks
description: Configuración de un disco administrado de Azure con discos compartidos, con el fin de poder compartirlo entre varias máquinas virtuales
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: d2a770dd007c801d2192ff08349966ff915bdd0a
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253090"
---
# <a name="enable-shared-disk"></a>Habilitación del disco compartido

En este artículo se describe cómo habilitar la característica de discos compartidos en Azure Managed Disks. Los discos compartidos de Azure son una nueva característica de Azure Managed Disks que permite conectar un disco administrado a varias máquinas virtuales (VM) al mismo tiempo. Si adjunta un disco administrado en varias VM, podrá implementar nuevas aplicaciones en clúster o migrar las existentes a Azure. 

Si busca información conceptual sobre los discos administrados que tienen habilitados discos compartidos, consulte [Discos compartidos de Azure](disks-shared.md).

## <a name="prerequisites"></a>Requisitos previos

Los scripts y comandos de este artículo requieren:

- La versión 6.0.0 o posterior del módulo de Azure Powershell.

Or
- La versión más reciente de la CLI de Azure.

## <a name="limitations"></a>Limitaciones

[!INCLUDE [virtual-machines-disks-shared-limitations](../../includes/virtual-machines-disks-shared-limitations.md)]

## <a name="supported-operating-systems"></a>Sistemas operativos admitidos

Los discos compartidos admiten varios sistemas operativos. Consulte las secciones [Windows](./disks-shared.md#windows) y [Linux](./disks-shared.md#linux) del artículo conceptual sobre los sistemas operativos compatibles.

## <a name="disk-sizes"></a>Tamaños de disco

[!INCLUDE [virtual-machines-disks-shared-sizes](../../includes/virtual-machines-disks-shared-sizes.md)]

## <a name="deploy-shared-disks"></a>Implementación de discos compartidos

### <a name="deploy-a-premium-ssd-as-a-shared-disk"></a>Implementación de un SSD Premium como un disco compartido

Para implementar un disco administrado con la característica de disco compartido habilitada, use la nueva propiedad `maxShares` y defina un valor mayor que 1. Esto permite que el disco se pueda compartir en varias VM.

> [!IMPORTANT]
> El valor de `maxShares` solo se puede establecer o cambiar cuando se desmonta un disco de todas las VM. Consulte los [tamaños de disco](#disk-sizes) para ver los valores permitidos para `maxShares`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en Azure Portal. 
1. Busque y seleccione **Discos**.
1. Seleccione **+ Crear** para crear un nuevo disco.
1. Rellene los detalles y seleccione una región adecuada y, a continuación, seleccione **Cambiar tamaño**.

    :::image type="content" source="media/disks-shared-enable/create-shared-disk-basics-pane.png" alt-text="Captura de pantalla del panel Crear un disco administrado, cambio de tamaño resaltado." lightbox="media/disks-shared-enable/create-shared-disk-basics-pane.png":::

1. Seleccione el tamaño de SSD Premium que desee y seleccione **Aceptar**.

    :::image type="content" source="media/disks-shared-enable/select-premium-shared-disk.png" alt-text="Captura de pantalla de la SKU de disco, SSD Premium resaltado." lightbox="media/disks-shared-enable/select-premium-shared-disk.png":::

1. Continúe con la implementación hasta llegar al panel **Avanzado**.
1. Seleccione **Sí** para **Habilitar disco compartido** y seleccione la cantidad de **recursos compartidos máximos** que desea.

    :::image type="content" source="media/disks-shared-enable/enable-premium-shared-disk.png" alt-text="Captura de pantalla del panel Avanzado, Habilitar disco compartido resaltado y establecer en Sí" lightbox="media/disks-shared-enable/enable-premium-shared-disk.png":::.

1. Seleccione **Revisar + crear**.


# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku Premium_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType Premium_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Plantilla de Resource Manager](#tab/azure-resource-manager)

Antes de usar la siguiente plantilla, reemplace `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]` y `[parameters('maxShares')]` con sus propios valores.

[Plantilla de disco compartido de SSD Premium](https://aka.ms/SharedPremiumDiskARMtemplate)

---

### <a name="deploy-a-standard-ssd-as-a-shared-disk"></a>Implementación de un SSD Estándar como un disco compartido

Para implementar un disco administrado con la característica de disco compartido habilitada, use la nueva propiedad `maxShares` y defina un valor mayor que 1. Esto permite que el disco se pueda compartir en varias VM.

> [!IMPORTANT]
> El valor de `maxShares` solo se puede establecer o cambiar cuando se desmonta un disco de todas las VM. Consulte los [tamaños de disco](#disk-sizes) para ver los valores permitidos para `maxShares`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Actualmente no se puede implementar un SSD estándar compartido a través de Azure Portal. Use la CLI de Azure, el módulo Azure PowerShell o una plantilla de Azure Resource Manager.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli
az disk create -g myResourceGroup -n mySharedDisk --size-gb 1024 -l westcentralus --sku StandardSSD_LRS --max-shares 2
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$dataDiskConfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType StandardSSD_LRS -CreateOption Empty -MaxSharesCount 2

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $dataDiskConfig
```

# <a name="resource-manager-template"></a>[Plantilla de Resource Manager](#tab/azure-resource-manager)

Introduzca sus propios valores en esta plantilla de Azure Resource Manager, antes de usarla:

```rest
{ 
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "dataDiskName": {
      "type": "string",
      "defaultValue": "mySharedDisk"
    },
    "dataDiskSizeGB": {
      "type": "int",
      "defaultValue": 1024
    },
    "maxShares": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('dataDiskName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-07-01",
      "sku": {
        "name": "StandardSSD_LRS"
      },
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeGB')]",
        "maxShares": "[parameters('maxShares')]"
      }
    }
  ] 
}
```

---

### <a name="deploy-an-ultra-disk-as-a-shared-disk"></a>Implementación de un disco Ultra como un disco compartido

Para implementar un disco administrado con la característica de disco compartido habilitada, cambie el parámetro `maxShares` por un valor mayor que 1. Esto permite que el disco se pueda compartir en varias VM.

> [!IMPORTANT]
> El valor de `maxShares` solo se puede establecer o cambiar cuando se desmonta un disco de todas las VM. Consulte los [tamaños de disco](#disk-sizes) para ver los valores permitidos para `maxShares`.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en Azure Portal. 
1. Busque y seleccione **Discos**.
1. Seleccione **+ Crear** para crear un nuevo disco.
1. Introduzca la información, después, seleccione **Cambiar tamaño**.
1. Seleccione disco ultra para la **SKU de disco**.

    :::image type="content" source="media/disks-shared-enable/select-ultra-shared-disk.png" alt-text="Captura de pantalla de la SKU de disco, disco ultra resaltado." lightbox="media/disks-shared-enable/select-ultra-shared-disk.png":::

1. Seleccione el tamaño del disco que desee y seleccione **Aceptar**.
1. Continúe con la implementación hasta llegar al panel **Avanzado**.
1. Seleccione **Sí** para **Habilitar disco compartido** y seleccione la cantidad de **recursos compartidos máximos** que desea.
1. Seleccione **Revisar + crear**.

    :::image type="content" source="media/disks-shared-enable/enable-ultra-shared-disk.png" alt-text="Captura de pantalla del panel Avanzado, Habilitar disco compartido resaltado." lightbox="media/disks-shared-enable/enable-ultra-shared-disk.png":::

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

##### <a name="regional-disk-example"></a>Ejemplo de disco regional

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

##### <a name="zonal-disk-example"></a>Ejemplo de disco de zona

Este ejemplo es casi el mismo que el anterior, salvo que crea un disco en la zona de disponibilidad 1.

```azurecli
#Creating an Ultra shared Disk 
az disk create -g rg1 -n clidisk --size-gb 1024 -l westus --sku UltraSSD_LRS --max-shares 5 --disk-iops-read-write 2000 --disk-mbps-read-write 200 --disk-iops-read-only 100 --disk-mbps-read-only 1 --zone 1

#Updating an Ultra shared Disk 
az disk update -g rg1 -n clidisk --disk-iops-read-write 3000 --disk-mbps-read-write 300 --set diskIopsReadOnly=100 --set diskMbpsReadOnly=1

#Show shared disk properties:
az disk show -g rg1 -n clidisk
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

##### <a name="regional-disk-example"></a>Ejemplo de disco regional

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

##### <a name="zonal-disk-example"></a>Ejemplo de disco de zona

Este ejemplo es casi el mismo que el anterior, salvo que crea un disco en la zona de disponibilidad 1.

```azurepowershell-interactive
$datadiskconfig = New-AzDiskConfig -Location 'WestCentralUS' -DiskSizeGB 1024 -AccountType UltraSSD_LRS -CreateOption Empty -DiskIOPSReadWrite 2000 -DiskMBpsReadWrite 200 -DiskIOPSReadOnly 100 -DiskMBpsReadOnly 1 -MaxSharesCount 5 -Zone 1

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'mySharedDisk' -Disk $datadiskconfig
```

# <a name="resource-manager-template"></a>[Plantilla de Resource Manager](#tab/azure-resource-manager)

##### <a name="regional-disk-example"></a>Ejemplo de disco regional

Antes de usar la siguiente plantilla, reemplace `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]` y `[parameters('diskMBpsReadOnly')]` con sus propios valores.

[Plantilla de discos Ultra compartidos regionales](https://aka.ms/SharedUltraDiskARMtemplateRegional)

##### <a name="zonal-disk-example"></a>Ejemplo de disco de zona

Antes de usar la siguiente plantilla, reemplace `[parameters('dataDiskName')]`, `[resourceGroup().location]`, `[parameters('dataDiskSizeGB')]`, `[parameters('maxShares')]`, `[parameters('diskIOPSReadWrite')]`, `[parameters('diskMBpsReadWrite')]`, `[parameters('diskIOPSReadOnly')]` y `[parameters('diskMBpsReadOnly')]` con sus propios valores.

[Plantilla de discos Ultra compartidos de zona](https://aka.ms/SharedUltraDiskARMtemplateZonal)

---

## <a name="using-azure-shared-disks-with-your-vms"></a>Uso de discos compartidos de Azure con las VM

Una vez implementado un disco compartido con `maxShares>1`, puede montar el disco en una o varias VM.

> [!NOTE]
> Si va a implementar un disco Ultra, asegúrese de que cumple los requisitos necesarios. Para más información, consulte [Uso de discos ultra de Azure](disks-enable-ultra-ssd.md).

```azurepowershell-interactive

$resourceGroup = "myResourceGroup"
$location = "WestCentralUS"

$vm = New-AzVm -ResourceGroupName $resourceGroup -Name "myVM" -Location $location -VirtualNetworkName "myVnet" -SubnetName "mySubnet" -SecurityGroupName "myNetworkSecurityGroup" -PublicIpAddressName "myPublicIpAddress"

$dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup -DiskName "mySharedDisk"

$vm = Add-AzVMDataDisk -VM $vm -Name "mySharedDisk" -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 0

update-AzVm -VM $vm -ResourceGroupName $resourceGroup
```

## <a name="supported-scsi-pr-commands"></a>Comandos de SCSI PR admitidos

Una vez montado el disco compartido en las VM del clúster, puede establecer cuórum y lectura/escritura en el disco mediante SCSI PR. Los siguientes comandos de PR están disponibles al usar discos compartidos de Azure:

Para interactuar con el disco, comience con la lista de acciones de reservas persistentes:

```
PR_REGISTER_KEY 

PR_REGISTER_AND_IGNORE 

PR_GET_CONFIGURATION 

PR_RESERVE 

PR_PREEMPT_RESERVATION 

PR_CLEAR_RESERVATION 

PR_RELEASE_RESERVATION 
```

Al usar PR_RESERVE, PR_PREEMPT_RESERVATION o PR_RELEASE_RESERVATION, proporcione uno de los siguientes tipos de reserva persistente:

```
PR_NONE 

PR_WRITE_EXCLUSIVE 

PR_EXCLUSIVE_ACCESS 

PR_WRITE_EXCLUSIVE_REGISTRANTS_ONLY 

PR_EXCLUSIVE_ACCESS_REGISTRANTS_ONLY 

PR_WRITE_EXCLUSIVE_ALL_REGISTRANTS 

PR_EXCLUSIVE_ACCESS_ALL_REGISTRANTS 
```

También debe proporcionar una clave de reserva persistente al usar PR_RESERVE, PR_REGISTER_AND_IGNORE, PR_REGISTER_KEY, PR_PREEMPT_RESERVATION, PR_CLEAR_RESERVATION o PR_RELEASE-RESERVATION.


## <a name="next-steps"></a>Pasos siguientes

Si prefiere usar plantillas de Azure Resource Manager para implementar el disco, están disponibles las siguientes plantillas de ejemplo:
- [SSD Premium](https://aka.ms/SharedPremiumDiskARMtemplate)
- [Discos Ultra regionales](https://aka.ms/SharedUltraDiskARMtemplateRegional)
- [Discos Ultra de zona](https://aka.ms/SharedUltraDiskARMtemplateZonal)
