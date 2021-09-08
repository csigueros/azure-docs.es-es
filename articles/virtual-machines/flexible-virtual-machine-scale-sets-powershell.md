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
ms.openlocfilehash: 9e01c9f4d9cdb3bdd95d77cb2cc26462a95661c6
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868530"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-powershell"></a>Versión preliminar: creación de máquinas virtuales en un conjunto de escalado flexible con PowerShell

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles


En este artículo se describe el uso de PowerShell para crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible. Para obtener más información sobre conjuntos de escalado flexibles, consulte [Modo de orquestación flexible para conjuntos de escalado de máquinas virtuales](flexible-virtual-machine-scale-sets.md). 


> [!IMPORTANT]
> En el modo de orquestación flexible, los conjuntos de escalado de máquinas virtuales se encuentran actualmente en versión preliminar pública. No es necesario ningún procedimiento de participación para usar la funcionalidad de versión preliminar pública que se describe a continuación.
> Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.


## <a name="launch-azure-cloud-shell"></a>Inicio de Azure Cloud Shell

Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar los pasos de este artículo. Tiene las herramientas comunes de Azure preinstaladas y configuradas para usarlas en la cuenta. 

Para abrir Cloud Shell, seleccione **Pruébelo** en la esquina superior derecha de un bloque de código. También puede ir a [https://shell.azure.com/powershell](https://shell.azure.com/powershell) para iniciar Cloud Shell en una pestaña independiente del explorador. Seleccione **Copiar** para copiar los bloques de código, péguelos en Cloud Shell y, luego, presione Entrar para ejecutarlos.


## <a name="register-for-flexible-orchestration-mode"></a>Registro para el modo de orquestación flexible

Para poder implementar conjuntos de escalado de máquinas virtuales en el modo de orquestación flexible, antes es preciso registrar la suscripción en la característica en vista previa (GB). El registro puede tardar varios minutos en terminar.

Use el cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) para habilitar la versión preliminar de su suscripción.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMScaleSetFlexPreview -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview -ProviderNamespace Microsoft.Compute
```

El registro de la característica puede tardar hasta 15 minutos. Para comprobar el estado del registro, siga estos pasos:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute
```


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
> [Aprenda a crear un conjunto de escalado flexible en Azure Portal.](flexible-virtual-machine-scale-sets-portal.md)
