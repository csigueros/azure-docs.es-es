---
title: 'Creación de una dirección IP pública: CLI de Azure'
titleSuffix: Azure Virtual Network
description: Obtenga información sobre cómo crear una dirección IP pública mediante la CLI de Azure
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 05/05/2021
ms.author: allensu
ms.openlocfilehash: 8b68597abaf4a715dc55a92f2445000c1aaed0d0
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435135"
---
# <a name="create-a-public-ip-address-using-azure-cli"></a>Creación de una dirección IP pública mediante la CLI de Azure

En este artículo se muestra cómo crear un recurso de dirección IP pública mediante la CLI de Azure. 

Para más información sobre los recursos que admiten direcciones IP públicas, consulte [Direcciones IP públicas](./public-ip-addresses.md).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requiere la versión 2.0.28 de la CLI de Azure o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create) llamado **myResourceGroup** en la ubicación **eastus2**.

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-standard-sku-public-ip-with-zones"></a>Creación de una dirección IP pública de SKU estándar con zonas

En esta sección, creará una dirección IP pública con zonas. Las direcciones IP públicas pueden tener redundancia de zona o ser zonales.

### <a name="zone-redundant"></a>Redundancia de zona

>[!NOTE]
>El siguiente comando funciona con la versión de API 2020-08-01 o posterior.  Para más información sobre la versión de API que se usa actualmente, consulte [Tipos y proveedores de recursos](../azure-resource-manager/management/resource-providers-and-types.md).

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para crear una dirección IPv4 pública estándar con redundancia de zona llamada **myStandardZRPublicIP** en **myResourceGroup**.  

Para crear una dirección IPv6, elija **IPv6** para el parámetro **version**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
> [!IMPORTANT]
> En el caso de versiones de la API anteriores a 2020-08-01, ejecute el comando sin especificar un parámetro de zona para crear una dirección IP con redundancia de zona. 
>

### <a name="zonal"></a>Zonal

Para crear una dirección IPv4 pública zonal estándar en la Zona 2 llamada **myStandardZonalPublicIP** en **myResourceGroup**, use el siguiente comando.

Para crear una dirección IPv6, elija **IPv6** para el parámetro **version**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 2
```

>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).


## <a name="create-standard-public-ip-without-zones"></a>Creación de una dirección IP pública estándar sin zonas

En esta sección, creará una dirección IP no zonal.  

>[!NOTE]
>El siguiente comando funciona con la versión de API 2020-08-01 o posterior.  Para más información sobre la versión de API que se usa actualmente, consulte [Tipos y proveedores de recursos](../azure-resource-manager/management/resource-providers-and-types.md).

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para crear una dirección IPv4 pública estándar como un recurso no zonal llamado **myStandardZRPublicIP** en **myResourceGroup**. 

Para crear una dirección IPv6, elija **IPv6** para el parámetro **version**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --version IPv4 \
    --sku Standard
```
La eliminación del parámetro **zone** en el comando es válida en todas las regiones.  

La eliminación del parámetro **zone** es la selección predeterminada para las direcciones IP públicas estándar en regiones sin [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-a-basic-public-ip"></a>Creación de una dirección IP pública básica

En esta sección, creará una IP básica. Las direcciones IP públicas básicas no admiten zonas de disponibilidad.

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para crear una dirección IPv4 pública estática básica llamada **myBasicPublicIP** en **myResourceGroup**.

Para crear una dirección IPv6, elija **IPv6** para el parámetro **version**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --version IPv4 \
    --sku Basic \
    --allocation-method Static
```
Si es admisible que la dirección IP cambie con el tiempo, se puede seleccionar la asignación de IP **dinámica** cambiando el valor de AllocationMethod a **Dynamic**. 

>[!NOTE]
> Una dirección IPv6 básica siempre debe ser "dinámica".

## <a name="routing-preference-and-tier"></a>Preferencia y nivel de enrutamiento

Las direcciones IPv4 públicas estáticas de SKU estándar admiten preferencia de enrutamiento o la característica de nivel global.

### <a name="routing-preference"></a>Preferencia de enrutamiento

De forma predeterminada, la preferencia de enrutamiento para las direcciones IP públicas se establece en "Red de Microsoft", que entrega el tráfico al usuario través de la red de área extensa global de Microsoft.  

La selección de **Internet** reduce el recorrido a través de la red de Microsoft y, en su lugar, utiliza la red de ISP de tránsito para entregar el tráfico a una velocidad más rentable.  

Para obtener más información sobre la preferencia de enrutamiento, vea [¿Qué es la preferencia de enrutamiento (versión preliminar)?](./routing-preference-overview.md)

El comando crea una dirección IPv4 pública con redundancia de zona estándar con una preferencia de enrutamiento de tipo **Internet**:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP-RP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

### <a name="tier"></a>Nivel

Las direcciones IP públicas están asociadas a una sola región. El nivel **Global** abarca una dirección IP entre varias regiones. El nivel **Global** es necesario para los servidores front-end de equilibradores de carga entre regiones.  

Para más información, consulte [Equilibrador de carga entre regiones](../load-balancer/cross-region-overview.md).

El comando siguiente crea una dirección IPv4 global. Esta dirección se puede asociar al servidor front-end de un equilibrador de carga entre regiones.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP-Global \
    --version IPv4 \
    --tier global \
    --sku Standard \
```
>[!NOTE]
>Las direcciones de nivel global no admiten Availability Zones.

## <a name="additional-information"></a>Información adicional 

Para más información sobre los parámetros individuales enumerados en este procedimiento, consulte [Administración de direcciones IP públicas](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Pasos siguientes
- Asocie una [dirección IP pública a una máquina virtual](./associate-public-ip-address-vm.md#azure-portal).
- Obtenga más información acerca de las [direcciones IP públicas](./public-ip-addresses.md#public-ip-addresses) en Azure.
- Obtenga más información acerca de toda la [configuración de direcciones IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address).
