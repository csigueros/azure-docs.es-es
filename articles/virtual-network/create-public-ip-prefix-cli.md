---
title: 'Creación de un prefijo de dirección IP pública: CLI de Azure'
titlesuffix: Azure Virtual Network
description: Aprenda a crear un prefijo de dirección IP pública mediante la CLI de Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 07/13/2021
ms.author: allensu
ms.openlocfilehash: 1b95c4ac4a6dcf6b3f065a6d5952eeff122855f3
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652877"
---
# <a name="create-a-public-ip-address-prefix-using-azure-cli"></a>Creación de un prefijo de dirección IP pública mediante la CLI de Azure

Obtenga información sobre el prefijo de una dirección IP pública y cómo crearlo, modificarlo y eliminarlo. Un prefijo de dirección IP pública es un rango continuo de direcciones IP públicas de SKU estándar. 

Al crear un recurso de dirección IP pública, puede asignar una dirección IP pública estática a partir del prefijo y asociar la dirección a máquinas virtuales, equilibradores de carga u otros recursos. Para obtener más información, vea [Prefijo de dirección IP pública](public-ip-address-prefix.md).

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

## <a name="create-a-public-ip-address-prefix"></a>Creación del prefijo de una dirección IP pública

En esta sección, creará un prefijo de IP pública con redundancia de zona, zonal y no zonal mediante Azure PowerShell. 

Los prefijos de los ejemplos son los siguientes:

* **IPv4**: /28 (16 direcciones)

* **IPv6**: /124 (16 direcciones)

Para obtener más información sobre los tamaños de prefijo disponibles, vea [Tamaños de prefijo](public-ip-address-prefix.md#prefix-sizes).

Cree un prefijo de IP pública denominado **myPublicIpPrefix** con [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) en la ubicación **eastus2**.

### <a name="zone-redundant-ipv4-prefix"></a>Prefijo IPv4 con redundancia de zona

Para crear un prefijo de IP pública IPv4, escriba **IPv4** en el parámetro **IpAddressVersion**. Para crear un prefijo IPv4 con redundancia de zona, escriba **1,2,3** en el parámetro **Zone**.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv4 \
    --zone 1 2 3
```

### <a name="zonal-ipv4-prefix"></a>Prefijo IPv4 zonal

Para crear un prefijo de IP pública IPv4, escriba **IPv4** en el parámetro **IpAddressVersion**. Escriba **2** en el parámetro **Zone** para crear un prefijo IP zonal en la zona 2.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv4 \
    --zone 2
```

>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

### <a name="non-zonal-ipv4-prefix"></a>Prefijo IPv4 no zonal

Para crear un prefijo de IP pública IPv4, escriba **IPv4** en el parámetro **IpAddressVersion**. Quite el parámetro **Zone** para crear un prefijo IP no zonal.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv4
```

La eliminación del parámetro **zone** en el comando es válida en todas las regiones.  

La eliminación del parámetro **zone** es la selección predeterminada para las direcciones IP públicas estándar en regiones sin [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

### <a name="zone-redundant-ipv6-prefix"></a>Prefijo IPv6 con redundancia de zona

Para crear un prefijo de IP pública IPv4, escriba **IPv6** en el parámetro **IpAddressVersion**. Para crear un prefijo IPv6 con redundancia de zona, escriba **1,2,3** en el parámetro **Zone**.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv6 \
    --zone 1 2 3
```

### <a name="zonal-ipv6-prefix"></a>Prefijo IPv6 zonal

Para crear un prefijo de IP pública IPv6, escriba **IPv6** en el parámetro **IpAddressVersion**. Escriba **2** en el parámetro **Zone** para crear un prefijo IP zonal en la zona 2.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv6 \
    --zone 2
```

>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [zonas de disponibilidad](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

### <a name="non-zonal-ipv6-prefix"></a>Prefijo IPv6 no zonal

Para crear un prefijo de IP pública IPv6, escriba **IPv6** en el parámetro **IpAddressVersion**. Quite el parámetro **Zone** para crear un prefijo IP no zonal.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup \
    --location eastus2 \
    --version IPv6
```

La eliminación del parámetro **zone** en el comando es válida en todas las regiones.  

La eliminación del parámetro **zone** es la selección predeterminada para las direcciones IP públicas estándar en regiones sin [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Creación de una dirección IP pública estática a partir de un prefijo

Una vez que cree un prefijo, debe crear las direcciones IP estáticas a partir del prefijo. En esta sección, creará una dirección IP estática a partir del prefijo que ha creado antes.

Cree una dirección IP pública con [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) en el prefijo **myPublicIpPrefix**.

### <a name="ipv4-address"></a>Dirección IPv4

Para crear una IP pública IPv4, escriba **IPv4** en el parámetro **IpAddressVersion**.

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group myResourceGroup \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv4
```

### <a name="ipv6-address"></a>Dirección IPv6

Para crear un prefijo de IP pública IPv6, escriba **IPv6** en el parámetro **IpAddressVersion**.

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group myResourceGroup \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv6
```

>[!NOTE]
>Solo se pueden asignar direcciones IP públicas estáticas del intervalo del prefijo creadas con la SKU Estándar. Para más información sobre las SKU de direcciones IP públicas, consulte [Direcciones IP publicas](./public-ip-addresses.md#public-ip-addresses).

## <a name="delete-a-prefix"></a>Eliminación de un prefijo

En esta sección, aprenderá a eliminar un prefijo.

Para eliminar un prefijo de IP pública, use [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete).

```azurecli-interactive
  az network public-ip prefix delete \
    --name myPublicIpPrefix \
    --resource-group myResourceGroup
```

>[!NOTE]
>Si las direcciones dentro del prefijo están asociadas a recursos de dirección IP pública, primero debe eliminar dichos recursos de dirección IP pública. Consulte [Eliminar una dirección IP pública](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Limpieza de recursos

En este artículo, ha creado un prefijo de IP pública y una IP pública a partir de ese prefijo. 


Cuando haya terminado con el prefijo de IP pública, elimine el grupo de recursos y todos los recursos que contiene con [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
  az group delete \
    --name myResourceGroup
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre los escenarios y las ventajas de usar un [prefijo de IP pública](public-ip-address-prefix.md).
