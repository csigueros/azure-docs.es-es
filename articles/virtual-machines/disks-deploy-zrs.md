---
title: Implementación de un disco administrado ZRS (versión preliminar)
description: Obtenga información sobre cómo implementar un disco administrado que use almacenamiento con redundancia de zona (ZRS).
author: roygara
ms.author: rogarana
ms.date: 07/12/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 7afa1a5416651dc7e93ff354e63fefb259ea81b4
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113667352"
---
# <a name="deploy-a-managed-disk-that-uses-zone-redundant-storage-preview"></a>Implementación de un disco administrado que use almacenamiento con redundancia de zona (versión preliminar)

En este artículo, se explica cómo implementar un disco que use almacenamiento con redundancia de zona (ZRS) como opción de redundancia (versión preliminar). ZRS replica el disco administrado de Azure de forma sincrónica en tres zonas de disponibilidad de Azure en la región seleccionada. Cada zona de disponibilidad es una ubicación física individual con alimentación, refrigeración y redes independientes.

Para obtener información conceptual sobre ZRS, consulte [Almacenamiento con redundancia de zona para discos administrados (versión preliminar)](disks-redundancy.md#zone-redundant-storage-for-managed-disks-preview).

## <a name="limitations"></a>Limitaciones

[!INCLUDE [disk-storage-zrs-limitations](../../includes/disk-storage-zrs-limitations.md)]

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

### <a name="prerequisites"></a>Requisitos previos

Debe habilitar la característica para su suscripción. Siga los pasos que se indican a continuación para habilitar la característica para su suscripción:

1.  Ejecute el siguiente comando para registrar la característica para su suscripción

    ```powershell
     Register-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  Antes de probar la característica, compruebe que el estado de registro sea **Registrado** (puede tardar unos minutos) mediante el comando siguiente.

    ```powershell
     Get-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute"  
    ```

### <a name="create-a-vm-with-a-zrs-os-disk"></a>Creación de una máquina virtual con un disco del sistema operativo con ZRS

1. Inicie sesión en Azure Portal.
1. Vaya a **Máquinas virtuales** y siga el proceso normal de creación de máquinas virtuales.
1. Seleccione una región admitida y establezca **Opciones de disponibilidad** en **No se requiere redundancia de la infraestructura**.

    :::image type="content" source="media/disks-deploy-zrs/disks-zrs-portal-basic.png" alt-text="Captura de pantalla del flujo de trabajo de creación de máquinas virtuales; el panel básico, la redundancia y las regiones están resaltados." lightbox="media/disks-deploy-zrs/disks-zrs-portal-basic.png":::

1. Continúe con el panel **Discos**.
1. Seleccione el disco y seleccione uno de los discos ZRS en la lista desplegable.

    :::image type="content" source="media/disks-deploy-zrs/disks-zrs-portal-select-blade.png" alt-text="Captura de pantalla del flujo de trabajo de creación de máquinas virtuales; el panel de discos y la lista desplegable de discos del sistema operativo están expandidos con las opciones SSD premium ZRS y SSD estándar resaltadas." lightbox="media/disks-deploy-zrs/disks-zrs-portal-select-blade.png":::

1. Continúe con el resto de la implementación de la máquina virtual, tomando las decisiones que desee.

Ahora ha implementado una máquina virtual con un disco del sistema operativo con ZRS.

### <a name="create-a-zrs-disk"></a>Creación de un disco ZRS

1. En Azure Portal, busque y seleccione **Discos**.
1. Seleccione **+ Agregar** para crear un nuevo disco.
1. Seleccione una región admitida y **Zona de disponibilidad** en **Ninguna**.
1. Seleccione **Cambiar el tamaño**.

    :::image type="content" source="media/disks-deploy-zrs/create-zrs-disk-pane.png" alt-text="Captura de pantalla del flujo de trabajo de creación del disco: panel de aspectos básicos." lightbox="media/disks-deploy-zrs/create-zrs-disk-pane.png":::

1. Seleccione uno de los discos ZRS disponibles y seleccione **Aceptar**.

    :::image type="content" source="media/disks-deploy-zrs/select-zrs-disk-sku.png" alt-text="Captura de pantalla del flujo de trabajo de creación de discos, con el panel de selección del tamaño del disco y los discos ZRS resaltados." lightbox="media/disks-deploy-zrs/select-zrs-disk-sku.png":::

1. Continúe con el proceso de implementación.

Ahora ha creado un disco administrado que usa ZRS.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

#### <a name="prerequisites"></a>Requisitos previos

Debe habilitar la característica para su suscripción. Siga los pasos que se indican a continuación para habilitar la característica para su suscripción:

1.  Ejecute el siguiente comando para registrar la característica para su suscripción

    ```azurecli
    az feature register --namespace Microsoft.Compute --name SsdZrsManagedDisks
    ```
 
2.  Antes de probar la característica, compruebe que el estado de registro sea **Registrado** (puede tardar unos minutos) mediante el comando siguiente.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name SsdZrsManagedDisks
    ```

#### <a name="create-a-vm-with-zrs-disks"></a>Creación de una máquina virtual con discos ZRS

```azurecli
rgName=yourRGName
vmName=yourVMName
location=westus2
vmSize=Standard_DS2_v2
image=UbuntuLTS 
osDiskSku=StandardSSD_ZRS
dataDiskSku=Premium_ZRS


az vm create -g $rgName \
-n $vmName \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--data-disk-sizes-gb 128 \
--storage-sku os=$osDiskSku 0=$dataDiskSku
```
#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Creación de máquinas virtuales con un disco ZRS compartido conectado a las máquinas virtuales de distintas zonas
```azurecli

location=westus2
rgName=yourRGName
vmNamePrefix=yourVMNamePrefix
vmSize=Standard_DS2_v2
image=UbuntuLTS
osDiskSku=StandardSSD_LRS
sharedDiskName=yourSharedDiskName
sharedDataDiskSku=Premium_ZRS


az disk create -g $rgName \
-n $sharedDiskName \
-l $location \
--size-gb 1024 \
--sku $sharedDataDiskSku \
--max-shares 2


sharedDiskId=$(az disk show -g $rgName -n $sharedDiskName --query 'id' -o tsv)

az vm create -g $rgName \
-n $vmNamePrefix"01" \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--zone 1 \
--attach-data-disks $sharedDiskId \
--storage-sku os=$osDiskSku \
--vnet-name $vmNamePrefix"_vnet" \
--subnet $vmNamePrefix"_subnet"

az vm create -g $rgName \
-n $vmNamePrefix"02" \
-l $location \
--image $image \
--size $vmSize \
--generate-ssh-keys \
--zone 2 \
--attach-data-disks $sharedDiskId \
--storage-sku os=$osDiskSku \
--vnet-name $vmNamePrefix"_vnet" \
--subnet $vmNamePrefix"_subnet"

```
#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Creación de un conjunto de escalado de máquinas virtuales con discos ZRS
```azurecli
location=westus2
rgName=yourRGName
vmssName=yourVMSSName
vmSize=Standard_DS3_V2
image=UbuntuLTS 
osDiskSku=StandardSSD_ZRS
dataDiskSku=Premium_ZRS

az vmss create -g $rgName \
-n $vmssName \
--encryption-at-host \
--image UbuntuLTS \
--upgrade-policy automatic \
--generate-ssh-keys \
--data-disk-sizes-gb 128 \
--storage-sku os=$osDiskSku 0=$dataDiskSku
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)


#### <a name="prerequisites"></a>Requisitos previos

Debe habilitar la característica para su suscripción. Siga los pasos que se indican a continuación para habilitar la característica para su suscripción:

1.  Ejecute el siguiente comando para registrar la característica para su suscripción

    ```powershell
     Register-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  Antes de probar la característica, compruebe que el estado de registro sea **Registrado** (puede tardar unos minutos) mediante el comando siguiente.

    ```powershell
     Get-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute"  
    ```
    
#### <a name="create-a-vm-with-zrs-disks"></a>Creación de una máquina virtual con discos ZRS

```powershell
$subscriptionId="yourSubscriptionId"
$vmLocalAdminUser = "yourAdminUserName"
$vmLocalAdminSecurePassword = ConvertTo-SecureString "yourVMPassword" -AsPlainText -Force
$location = "westus2"
$rgName = "yourResourceGroupName"
$vmName = "yourVMName"
$vmSize = "Standard_DS2_v2"
$osDiskSku = "StandardSSD_ZRS"
$dataDiskSku = "Premium_ZRS"


Connect-AzAccount

Set-AzContext -Subscription $subscriptionId
    
$subnet = New-AzVirtualNetworkSubnetConfig -Name $($vmName+"_subnet") `
                                           -AddressPrefix "10.0.0.0/24"

$vnet = New-AzVirtualNetwork -Name $($vmName+"_vnet") `
                             -ResourceGroupName $rgName `
                             -Location $location `
                             -AddressPrefix "10.0.0.0/16" `
                             -Subnet $subnet

$nic = New-AzNetworkInterface -Name $($vmName+"_nic") `
                              -ResourceGroupName $rgName `
                              -Location $location `
                              -SubnetId $vnet.Subnets[0].Id
    

$vm = New-AzVMConfig -VMName $vmName `
                     -VMSize $vmSize
                     

$credential = New-Object System.Management.Automation.PSCredential ($vmLocalAdminUser, $vmLocalAdminSecurePassword);

$vm = Set-AzVMOperatingSystem -VM $vm `
                              -ComputerName $vmName `
                              -Windows `
                              -Credential $credential

$vm = Add-AzVMNetworkInterface -VM $vm -Id $NIC.Id

$vm = Set-AzVMSourceImage -VM $vm `
                          -PublisherName 'MicrosoftWindowsServer' `
                          -Offer 'WindowsServer' `
                          -Skus '2012-R2-Datacenter' `
                          -Version latest


$vm = Set-AzVMOSDisk -VM $vm `
                     -Name $($vmName +"_OSDisk") `
                     -CreateOption FromImage `
                     -StorageAccountType $osDiskSku

$vm = Add-AzVMDataDisk -VM $vm `
                       -Name $($vmName +"_DataDisk1") `
                       -DiskSizeInGB 128 `
                       -StorageAccountType $dataDiskSku `
                       -CreateOption Empty -Lun 0
    
New-AzVM -ResourceGroupName $rgName `
         -Location $location `
         -VM $vm -Verbose
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Creación de máquinas virtuales con un disco ZRS compartido conectado a las máquinas virtuales de distintas zonas

```powershell
$location = "westus2"
$rgName = "yourResourceGroupName"
$vmNamePrefix = "yourVMPrefix"
$vmSize = "Standard_DS2_v2"
$sharedDiskName = "yourSharedDiskName"
$sharedDataDiskSku = "Premium_ZRS"
$vmLocalAdminUser = "yourVMAdminUserName"
$vmLocalAdminSecurePassword = ConvertTo-SecureString "yourPassword" -AsPlainText -Force  


$datadiskconfig = New-AzDiskConfig -Location $location `
                                   -DiskSizeGB 1024 `
                                   -AccountType $sharedDataDiskSku `
                                   -CreateOption Empty `
                                   -MaxSharesCount 2 `

$sharedDisk=New-AzDisk -ResourceGroupName $rgName `
            -DiskName $sharedDiskName `
            -Disk $datadiskconfig

$credential = New-Object System.Management.Automation.PSCredential ($vmLocalAdminUser, $vmLocalAdminSecurePassword);
    
$vm1 = New-AzVm `
        -ResourceGroupName $rgName `
        -Name $($vmNamePrefix+"01") `
        -Zone 1 `
        -Location $location `
        -Size $vmSize `
        -VirtualNetworkName $($vmNamePrefix+"_vnet") `
        -SubnetName $($vmNamePrefix+"_subnet") `
        -SecurityGroupName $($vmNamePrefix+"01_sg") `
        -PublicIpAddressName $($vmNamePrefix+"01_ip") `
        -Credential $credential `
        -OpenPorts 80,3389


$vm1 = Add-AzVMDataDisk -VM $vm1 -Name $sharedDiskName -CreateOption Attach -ManagedDiskId $sharedDisk.Id -Lun 0

update-AzVm -VM $vm1 -ResourceGroupName $rgName
  
$vm2 =  New-AzVm `
        -ResourceGroupName $rgName `
        -Name $($vmNamePrefix+"02") `
        -Zone 2 `
        -Location $location `
        -Size $vmSize `
        -VirtualNetworkName $($vmNamePrefix+"_vnet") `
        -SubnetName ($vmNamePrefix+"_subnet") `
        -SecurityGroupName $($vmNamePrefix+"02_sg") `
        -PublicIpAddressName $($vmNamePrefix+"02_ip") `
        -Credential $credential `
        -OpenPorts 80,3389


$vm2 = Add-AzVMDataDisk -VM $vm1 -Name $sharedDiskName -CreateOption Attach -ManagedDiskId $sharedDisk.Id -Lun 0

update-AzVm -VM $vm1 -ResourceGroupName $rgName
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Creación de un conjunto de escalado de máquinas virtuales con discos ZRS
```powershell
$vmLocalAdminUser = "yourLocalAdminUser"
$vmLocalAdminSecurePassword = ConvertTo-SecureString "yourVMPassword" -AsPlainText -Force
$location = "westus2"
$rgName = "yourResourceGroupName"
$vmScaleSetName = "yourScaleSetName"
$vmSize = "Standard_DS3_v2"
$osDiskSku = "StandardSSD_ZRS"
$dataDiskSku = "Premium_ZRS"
   
    
$subnet = New-AzVirtualNetworkSubnetConfig -Name $($vmScaleSetName+"_subnet") `
                                                 -AddressPrefix "10.0.0.0/24"

$vnet = New-AzVirtualNetwork -Name $($vmScaleSetName+"_vnet") `
                             -ResourceGroupName $rgName `
                             -Location $location `
                             -AddressPrefix "10.0.0.0/16" `
                             -Subnet $subnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" `
                               -SubnetId $vnet.Subnets[0].Id 


$vmss = New-AzVmssConfig -Location $location `
                         -SkuCapacity 2 `
                         -SkuName $vmSize `
                         -UpgradePolicyMode 'Automatic'

$vmss = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" `
                                                -VirtualMachineScaleSet $vmss `
                                                -Primary $true `
                                                -IpConfiguration $ipConfig

$vmss = Set-AzVmssStorageProfile $vmss -OsDiskCreateOption "FromImage" `
                                       -ImageReferenceOffer 'WindowsServer' `
                                       -ImageReferenceSku '2012-R2-Datacenter' `
                                       -ImageReferenceVersion latest `
                                       -ImageReferencePublisher 'MicrosoftWindowsServer' `
                                       -ManagedDisk $osDiskSku

$vmss = Set-AzVmssOsProfile $vmss -ComputerNamePrefix $vmScaleSetName `
                                  -AdminUsername $vmLocalAdminUser `
                                  -AdminPassword $vmLocalAdminSecurePassword 

$vmss = Add-AzVmssDataDisk -VirtualMachineScaleSet $vmss `
                           -CreateOption Empty `
                           -Lun 1 `
                           -DiskSizeGB 128 `
                           -StorageAccountType $dataDiskSku

New-AzVmss -VirtualMachineScaleSet $vmss `
           -ResourceGroupName $rgName `
           -VMScaleSetName $vmScaleSetName
```

# <a name="resource-manager-template"></a>[Plantilla de Resource Manager](#tab/azure-resource-manager)

Utilice la API `2020-12-01` con la plantilla de Azure Resource Manager para crear un disco ZRS.

#### <a name="prerequisites"></a>Requisitos previos

Debe habilitar la característica para su suscripción. Siga los pasos que se indican a continuación para habilitar la característica para su suscripción:

1.  Ejecute el siguiente comando para registrar la característica para su suscripción

    ```powershell
     Register-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  Antes de probar la característica, compruebe que el estado de registro sea **Registrado** (puede tardar unos minutos) mediante el comando siguiente.

    ```powershell
     Get-AzProviderFeature -FeatureName "SsdZrsManagedDisks" -ProviderNamespace "Microsoft.Compute"  
    ```
    
#### <a name="create-a-vm-with-zrs-disks"></a>Creación de una máquina virtual con discos ZRS

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>Creación de máquinas virtuales con un disco ZRS compartido conectado a las máquinas virtuales de distintas zonas

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>Creación de un conjunto de escalado de máquinas virtuales con discos ZRS

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```
---

## <a name="next-steps"></a>Pasos siguientes

- Vea más [plantillas de Azure Resource Manager para crear una máquina virtual con discos ZRS](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks).