---
title: Creación de máquinas virtuales en un conjunto de escalado flexible con la CLI de Azure
description: Aprenda a crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible mediante la CLI de Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 09f0d1f3f39a43ece445863023fd2ff7772404b9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122868531"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>Versión preliminar: creación de máquinas virtuales en un conjunto de escalado flexible con la CLI de Azure

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles


En este artículo se describe el uso de la CLI de Azure para crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible. Para obtener más información sobre los conjuntos de escalado flexibles, vea [Modo de orquestación flexible para conjuntos de escalado de máquinas virtuales](flexible-virtual-machine-scale-sets.md). 

Asegúrese de haber instalado la versión más reciente de la [CLI de Azure](/cli/azure/install-az-cli2) y de haber iniciado sesión en una cuenta de Azure con [az login](/cli/azure/reference-index).


> [!IMPORTANT]
> En el modo de orquestación flexible, los conjuntos de escalado de máquinas virtuales se encuentran actualmente en versión preliminar pública. No es necesario ningún procedimiento de participación para usar la funcionalidad de versión preliminar pública que se describe a continuación.
> Esta versión preliminar se ofrece sin contrato de nivel de servicio y no es aconsejable usarla para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.


## <a name="register-for-flexible-orchestration-mode"></a>Registro para el modo de orquestación flexible

Para poder implementar conjuntos de escalado de máquinas virtuales en el modo de orquestación flexible, antes es preciso registrar la suscripción en la característica en vista previa (GB). El registro puede tardar varios minutos en terminar.

Utilice [az feature register](/cli/azure/feature#az_feature_register) para habilitar la versión preliminar de su suscripción.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

El registro de la característica puede tardar hasta 15 minutos. Para comprobar el estado del registro, siga estos pasos:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```


## <a name="get-started-with-flexible-scale-sets"></a>Introducción a los conjuntos de escalado flexibles

Cree un conjunto de escalado de máquinas virtuales flexible con la CLI de Azure.

### <a name="add-multiple-vms-to-a-scale-set"></a>Incorporación de varias máquinas virtuales a un conjunto de escalado

En el ejemplo siguiente se especifica un perfil de máquina virtual (tipo de máquina virtual, configuración de red, etc.) y el número de instancias que se van a crear (recuento de instancias = 2).  

```azurecli-interactive
az vmss create
--resource-group $rg
--name $vmssName
--single-placement-group false
--orchestration-mode flexible
--platform-fault-domain-count 1
--image UbuntuLTS
--admin-username azureuser
--instance-count 2
--vm-sku 'Standard_D2s_v3'
--network-api-version '2020-11-01'
--computer-name-prefix $computerNamePrefix
--vnet-name $vnetName
--subnet $subnetName
--public-ip-per-vm
```

### <a name="add-a-single-vm-to-a-scale-set"></a>Incorporación de una sola máquina virtual a un conjunto de escalado

En el ejemplo siguiente se muestra cómo crear un conjunto de escalado flexible sin ningún perfil de VM, donde el recuento de dominios de error se establece en 1. Se crea una máquina virtual que luego se agrega al conjunto de escalado flexible.

```azurecli-interactive
vmoname="my-vmss-vmo"
vmname="myVM"
rg="my-resource-group"

az group create -n "$rg" -l $location
az vmss create -n "$vmoname" -g "$rg" -l $location --orchestration-mode vm --platform-fault-domain-count 1
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmoname --image UbuntuLTS
``` 


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Aprenda a crear un conjunto de escalado flexible en Azure Portal.](flexible-virtual-machine-scale-sets-portal.md)