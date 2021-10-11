---
title: 'Inicio rápido: Creación de una dirección IP pública mediante la CLI de Azure'
titleSuffix: Azure Virtual Network
description: Aprenda a crear una dirección IP pública mediante la CLI de Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: quickstart
ms.date: 10/01/2021
ms.author: allensu
ms.openlocfilehash: 8d8b1525c910592cb9cc35ae1e08da7e9e24e30b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369738"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-cli"></a>Inicio rápido: Creación de una dirección IP pública mediante la CLI de Azure

En este inicio rápido aprenderá a crear una dirección IP pública de Azure. Las direcciones IP públicas de Azure se usan para las conexiones públicas a recursos de Azure. Las direcciones IP públicas están disponibles en dos SKU: básica y estándar. Hay dos niveles de direcciones IP públicas disponibles: regionales y globales. La preferencia de enrutamiento de una dirección IP pública se establece cuando se crea. El enrutamiento de Internet y el enrutamiento de Microsoft Network son las opciones disponibles.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requiere la versión 2.0.28 de la CLI de Azure o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Un grupo de recursos de Azure es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

Cree un grupo de recursos con [az group create](/cli/azure/group#az_group_create) llamado **QuickStartCreateIP-rg** en la ubicación **eastus2**.

```azurecli-interactive
  az group create \
    --name QuickStartCreateIP-rg \
    --location eastus2
```

# <a name="standard-sku"></a>[**SKU Estándar**](#tab/create-public-ip-standard)

>[!NOTE]
>Se recomienda usar la dirección IP pública de SKU estándar para cargas de trabajo de producción.  Para obtener más información sobre las SKU, consulte **[Direcciones IP públicas](public-ip-addresses.md)** .
>
>El siguiente comando funciona con la versión de API **2020-08-01** o **posterior**.  Para más información sobre la versión de API que se usa actualmente, consulte [Tipos y proveedores de recursos](../../azure-resource-manager/management/resource-providers-and-types.md).

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para crear una dirección IPv4 pública estándar con redundancia de zona llamada **myStandardPublicIP** en **QuickStartCreateIP-rg**.  

Para crear una dirección IPv6, modifique el parámetro **`--version`** a **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP \
    --version IPv4 \
    --sku Standard \
    --zone 1 2 3
```
> [!IMPORTANT]
> En el caso de versiones de la API anteriores a 2020-08-01, ejecute el comando sin especificar un parámetro **`--zone`** para crear una dirección IP con redundancia de zona. 
>

# <a name="basic-sku"></a>[**SKU básica**](#tab/create-public-ip-basic)

En esta sección, creará una IP básica. Las direcciones IP públicas básicas no admiten zonas de disponibilidad.

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para crear una dirección IPv4 pública estática básica llamada **myBasicPublicIP** en **QuickStartCreateIP-rg**.

Para crear una dirección IPv6, modifique el parámetro **`--version`** a **IPv6**. 

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myBasicPublicIP \
    --version IPv4 \
    --sku Basic \
    --allocation-method Static
```
Si es admisible que la dirección IP cambie con el tiempo, se puede seleccionar la asignación de IP **dinámica** cambiando el valor de **`--allocation-method`** a **Dynamic**. 

>[!NOTE]
> Una dirección IPv6 básica siempre debe ser "dinámica".

---

## <a name="create-a-zonal-or-no-zone-ip-address"></a>Creación de una dirección IP zonal o no zonal

En esta sección, aprenderá a crear una dirección IP pública zonal o no zonal.

# <a name="zonal"></a>[**Zonal**](#tab/create-public-ip-zonal)

Para crear una dirección IPv4 pública zonal estándar en la Zona 2 llamada **myStandardPublicIP** en **QuickStartCreateIP-rg**, use el siguiente comando.

Para crear una dirección IPv6, modifique el parámetro **`--version`** a **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rgLB \
    --name myStandardPublicIP-zonal \
    --version IPv4 \
    --sku Standard \
    --zone 2
```

>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="non-zonal"></a>[**No zonal**](#tab/create-public-ip-non-zonal)

En esta sección, creará una dirección IP no zonal.  

>[!NOTE]
>El siguiente comando funciona con la versión de API 2020-08-01 o posterior.  Para más información sobre la versión de API que se usa actualmente, consulte [Tipos y proveedores de recursos](../../azure-resource-manager/management/resource-providers-and-types.md).

Use [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) para crear una dirección IPv4 pública estándar como un recurso no zonal llamado **myStandardPublicIP-nozone** en **QuickStartCreateIP-rg**. 

Para crear una dirección IPv6, modifique el parámetro **`--version`** a **IPv6**.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-nozone \
    --version IPv4 \
    --sku Standard
```
La eliminación del parámetro **`--zone`** en el comando es válida en todas las regiones.  

La eliminación del parámetro **`--zone`** es la selección predeterminada para las direcciones IP públicas estándar en regiones sin [Availability Zones](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

---

## <a name="routing-preference-and-tier"></a>Preferencia y nivel de enrutamiento

Las direcciones IPv4 públicas estáticas de SKU estándar admiten preferencia de enrutamiento o la característica de nivel global.

# <a name="routing-preference"></a>[**Preferencia de enrutamiento**](#tab/routing-preference)

De forma predeterminada, la preferencia de enrutamiento para las direcciones IP públicas se establece en "Red de Microsoft", que entrega el tráfico al usuario través de la red de área extensa global de Microsoft.  

La selección de **Internet** reduce el recorrido a través de la red de Microsoft y, en su lugar, utiliza la red de ISP de tránsito para entregar el tráfico a una velocidad más rentable.  

Para obtener más información sobre la preferencia de enrutamiento, vea [¿Qué es la preferencia de enrutamiento (versión preliminar)?](routing-preference-overview.md)

El comando crea una dirección IPv4 pública con redundancia de zona estándar con una preferencia de enrutamiento de tipo **Internet**:

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-RP \
    --version IPv4 \
    --ip-tags 'RoutingPreference=Internet' \
    --sku Standard \
    --zone 1 2 3
```

# <a name="tier"></a>[**Nivel**](#tab/tier)

Las direcciones IP públicas están asociadas a una sola región. El nivel **Global** abarca una dirección IP entre varias regiones. El nivel **Global** es necesario para los servidores front-end de equilibradores de carga entre regiones.  

Para más información, consulte [Equilibrador de carga entre regiones](../../load-balancer/cross-region-overview.md).

El comando siguiente crea una dirección IPv4 global. Esta dirección se puede asociar al servidor front-end de un equilibrador de carga entre regiones.

```azurecli-interactive
  az network public-ip create \
    --resource-group QuickStartCreateIP-rg \
    --name myStandardPublicIP-Global \
    --version IPv4 \
    --tier global \
    --sku Standard \
```
>[!NOTE]
>Las direcciones de nivel global no admiten Availability Zones.

---

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, elimine las direcciones IP públicas mediante los siguientes pasos:

1. Escriba **Grupo de recursos** en el cuadro de búsqueda que se encuentra en la parte superior del portal.

2. En los resultados de la búsqueda, seleccione **Grupos de recursos**.

3. Seleccione **QuickStartCreateIP-rg**.

4. Seleccione **Eliminar grupo de recursos**.

5. Escriba **QuickStartCreateIP-rg** en la sección **ESCRIBA EL NOMBRE DEL GRUPO DE RECURSOS** y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente artículo para aprender a crear un prefijo de dirección IP pública:
> [!div class="nextstepaction"]
> [Creación de un prefijo de dirección IP pública mediante la CLI de Azure](create-public-ip-prefix-cli.md)
