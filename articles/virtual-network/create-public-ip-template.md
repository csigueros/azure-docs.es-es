---
title: Creación de una IP pública mediante una plantilla de Resource Manager
titleSuffix: Azure Virtual Network
description: Obtenga información acerca de cómo crear una IP pública mediante una plantilla de Resource Manager.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 5839d4a66b0215c86c9521393b3d04f044b53f09
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113439462"
---
# <a name="create-a-public-ip-address-using-a-resource-manager-template"></a>Creación de una dirección IP pública mediante una plantilla de Resource Manager

En este artículo se muestra cómo crear un recurso de dirección IP pública dentro de una plantilla de Resource Manager.

Para obtener más información sobre los recursos a los que se puede asociar esta dirección IP pública y la diferencia entre las SKU básica y estándar, vea [Direcciones IP públicas](./public-ip-addresses.md). 

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
* Un grupo de recursos en la suscripción de Azure.
* Una plantilla de Azure Resource Manager para las secciones de la IP pública.

## <a name="create-standard-sku-public-ip-with-zones"></a>Creación de una dirección IP pública de SKU estándar con zonas

En esta sección, creará una dirección IP pública con zonas. Las direcciones IP públicas pueden tener redundancia de zona o ser zonales.

### <a name="zone-redundant"></a>Redundancia de zona

El código de esta sección crea una dirección IPv4 pública estándar con redundancia de zona denominada **myStandardZRPublicIP**.

Para crear una dirección IPv6, modifique el parámetro **publicIPAddressVersion** a **IPv6**.

Sección de plantilla que se va a agregar:

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardZRPublicIP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "zones": [
                "1",
                "2",
                "3"
      ],
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```
> [!IMPORTANT]
> En el caso de versiones de la API anteriores a 2020-08-01, use el código anterior sin especificar un parámetro de zona para una SKU estándar para crear una dirección IP con redundancia de zona. 
>

>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

### <a name="zonal"></a>Zonal

El código de esta sección crea una dirección IPv4 pública zonal estándar denominada **myStandardZonalPublicIP**. 

Para crear una dirección IP pública zonal estándar en la Zona 2, la propiedad **"zonas"** contiene un "2".

Sección de plantilla que se va a agregar:

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardZonalPublicIP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "zones": [
                "2"
      ],
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```

>[!NOTE]
>Las opciones anteriores para las zonas son solo selecciones válidas en regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

## <a name="create-standard-public-ip-without-zones"></a>Creación de una dirección IP pública estándar sin zonas

En esta sección, creará una dirección IP no zonal. 

El código de esta sección crea una dirección IPv4 pública no zonal estándar denominada **myStandardPublicIP**. La sección de código es válida para todas las regiones con o sin [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

Para crear una dirección IPv6, modifique el parámetro **publicIPAddressVersion** a **IPv6**.

Sección de plantilla que se va a agregar:

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardPublicIP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```
> [!IMPORTANT]
> En el caso de versiones de la API anteriores a 2020-08-01, la no especificación de un parámetro de zona para una SKU estándar creará una dirección IP con redundancia de zona. 
>


## <a name="create-a-basic-public-ip"></a>Creación de una dirección IP pública básica

En esta sección, creará una IP básica. Las direcciones IP públicas básicas no admiten zonas de disponibilidad. 

El código de esta sección crea una dirección IPv4 pública básica denominada **myBasicPublicIP**.

Para crear una dirección IPv6, modifique el parámetro **publicIPAddressVersion** a **IPv6**. 

Sección de plantilla que se va a agregar:

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myBasicPublicIP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Basic"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": 'IPv4'
      }
```

Si es admisible que la dirección IP cambie con el tiempo, se puede seleccionar la asignación de IP **publicIPAllocationMethod** cambiando el valor de AllocationMethod a **Dynamic**. 

>[!NOTE]
> Una dirección IPv6 básica siempre debe ser "dinámica".

## <a name="routing-preference-and-tier"></a>Preferencia y nivel de enrutamiento

Las direcciones IPv4 públicas estáticas de SKU estándar admiten preferencia de enrutamiento o la característica de nivel global.

### <a name="routing-preference"></a>Preferencia de enrutamiento

De forma predeterminada, la preferencia de enrutamiento para las direcciones IP públicas se establece en "Red de Microsoft", que entrega el tráfico al usuario través de la red de área extensa global de Microsoft.  

La selección de **Internet** reduce el recorrido a través de la red de Microsoft y, en su lugar, utiliza la red de ISP de tránsito para entregar el tráfico a una velocidad más rentable.  

Para obtener más información sobre la preferencia de enrutamiento, vea [¿Qué es la preferencia de enrutamiento (versión preliminar)?](./routing-preference-overview.md)

Para usar la preferencia de enrutamiento de Internet para una dirección IPv4 pública con redundancia de zona estándar, la sección de plantilla debe ser similar a la siguiente:

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardZRPublicIP-RP",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "zones": [
                "1",
                "2",
                "3"
      ],
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4",
            "ipTags": [
          {
           "ipTagType": "RoutingPreference",
            "tag": "Internet"
           }
         ]
      }
    }
```

### <a name="tier"></a>Nivel

Las direcciones IP públicas están asociadas a una sola región. El nivel **Global** abarca una dirección IP entre varias regiones. El nivel **Global** es necesario para los servidores front-end de equilibradores de carga entre regiones.  

Para más información, consulte [Equilibrador de carga entre regiones](../load-balancer/cross-region-overview.md).

Para usar una dirección IPv4 pública global estándar, la sección de plantilla debe tener un aspecto similar al siguiente:

```JSON
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "myStandardPublicIP-Global",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard",
            "tier": "Global"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv4"
      }
```

## <a name="additional-information"></a>Información adicional 

Para obtener más información sobre las propiedades de la IP pública enumeradas en este artículo, consulte [Administración de direcciones IP públicas](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Pasos siguientes
- Asocie una [dirección IP pública a una máquina virtual](./associate-public-ip-address-vm.md#azure-portal).
- Obtenga más información acerca de las [direcciones IP públicas](./public-ip-addresses.md#public-ip-addresses) en Azure.
- Obtenga más información acerca de toda la [configuración de direcciones IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address).
