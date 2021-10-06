---
title: Configuración de la preferencia de enrutamiento para una dirección IP pública mediante la CLI de Azure
titlesuffix: Azure Virtual Network
description: Aprenda a crear una dirección IP pública con una preferencia de enrutamiento de tráfico de Internet mediante la CLI de Azure.
services: virtual-network
documentationcenter: na
author: asudbring
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: 380fc6e56cab49056e148a3ca14c8973a0539c6d
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367728"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Configuración de la preferencia de enrutamiento para una dirección IP pública mediante la CLI de Azure

En este artículo se muestra cómo configurar las preferencias de enrutamiento a través de la red de ISP (opción de **Internet**) para una dirección IP pública mediante la CLI de Azure. Después de crear la dirección IP pública, puede asociarla a los siguientes recursos de Azure para el tráfico entrante y saliente de Internet:

* Máquina virtual
* Conjunto de escalado de máquina virtual
* Azure Kubernetes Service (AKS)
* Equilibrador de carga accesible desde Internet
* Application Gateway
* Azure Firewall

De forma predeterminada, la preferencia de enrutamiento de la dirección IP pública se establece en la red global de Microsoft para todos los servicios de Azure y puede asociarse a cualquier servicio de Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0.49 de la CLI de Azure, o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Para crear un grupo de recursos, use el comando [az group create](/cli/azure/group#az_group_create). En el siguiente ejemplo se crea un grupo de recursos en la región **Este de EE. UU.** de Azure:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Crear una dirección IP pública

Cree una dirección IP pública con preferencia de enrutamiento y de tipo **Internet** mediante el comando [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create), con el formato que se muestra a continuación.

El siguiente comando crea una nueva dirección IP pública con preferencia de enrutamiento de **Internet** en la región de Azure del **Este de EE. UU.** .

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  Actualmente, la preferencia de enrutamiento solo admite direcciones IP públicas IPV4.

Puede asociar la dirección IP pública creada anteriormente con una máquina virtual de [Windows](../../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Use la sección CLI en la siguiente página del tutorial: [asociación de una dirección IP pública a una máquina virtual](../../virtual-network/associate-public-ip-address-vm.md#azure-cli) para asociar la dirección IP pública a la VM. Asimismo, puede asociar una dirección IP pública que haya creado con anterioridad con una instancia de [Azure Load Balancer](../../load-balancer/load-balancer-overview.md) asignándola a la configuración de **front-end** del equilibrador de carga. La dirección IP pública actúa como dirección IP virtual (VIP) de carga equilibrada.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [preferencia de enrutamiento en direcciones IP públicas](routing-preference-overview.md). 
- [Configure la preferencia de enrutamiento de una VM mediante la CLI de Azure](../../virtual-network/configure-routing-preference-virtual-machine-cli.md).
