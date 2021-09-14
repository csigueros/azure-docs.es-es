---
title: Creación de una máquina virtual a partir de una imagen generalizada de una galería
description: Cree una máquina virtual a partir de una imagen generalizada de una galería.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 7468218fbbb08a2e7157d8fc10d6433d6d784d2e
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452348"
---
# <a name="create-a-vm-from-a-generalized-image-version"></a>Creación de una máquina virtual a partir de una versión de imagen generalizada

Cree una máquina virtual a partir de una versión de imagen generalizada almacenada en [Shared Image Gallery](./shared-image-galleries.md#generalized-and-specialized-images). Si desea crear una máquina virtual mediante una imagen especializada, consulte [Creación de una máquina virtual a partir de una imagen especializada](vm-specialized-image-version.md). 


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
1. En **Cuenta de administrador**, debe proporcionar un nombre de usuario, como *azureuser*, y una contraseña o clave SSH. La contraseña debe tener al menos 12 caracteres de largo y cumplir con los [requisitos de complejidad definidos](./windows/faq.yml#what-are-the-password-requirements-when-creating-a-vm-).
1. Si quiere permitir el acceso remoto a la máquina virtual, en **Puertos de entrada públicos**, elija **Permitir puertos seleccionados** y, después, seleccione **SSH (22)** o **RDP (3389)** en la lista desplegable. Si no desea permitir el acceso remoto a la máquina virtual, deje **Ninguno** seleccionado en **Puertos de entrada públicos**.
1. Cuando haya terminado, seleccione el botón **Revisar y crear** situado en la parte inferior de la página.
1. Una vez que la máquina virtual haya superado la validación, seleccione **Crear** en la parte inferior de la página para iniciar la implementación.

### <a name="cli"></a>[CLI](#tab/cli)


Enumere las definiciones de imagen en una galería mediante [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) para ver el nombre y el identificador de las definiciones.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

Cree una máquina virtual mediante [az vm create](/cli/azure/vm#az_vm_create). Para usar la versión más reciente de la imagen, establezca `--image` en el identificador de la definición de la imagen. 

El ejemplo siguiente es para crear una máquina virtual Linux protegida con SSH. Para Windows o para proteger una máquina virtual Linux con una contraseña, quite `--generate-ssh-keys` para que se le solicite una contraseña. Si desea proporcionar una contraseña directamente, reemplace `--generate-ssh-keys` por `--admin-password`. Reemplace los nombres de los recursos según sea necesario en este ejemplo. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

También puede usar una versión específica con el identificador de la versión de imagen del parámetro `--image`. Por ejemplo, para usar la versión de imagen *1.0.0*, escriba: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Cuando tenga una versión de una imagen generalizada, puede crear una o varias VM. Mediante el uso del cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm). 

En este ejemplo, se usa el identificador de definición de imagen para asegurarse de que la nueva VM usará la versión más reciente de una imagen. También puede usar una versión específica con el identificador de la versión de imagen de `Set-AzVMSourceImage -Id`. Por ejemplo, para usar la versión de imagen *1.0.0*, escriba: `Set-AzVMSourceImage -Id "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`. 

Tenga en cuenta que el uso de una versión de imagen específica significa que la automatización podría producir un error si dicha versión específica no está disponible porque se eliminó o se quitó de la región. Se recomienda usar el id. de definición de la imagen para crear la nueva VM, a menos que se requiera una versión de imagen específica.

Reemplace los nombres de recursos según sea necesario en estos ejemplos. 

**Conjunto de parámetros simplificado**

Puede usar el conjunto de parámetros simplificado para crear rápidamente una VM a partir de una imagen. El conjunto de parámetros simplificado usa el nombre de la VM para crear automáticamente algunos de los recursos necesarios, como la red virtual y la dirección IP pública. 

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -Name $vmName `
   -Image $imageDefinition.Id
   -Credential $cred
```



**Conjunto de parámetros completo**

Puede crear una VM mediante recursos específicos con el conjunto de parámetros completo.

```azurepowershell-interactive
# Create some variables for the new VM 
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "myVMfromImage"

# Get the image. Replace the name of your resource group, gallery, and image definition. This will create the VM from the latest image version available.

$imageDefinition = Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition

# Create user object
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup `
   -Name $resourceGroup `
   -Location $location

# Network pieces
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
   -DestinationPortRange 3389 `
   -Access Allow
$nsg = New-AzNetworkSecurityGroup `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface `
   -Name myNic `
   -ResourceGroupName $resourceGroup `
   -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageDefinition.Id to use the latest image version.
$vmConfig = New-AzVMConfig `
   -VMName $vmName `
   -VMSize Standard_D1_v2 | `
   Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
   Set-AzVMSourceImage -Id $imageDefinition.Id | `
   Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM `
   -ResourceGroupName $resourceGroup `
   -Location $location `
   -VM $vmConfig
```

### <a name="rest"></a>[REST](#tab/rest)

Crea una red virtual.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}?api-version=2020-05-01

{
    "properties": {
        "addressSpace": {
            "addressPrefixes": [
                "10.0.0.0/16"
            ]
        }
    },
    "location": "eastus"
}
```

Cree una subred.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}/subnets/{subnetName}?api-version=2020-05-01

{
    "properties": {
        "addressPrefix": "10.0.0.0/16"
    },
}
```

Cree una dirección IP pública.

```rest
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{pIPName}?api-version=2020-11-01

{
  "location": "eastus"
}

```

Crear un grupo de seguridad de red.

```rest
# @name vmNSG
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}?api-version=2020-11-01

{
  "properties": {
    "securityRules": [
      {
        "name": "AllowSSH",
        "properties": {
          "protocol": "Tcp",
          "sourceAddressPrefix": "*",
          "destinationAddressPrefix": "*",
          "access": "Allow",
          "destinationPortRange": "3389",
          "sourcePortRange": "*",
          "priority": 1000,
          "direction": "Inbound"
        }
      }
    ]
  },
  "location": "eastus"
}
```

Cree una NIC.

```rest
# @name vmNIC
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}?api-version=2020-05-01

{
    "properties": {
        "enableAcceleratedNetworking": true,
        "networkSecurityGroup": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
        },
        "ipConfigurations": [
            {
                "name": "ipconfig1",
                "properties": {
                    "subnet": {
                        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/virtualNetworks/{vNetName}/subnets/{subNetName}",
                    },
                    "publicIPAddress": {
                        "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/publicIPAddresses/{pipName}"
          }
                }
            }
        ]
    },
    "location": "eastus",
}
```
Cree una máquina virtual Linux. La sección `oSProfile` contiene algunos detalles específicos del sistema operativo. Consulte el ejemplo de código siguiente para obtener la sintaxis de Windows.

```rest
# @name vm
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2020-06-01

{
    "location": "eastus",
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_DS3_v2"
        },
        "storageProfile": {
            "imageReference": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryImageName}/versions/{versionNumber}"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "managedDisk": {
                    "storageAccountType": "Standard_LRS"
                },
                "createOption": "FromImage"
            }
        },
        "osProfile": {
            "adminUsername": "{your-username}",
            "computerName": "myVM",
            "linuxConfiguration": {
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "/home/{your-username}/.ssh/authorized_keys",
                            "keyData": "{sshKey}",
                        }
                    ]
                },
                "disablePasswordAuthentication": true
            }
        },
        "networkProfile": {
            "networkInterfaces": [
                {
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
                }
            ]
        }
    },
}

```

Creación de una máquina virtual Windows.

```rest
# @name vm
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2020-06-01

{
    "location": "eastus",
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_DS3_v2"
        },
        "storageProfile": {
            "imageReference": {
                "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/galleries/{galleryName}/images/{galleryImageName}/versions/{versionNumber}"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "managedDisk": {
                    "storageAccountType": "Standard_LRS"
                },
                "createOption": "FromImage"
            }
        },
        "osProfile": {
            "adminUsername": "{your-username}",
            "computerName": "myVM",
            "adminPassword": "{your-password}"
        },
        "networkProfile": {
            "networkInterfaces": [
                {
                    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
                }
            ]
        }
    },
```

---

**Pasos siguientes**

[Azure Image Builder (versión preliminar)](./image-builder-overview.md) puede ayudar a automatizar la creación de versiones de la imagen, incluso se puede usar para actualizar y [crear una nueva versión de la imagen a partir de una versión de imagen existente](./linux/image-builder-gallery-update-image-version.md).