---
title: Creación de máquinas virtuales en un conjunto de escalado flexible con Azure PowerShell
description: Aprenda a crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible mediante PowerShell.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 45f998dbba1ffac99fc8d491cb90694a76c11f98
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165751"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-powershell"></a>Creación de máquinas virtuales en un conjunto de escalado flexible con PowerShell

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles


En este artículo se describe el uso de PowerShell para crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible. Para obtener más información sobre conjuntos de escalado flexibles, consulte [Modo de orquestación flexible para conjuntos de escalado de máquinas virtuales](flexible-virtual-machine-scale-sets.md). 

> [!CAUTION]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.


## <a name="launch-azure-cloud-shell"></a>Inicio de Azure Cloud Shell

Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar los pasos de este artículo. Tiene las herramientas comunes de Azure preinstaladas y configuradas para usarlas en la cuenta. 

Para abrir Cloud Shell, seleccione **Pruébelo** en la esquina superior derecha de un bloque de código. También puede ir a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) para iniciar Cloud Shell en una pestaña independiente del explorador. Seleccione **Copiar** para copiar los bloques de código, péguelos en Cloud Shell y, luego, presione Entrar para ejecutarlos.


## <a name="get-started-with-flexible-scale-sets"></a>Introducción a los conjuntos de escalado flexibles

Cree un conjunto de escalado de máquinas virtuales flexible con Azure PowerShell.

### <a name="add-multiple-vms-to-a-scale-set"></a>Adición de varias máquinas virtuales a un conjunto de escalado 

En el ejemplo siguiente, especificamos un perfil de máquina virtual (tipo de máquina virtual, configuración de red, tipo de almacenamiento, etc.) y el número de instancias que se crearán (capacidad de SKU = 2). 

1. Creación de configuraciones de dirección IP:

    ```azurepowershell-interactive
    $ipConfig = New-AzVmssIpConfig -Name "myIPConfig"
    -SubnetId "${vnetid}/subnets/default" `
    -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id
    ```

1. Cree un objeto de configuración:

    El objeto config almacena la información básica para crear un conjunto de escalado.

    ```azurepowershell-interactive
    $vmssConfig = New-AzVmssConfig -Location $loc
    -SkuCapacity 2 -SkuName "Standard_DS1_v2"
    -OrchestrationMode 'Flexible' `
    -PlatformFaultDomainCount 1
    ```

1. Haga referencia a una imagen de máquina virtual de la galería:

    ```azurepowershell-interactive
    Set-AzVmssStorageProfile $vmssConfig -OsDiskCreateOption "FromImage"
    -ImageReferencePublisher "Canonical" -ImageReferenceOffer "UbuntuServer"
    -ImageReferenceSku "18.04-LTS" `
    -ImageReferenceVersion "latest"
    ```

1. Configure la información para la autenticación con la máquina virtual:

    ```azurepowershell-interactive
    Set-AzVmssOsProfile $vmssConfig -AdminUsername $cred.UserName
    -AdminPassword $cred.Password -ComputerNamePrefix $vmname
    ```

1. Conecte la red virtual al objeto de configuración:

    ```azurepowershell-interactive
    Add-AzVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmssConfig
    -Name "network-config" -Primary $true
    -IPConfiguration $ipConfig `
    -NetworkApiVersion '2020-11-01'
    ```

1. Cree el conjunto de escalado con el objeto de configuración:

    Este paso puede tardar unos minutos. 

    ```azurepowershell-interactive
    New-AzVmss -ResourceGroupName $rgname
    -Name $vmssName `
    -VirtualMachineScaleSet $vmssConfig
    ```

### <a name="add-a-single-vm-to-a-scale-set"></a>Adición de una sola máquina virtual a un conjunto de escalado

En el ejemplo siguiente se muestra cómo crear un conjunto de escalado flexible sin ningún perfil de VM, en el que el dominio de error se establece en 1. Se crea una máquina virtual y, a continuación, se agrega al conjunto de escalado flexible.

1. Inicie sesión a Azure PowerShell y especifique la suscripción y las variables para la implementación. 

    ```azurepowershell-interactive
    Connect-AzAccount
    Set-AzContext `
        -Subscription "00000000-0000-0000-0000-000000000" 
    
    $loc = "eastus" 
    $rgname = "myResourceGroupFlexible" 
    $vmssName = "myFlexibleVMSS" 
    $vmname = "myFlexibleVM"
    ```

1. No especifique parámetros de perfil de máquina virtual como redes o SKU de máquina virtual.

    ```azurepowershell-interactive
    $VmssConfigWithoutVmProfile = new-azvmssconfig -location $loc -platformfaultdomain 1 `
    $VmssFlex = new-azvmss -resourcegroupname $rgname -vmscalesetname $vmssName -virtualmachinescaleset $VmssConfigWithoutVmProfile 
    ```
 
1. Agregue una máquina virtual al conjunto de escalado flexible.

    ```azurepowershell-interactive
    $vm = new-azvm -resourcegroupname $rgname -location $loc -name $vmname -credential $cred -domainnamelabel $domainName -vmssid $VmssFlex.id 
    ```


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Obtenga información sobre cómo crear un conjunto de escalado flexible en Azure Portal.](flexible-virtual-machine-scale-sets-portal.md)
