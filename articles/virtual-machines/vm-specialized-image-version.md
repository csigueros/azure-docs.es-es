---
title: Creación de una VM a partir de una versión de imagen especializada
description: Cree una VM con una versión de imagen especializada en una instancia de Shared Image Gallery.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/05/2021
ms.author: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 009678278d7900908572e81b7aab4222dc3c4a07
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452342"
---
# <a name="create-a-vm-using-a-specialized-image-version"></a>Creación de una VM mediante una versión de imagen especializada 

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows 

Cree una máquina virtual a partir de una [versión de imagen especializada](./shared-image-galleries.md#generalized-and-specialized-images) almacenada en Shared Image Gallery. Si desea crear una máquina virtual mediante una versión de imagen generalizada, consulte [Creación de una máquina virtual a partir de una versión de imagen generalizada](vm-generalized-image-version.md).

Reemplace los nombres de recursos según sea necesario en estos ejemplos. 

### <a name="portal"></a>[Portal](#tab/portal)

Ahora puede crear una o varias máquinas virtuales. En este ejemplo se crea una máquina virtual denominada *myVM*, en el *myResourceGroup*, en el centro de recursos del *Este de EE. UU.*

1. Vaya a la definición de la imagen. Puede usar el filtro de recursos para mostrar todas las definiciones de imagen disponibles.
1. En la página de la definición de la imagen, seleccione **Crear máquina virtual** en el menú de la parte superior de la página.
1. En **Grupo de recursos**, seleccione **Crear nuevo** y escriba *myResourceGroup* como nombre.
1. En **Nombre de máquina virtual**, escriba *myVM*.
1. En **Región**, seleccione *Este de EE. UU.* .
1. En **Opciones de disponibilidad**, deje el valor predeterminado *No se requiere redundancia de la infraestructura*.
1. El valor de **Imagen** se completa automáticamente con la versión de imagen `latest` si el inicio se realizó desde la página para la definición de la imagen.
1. En **Tamaño**, elija un tamaño de máquina virtual en la lista de tamaños disponibles y, después, elija **Seleccionar**.
1. En **Cuenta de administrador**, el nombre de usuario aparecerá atenuado porque se usan el nombre de usuario y las credenciales de la máquina virtual de origen.
1. Si quiere permitir el acceso remoto a la máquina virtual, en **Puertos de entrada públicos**, elija **Permitir los puertos seleccionados** y, después, seleccione **SSH (22)** o **RDP (3389)** en la lista desplegable. Si no desea permitir el acceso remoto a la máquina virtual, deje **Ninguno** seleccionado en **Puertos de entrada públicos**.
1. Cuando haya terminado, seleccione el botón **Revisar y crear** situado en la parte inferior de la página.
1. Una vez que la máquina virtual haya superado la validación, seleccione **Crear** en la parte inferior de la página para iniciar la implementación.


### <a name="cli"></a>[CLI](#tab/cli)

Enumere las definiciones de imagen en una galería mediante [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) para ver el nombre y el identificador de las definiciones.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Cree la máquina virtual con [az vm create](/cli/azure/vm#az_vm_create), usando el parámetro --specialized para indicar que la imagen es una imagen especializada. 

Use el identificador de la definición de imagen en `--image` a fin de crear la máquina virtual a partir de la versión más reciente de la imagen que está disponible. También puede crear la máquina virtual a partir de una versión específica si proporciona el identificador de la versión de la imagen en `--image`. 

En este ejemplo, se creará una máquina virtual a partir de la versión más reciente de la imagen *myImageDefinition*.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Cuando tenga una versión de una imagen especializada, puede crear una o varias máquinas virtuales nuevas con el cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm). 

En este ejemplo, se usa el identificador de definición de imagen para asegurarse de que la nueva máquina virtual usará la versión más reciente de una imagen. También puede usar una versión específica con el identificador de la versión de imagen de `Set-AzVMSourceImage -Id`. Por ejemplo, para usar la versión de imagen *1.0.0*, escriba: `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`. 

Tenga en cuenta que el uso de una versión de imagen específica significa que la automatización podría producir un error si dicha versión específica no está disponible porque se eliminó o se quitó de la región. Se recomienda usar el id. de definición de la imagen para crear la nueva VM, a menos que se requiera una versión de imagen específica.

Reemplace los nombres de recursos según sea necesario en este ejemplo. 


```azurepowershell-interactive

# Create some variables for the new VM.

$resourceGroup = "mySIGSpecializedRG"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition


# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.

$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet `
   -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name MYvNET `
   -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name "mypublicdns$(Get-Random)" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig `
   -Name myNetworkSecurityGroupRuleRDP  `
   -Protocol Tcp `
   -Direction Inbound `
   -Priority 1000 `
   -SourceAddressPrefix * `
   -SourcePortRange * `
   -DestinationAddressPrefix * `
   -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name myNetworkSecurityGroup `
   -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name $vmName `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using Set-AzVMSourceImage -Id $imageDefinition.Id to use the latest available image version.

$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig

```

---

**Pasos siguientes**

Puede crear también recursos de galería de imágenes compartidas con plantillas. Hay varias plantillas de Inicio rápido de Azure disponibles: 

- [Creación de una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-create/)
- [Creación de una definición de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Creación de una versión de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
