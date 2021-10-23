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
ms.openlocfilehash: a0c6d8dc1f9e032ca76393c52be76d1803873f8b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166420"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-cli"></a>Creación de máquinas virtuales en un conjunto de escalado flexible con la CLI de Azure

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles


En este artículo se describe el uso de la CLI de Azure para crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible. Para obtener más información sobre los conjuntos de escalado flexibles, vea [Modo de orquestación flexible para conjuntos de escalado de máquinas virtuales](flexible-virtual-machine-scale-sets.md). 

Asegúrese de haber instalado la versión más reciente de la [CLI de Azure](/cli/azure/install-az-cli2) y de haber iniciado sesión en una cuenta de Azure con [az login](/cli/azure/reference-index).


> [!CAUTION]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.


## <a name="get-started-with-flexible-scale-sets"></a>Introducción a los conjuntos de escalado flexibles

Cree un conjunto de escalado de máquinas virtuales flexible con la CLI de Azure.

### <a name="add-multiple-vms-to-a-scale-set"></a>Incorporación de varias máquinas virtuales a un conjunto de escalado

En el ejemplo siguiente se especifica un perfil de máquina virtual (tipo de máquina virtual, configuración de red, etc.) y el número de instancias que se van a crear (recuento de instancias = 2).  

```azurecli-interactive
az vmss create
--name $vmssName
--resource-group $rg
--image UbuntuLTS
--instance-count 2
--orchestration-mode flexible
```

### <a name="add-a-single-vm-to-a-scale-set"></a>Incorporación de una sola máquina virtual a un conjunto de escalado

En el ejemplo siguiente se muestra cómo crear un conjunto de escalado flexible sin ningún perfil de VM, en el que el dominio de error se establece en 1. Se crea una máquina virtual que luego se agrega al conjunto de escalado flexible.

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