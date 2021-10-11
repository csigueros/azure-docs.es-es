---
title: 'Inicio rápido: Creación de un prefijo de dirección IP pública: CLI de Azure'
titlesuffix: Azure Virtual Network
description: Aprenda a crear un prefijo de dirección IP pública mediante la CLI de Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 0b72eb4e70fb80602b352c32d6ffbde1cf4336da
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369686"
---
# <a name="quickstart-create-a-public-ip-address-prefix-using-the-azure-cli"></a>Inicio rápido: Creación de un prefijo de dirección IP pública mediante la CLI de Azure

Obtenga información sobre el prefijo de una dirección IP pública y cómo crearlo, modificarlo y eliminarlo. Un prefijo de dirección IP pública es un rango continuo de direcciones IP públicas de SKU estándar. 

Al crear un recurso de dirección IP pública, puede asignar una dirección IP pública estática a partir del prefijo y asociar la dirección a máquinas virtuales, equilibradores de carga u otros recursos. Para obtener más información, vea [Prefijo de dirección IP pública](public-ip-address-prefix.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requiere la versión 2.0.28 de la CLI de Azure o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create) llamado **QuickStartCreateIPPrefix-rg** en la ubicación **eastus2**.

```azurecli-interactive
  az group create \
    --name QuickStartCreateIPPrefix-rg \
    --location eastus2
```

## <a name="create-a-public-ip-address-prefix"></a>Creación del prefijo de una dirección IP pública

En esta sección, creará un prefijo de dirección IP pública con redundancia de zona, zonal y no zonal mediante Azure PowerShell. 

Los prefijos de los ejemplos son los siguientes:

* **IPv4**: /28 (16 direcciones)

* **IPv6**: /124 (16 direcciones)

Para obtener más información sobre los tamaños de prefijo disponibles, vea [Tamaños de prefijo](public-ip-address-prefix.md#prefix-sizes).

Cree un prefijo de IP pública denominado **myPublicIpPrefix** con [az network public-ip prefix create](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_create) en la ubicación **eastus2**.

## <a name="ipv4"></a>IPv4

# <a name="zone-redundant-ipv4-prefix"></a>[**Prefijo IPv4 con redundancia de zona**](#tab/ipv4-zone-redundant)

Para crear un prefijo de dirección IP pública IPv4, escriba **IPv4** en el parámetro **`--version`** . Para crear un prefijo IPv4 con redundancia de zona, escriba **1,2,3** en el parámetro **`--zone`** .

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 1 2 3
```

# <a name="zonal-ipv4-prefix"></a>[**Prefijo IPv4 zonal**](#tab/ipv4-zonal)

Para crear un prefijo de dirección IP pública IPv4, escriba **IPv4** en el parámetro **`--version`** . Escriba **2** en el parámetro **`--zone`** para crear un prefijo de dirección IP zonal en la zona 2.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4 \
    --zone 2
```

>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="non-zonal-ipv4-prefix"></a>[**Prefijo IPv4 no zonal**](#tab/ipv4-non-zonal)

Para crear un prefijo de dirección IP pública IPv4, escriba **IPv4** en el parámetro **`--version`** . Quite el parámetro **`--zone`** para crear un prefijo de dirección IP no zonal.

```azurecli-interactive
  az network public-ip prefix create \
    --length 28 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv4
```

La eliminación del parámetro **`--zone`** en el comando es válida en todas las regiones.  

La eliminación del parámetro **`--zone`** es la selección predeterminada para las direcciones IP públicas estándar en regiones sin [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---



## <a name="ipv6"></a>IPv6

# <a name="zone-redundant-ipv6-prefix"></a>[**Prefijo IPv6 con redundancia de zona**](#tab/ipv6-zone-redundant)

Para crear un prefijo de dirección IP pública IPv4, escriba **IPv6** en el parámetro **`--version`** . Para crear un prefijo IPv6 con redundancia de zona, escriba **1,2,3** en el parámetro **`--zone`** .

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 1 2 3
```

# <a name="zonal-ipv6-prefix"></a>[**Prefijo IPv6 zonal**](#tab/ipv6-zonal)

Para crear un prefijo de IP pública IPv6, escriba **IPv6** en el parámetro **`--version`** . Escriba **2** en el parámetro **`--zone`** para crear un prefijo de dirección IP zonal en la zona 2.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-zonal \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6 \
    --zone 2
```

>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="non-zonal-ipv6-prefix"></a>[**Prefijo IPv6 no zonal**](#tab/ipv6-non-zonal)

Para crear un prefijo de IP pública IPv6, escriba **IPv6** en el parámetro **`--version`** . Quite el parámetro **`--zone`** para crear un prefijo de dirección IP no zonal.

```azurecli-interactive
  az network public-ip prefix create \
    --length 124 \
    --name myPublicIpPrefix-nozone \
    --resource-group QuickStartCreateIPPrefix-rg \
    --location eastus2 \
    --version IPv6
```

La eliminación del parámetro **`--zone`** en el comando es válida en todas las regiones.  

La eliminación del parámetro **`--zone`** es la selección predeterminada para las direcciones IP públicas estándar en regiones sin [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="create-a-static-public-ip-address-from-a-prefix"></a>Creación de una dirección IP pública estática a partir de un prefijo

Una vez que cree un prefijo, debe crear las direcciones IP estáticas a partir del prefijo. En esta sección, creará una dirección IP estática a partir del prefijo que ha creado antes.

Cree una dirección IP pública con [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) en el prefijo **myPublicIpPrefix**.

# <a name="ipv4-address"></a>[**Dirección IPv4**](#tab/ipv4-address)

Para crear un prefijo de IP pública IPv4, escriba **IPv4** en el parámetro **`--version`** .

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv4
```

# <a name="ipv6-address"></a>[**Dirección IPv6**](#tab/ipv6-address)

Para crear un prefijo de IP pública IPv6, escriba **IPv6** en el parámetro **`--version`** .

```azurecli-interactive
  az network public-ip create \
    --name myPublicIP \
    --resource-group QuickStartCreateIPPrefix-rg \
    --allocation-method Static \
    --public-ip-prefix myPublicIpPrefix \
    --sku Standard \
    --version IPv6
```

---

>[!NOTE]
>Solo se pueden asignar direcciones IP públicas estáticas del intervalo del prefijo creadas con la SKU Estándar. Para más información sobre las SKU de direcciones IP públicas, consulte [Direcciones IP publicas](public-ip-addresses.md#public-ip-addresses).

## <a name="delete-a-prefix"></a>Eliminación de un prefijo

En esta sección, aprenderá a eliminar un prefijo.

Para eliminar un prefijo de IP pública, use [az network public-ip prefix delete](/cli/azure/network/public-ip/prefix#az_network_public_ip_prefix_delete).

```azurecli-interactive
  az network public-ip prefix delete \
    --name myPublicIpPrefix \
    --resource-group QuickStartCreateIPPrefix-rg
```

>[!NOTE]
>Si las direcciones dentro del prefijo están asociadas a recursos de dirección IP pública, primero debe eliminar dichos recursos de dirección IP pública. Consulte [Eliminar una dirección IP pública](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).

## <a name="clean-up-resources"></a>Limpieza de recursos

En este artículo, ha creado un prefijo de IP pública y una IP pública a partir de ese prefijo. 


Cuando haya terminado con el prefijo de IP pública, elimine el grupo de recursos y todos los recursos que contiene con [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
  az group delete \
    --name QuickStartCreateIPPrefix-rg
```

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente artículo para aprender a crear un prefijo de IP pública mediante Azure PowerShell:
> [!div class="nextstepaction"]
> [Creación de un prefijo de IP pública mediante la CLI de Azure](create-public-ip-cli.md)
