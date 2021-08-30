---
title: 'Creación de una dirección IP pública: Azure Portal'
titleSuffix: Azure Virtual Network
description: Aprenda a crear una IP pública en Azure Portal.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/04/2021
ms.author: allensu
ms.openlocfilehash: 6cf9e86222d98835ea5355440343df96794dbfd4
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435085"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Creación de una dirección IP pública mediante Azure Portal

En este artículo se muestra cómo crear un recurso de dirección IP pública mediante Azure Portal. 

Para obtener más información sobre los recursos a los que se puede asociar esta dirección IP pública y la diferencia entre las SKU básica y estándar, vea [Direcciones IP públicas](./public-ip-addresses.md). 

## <a name="create-a-standard-sku-public-ip-address"></a>Creación de una dirección IP pública de una SKU estándar

Use los pasos siguientes para crear una dirección IPv4 pública estándar llamada **myStandardPublicIP**.  

> [!NOTE]
>Para crear una dirección IPv6, elija **IPv6** para el parámetro **IP Version**. Si la implementación requiere una configuración de pila doble (dirección IPv4 e IPv6), elija **Ambos**.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el cuadro de búsqueda que aparece en la parte superior del portal, escriba **IP pública**.

3. En los resultados de la búsqueda, escriba **Direcciones IP públicas**.

4. Seleccione **+ Create** (+ Crear).

5. En **Crear dirección IP pública**, escriba o seleccione la siguiente información:

    | Configuración                 | Value                       |
    | ---                     | ---                         |
    | Versión de la dirección IP              | Seleccione IPv4              |    
    | SKU                     | Seleccione **Estándar**.         |
    | Nivel                   | Seleccione **Regional**.    </br> Para más información, consulte [Preferencia y nivel de enrutamiento](#routing-preference-and-tier).     |
    | Nombre                    | Escriba **myStandardPublicIP**          |
    | Asignación de dirección IP   | Se bloquea como **Estática**                |
    | Preferencia de enrutamiento     | Seleccione **Red de Microsoft**. </br> Para más información, consulte [Preferencia y nivel de enrutamiento](#routing-preference-and-tier). |
    | Tiempo de espera de inactividad (minutos)  | Deje el valor predeterminado **4**.        |
    | Etiqueta de nombre DNS          | Deje el valor en blanco.    |
    | Subscription            | Seleccione su suscripción.   |
    | Resource group          | Seleccione **Crear nuevo** y escriba **myResourceGroup**. </br> Seleccione **Aceptar**. |
    | Location                | Seleccione **(EE. UU.) Este de EE. UU. 2**.     |
    | Zona de disponibilidad       | Seleccione **Ninguna zona**. |

6. Seleccione **Crear**.

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Creación de una dirección IP estándar en Azure Portal" border="false":::

> [!NOTE]
> En las regiones con [Availability Zones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones), tiene la opción de seleccionar Ninguna zona (opción predeterminada), una zona específica o con redundancia de zona. La elección dependerá de los requisitos de error de dominio específicos. En regiones sin Availability Zones, este campo no aparecerá. </br> Para más información sobre las zonas de disponibilidad, consulte [Introducción a las zonas de disponibilidad](../availability-zones/az-overview.md).

> [!NOTE]
> IPv6 no se admite actualmente con preferencia de enrutamiento o equilibrio de carga entre regiones (nivel global).

## <a name="create-a-basic-sku-public-ip-address"></a>Creación de una IP pública dinámica para una SKU básica

En esta sección, creará una dirección IPv4 pública básica denominada **myBasicPublicIP**. 

> [!NOTE]
> Las direcciones IP públicas básicas no admiten zonas de disponibilidad.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Crear un recurso**. 
3. En el cuadro de búsqueda, escriba **Dirección IP pública**. Seleccione **Dirección IP pública** en los resultados de la búsqueda.
4. En la página **Dirección IP pública**, seleccione **Crear**.
5. En la página **Crear dirección IP pública**, escriba o seleccione la siguiente información: 

    | Configuración                 | Value                       |
    | ---                     | ---                         |
    | Versión de la dirección IP              | Seleccione IPv4                 |    
    | SKU                     | Seleccione **Básica**.         |
    | Nombre                    | Escriba **myBasicPublicIP**          |
    | Asignación de dirección IP   | Seleccione **Estática**.            |
    | Tiempo de espera de inactividad (minutos)  | Deje el valor predeterminado **4**.       |
    | Etiqueta de nombre DNS          | Deje el valor en blanco.    |
    | Subscription            | Seleccione su suscripción.   |
    | Resource group          | Seleccione **Crear nuevo** y escriba **myResourceGroup**. </br> Seleccione **Aceptar**. |
    | Location                | Seleccione **(EE. UU.) Este de EE. UU. 2**.      |

6. Seleccione **Crear**.

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Creación de una dirección IP básica en Azure Portal" border="false":::

Si es admisible que la dirección IP cambie con el tiempo, se puede seleccionar la asignación de IP **dinámica** cambiando el valor de AllocationMethod a **Dynamic**. 

---

## <a name="routing-preference-and-tier"></a>Preferencia y nivel de enrutamiento

Las direcciones IPv4 públicas estáticas de SKU estándar admiten preferencia de enrutamiento o la característica de nivel global. Elija la preferencia y el nivel de enrutamiento al crear una dirección IP pública.

### <a name="routing-preference"></a>Preferencia de enrutamiento

De forma predeterminada, la preferencia de enrutamiento para las direcciones IP públicas se establece en "Red de Microsoft", que entrega el tráfico al usuario través de la red de área extensa global de Microsoft.  

La selección de **Internet** reduce el recorrido a través de la red de Microsoft y, en su lugar, utiliza la red de ISP de tránsito para entregar el tráfico a una velocidad más rentable.  

Para obtener más información sobre la preferencia de enrutamiento, vea [¿Qué es la preferencia de enrutamiento (versión preliminar)?](./routing-preference-overview.md)

:::image type="content" source="./media/create-public-ip-portal/routing-preference.png" alt-text="Configuración de la preferencia de enrutamiento en Azure Portal" border="false":::

### <a name="tier"></a>Nivel

Las direcciones IP públicas están asociadas a una sola región. El nivel **Global** abarca una dirección IP entre varias regiones. El nivel **Global** es necesario para los servidores front-end de equilibradores de carga entre regiones.  

Para más información, consulte [Equilibrador de carga entre regiones](../load-balancer/cross-region-overview.md).

:::image type="content" source="./media/create-public-ip-portal/tier.png" alt-text="Configuración del nivel en Azure Portal" border="false":::

## <a name="additional-information"></a>Información adicional 

Para más información sobre los campos individuales enumerados anteriormente, vea [Administración de direcciones IP públicas](./virtual-network-public-ip-address.md#create-a-public-ip-address).

## <a name="next-steps"></a>Pasos siguientes
- Asocie una [dirección IP pública a una máquina virtual](./associate-public-ip-address-vm.md#azure-portal).
- Obtenga más información acerca de las [direcciones IP públicas](./public-ip-addresses.md#public-ip-addresses) en Azure.
- Obtenga más información acerca de toda la [configuración de direcciones IP públicas](virtual-network-public-ip-address.md#create-a-public-ip-address).