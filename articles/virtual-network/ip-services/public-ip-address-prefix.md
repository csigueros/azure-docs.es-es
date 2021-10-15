---
title: Prefijo de dirección IP pública de Azure
titleSuffix: Azure Virtual Network
description: Obtenga información sobre qué es un prefijo de dirección IP pública de Azure y cómo puede ayudarle a asignar direcciones IP públicas a los recursos.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 05/20/2021
ms.author: allensu
ms.openlocfilehash: 4908810bc6311593adaa157a88355e182aeba7e8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367844"
---
# <a name="public-ip-address-prefix"></a>Prefijo de dirección IP pública

Un prefijo de dirección IP pública es un intervalo reservado de [direcciones IP públicas](public-ip-addresses.md#public-ip-addresses) en Azure. Las direcciones IP públicas se asignan desde un grupo de direcciones en cada región de Azure. Para crear un prefijo de dirección IP pública en una región y una suscripción de Azure, especifique un nombre y un tamaño de prefijo. El tamaño del prefijo es el número de direcciones disponibles para usar. Los prefijos de dirección IP pública constan de direcciones IPv4 o IPv6.  En las regiones con Availability Zones, los prefijos de dirección IP pública se pueden crear como con prefijos con redundancia de zona o asociados a una zona de disponibilidad específica.  Después de crear el prefijo de IP pública, puede crear direcciones IP públicas.

## <a name="benefits"></a>Ventajas

- Creación de recursos de dirección IP pública desde un intervalo conocido. Las direcciones que se crean con el prefijo se pueden asignar a cualquier recurso de Azure al que se pueda asignar una dirección IP pública de SKU estándar.
- Al eliminar direcciones IP públicas individuales, estas *devuelven* al intervalo reservado para su reutilización posterior. Las direcciones IP del prefijo de dirección IP pública se reservan para su uso hasta que elimine el prefijo.
- Puede ver las direcciones IP que se han proporcionado y que están disponibles en el intervalo de prefijos.

## <a name="prefix-sizes"></a>Tamaños de prefijo

Están disponibles los tamaños de prefijo de IP pública siguientes:

-  /28 (IPv4) o /124 (IPv6) = 16 direcciones
-  /29 (IPv4) o /125 (IPv6) = 8 direcciones
-  /30 (IPv4) o /126 (IPv6) = 4 direcciones
-  /31 (IPv4) o /127 (IPv6) = 2 direcciones

El tamaño del prefijo se especifica como un tamaño de máscara de enrutamiento de interdominios sin clases (CIDR).

No hay límites en cuanto al número de prefijos creados en una suscripción. El número de intervalos creados no puede superar el límite de direcciones IP públicas estáticas permitido en su suscripción. Para más información, consulte [Límites de Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="scenarios"></a>Escenarios
Puede asociar los siguientes recursos a una dirección IP pública estática desde un prefijo:

|Resource|Escenario|Pasos|
|---|---|---|
|Máquinas virtuales| La asociación de direcciones IP públicas desde un prefijo a las máquinas virtuales de Azure reduce la sobrecarga de administración que se produce cuando se agregan direcciones IP a una lista de permitidas en el firewall. Puede agregar un prefijo completo con una única regla de firewall. A medida que escala con máquinas virtuales de Azure, puede asociar direcciones IP del mismo prefijo para ahorrar costos, tiempo y sobrecarga de administración.| Para asociar direcciones IP de un prefijo a la máquina virtual: </br> 1. [Cree un prefijo.](manage-public-ip-address-prefix.md) </br> 2. [Cree una dirección IP del prefijo.](manage-public-ip-address-prefix.md) </br> 3. [Asocie la dirección IP a la interfaz de red de la máquina virtual.](../../virtual-network/virtual-network-network-interface-addresses.md#add-ip-addresses) </br> También puede [asociar las direcciones IP a un conjunto de escalado de máquinas virtuales](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/).
| Equilibradores de carga estándar | La asociación de direcciones IP públicas de un prefijo a la configuración IP de front-end o a la regla de salida de un equilibrador de carga garantiza la simplificación del espacio de direcciones IP públicas de Azure. Para simplificar su escenario, limpie las conexiones salientes de un rango de direcciones IP contiguas. | Para asociar las direcciones IP de un prefijo a un equilibrador de carga: </br> 1. [Cree un prefijo.](manage-public-ip-address-prefix.md) </br> 2. [Cree una dirección IP del prefijo.](manage-public-ip-address-prefix.md) </br> 3. Al crear el equilibrador de carga, seleccione o actualice la dirección IP que creó en el paso 2 como dirección IP de front-end del equilibrador de carga. |
| Azure Firewall | Puede usar una dirección IP pública de un prefijo para la conexión SNAT de salida. Todo el tráfico de red virtual saliente se traslada a la dirección IP pública de [Azure Firewall](../../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). | Para asociar una dirección IP de un prefijo a su firewall: </br> 1. [Cree un prefijo.](manage-public-ip-address-prefix.md) </br> 2. [Cree una dirección IP del prefijo.](manage-public-ip-address-prefix.md) </br> 3. Cuando [implemente el firewall de Azure](../../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), no olvide seleccionar la dirección IP que previamente proporcionó desde el prefijo.|
| VPN Gateway (AZ SKU) o Application Gateway v2, NAT Gateway | Puede usar una dirección IP pública desde un prefijo para su puerta de enlace | Para asociar una dirección IP de un prefijo a su puerta de enlace: </br> 1. [Cree un prefijo.](manage-public-ip-address-prefix.md) </br> 2. [Cree una dirección IP del prefijo.](manage-public-ip-address-prefix.md) </br> 3. Al implementar [VPN Gateway](../../vpn-gateway/tutorial-create-gateway-portal.md), [Application Gateway](../../application-gateway/quick-create-portal.md#create-an-application-gateway) o [NAT Gateway](../nat-gateway/tutorial-create-nat-gateway-portal.md), asegúrese de seleccionar la dirección IP que proporcionó anteriormente desde el prefijo.|

Además, determinados recursos pueden usar directamente el recurso de prefijo de dirección IP pública:

Resource|Escenario|Pasos|
|---|---|---|
|Conjuntos de escalado de máquinas virtuales | Puede usar un prefijo de dirección IP pública para generar direcciones IP de nivel de instancia en un conjunto de escalado de máquinas virtuales, aunque no se crearán recursos de IP pública individuales. | Use una [plantilla](https://azure.microsoft.com/resources/templates/vmms-with-public-ip-prefix/) con instrucciones para usar este prefijo para la configuración de IP públicas como parte de la creación del conjunto de escalado. |
| Equilibradores de carga estándar | Se puede usar un prefijo de dirección IP pública para escalar un equilibrador de carga [mediante todas las direcciones IP del intervalo para las conexiones salientes](../../load-balancer/outbound-rules.md#scale). | Para asociar un prefijo al equilibrador de carga: </br> 1. [Cree un prefijo.](manage-public-ip-address-prefix.md) </br> 2. Al crear el equilibrador de carga, seleccione el prefijo IP asociado al front-end del equilibrador de carga. |
| NAT Gateway | Se puede usar un prefijo de IP pública para escalar una puerta de enlace NAT mediante las direcciones IP públicas del prefijo para las conexiones salientes. | Para asociar un prefijo a su instancia de NAT Gateway: </br> 1. [Cree un prefijo.](manage-public-ip-address-prefix.md) </br> 2. Al crear la instancia de NAT Gateway, seleccione el prefijo IP como IP saliente. |
| VPN Gateway (AZ SKU) o Application Gateway v2 | Puede usar una dirección IP pública de un prefijo para su VPN con redundancia de zona o Application Gateway v2. | Para asociar una dirección IP de un prefijo a su puerta de enlace: </br> 1. [Cree un prefijo.](manage-public-ip-address-prefix.md) </br> 2. [Cree una dirección IP del prefijo.](manage-public-ip-address-prefix.md) </br> 3. Al implementar [VPN Gateway](../../vpn-gateway/tutorial-create-gateway-portal.md) o [Application Gateway](../../application-gateway/quick-create-portal.md#create-an-application-gateway), asegúrese de seleccionar la dirección IP que proporcionó anteriormente desde el prefijo.|

## <a name="limitations"></a>Limitaciones

- No se pueden especificar las direcciones IP del prefijo. Azure proporciona las direcciones IP para el prefijo, en función del tamaño que especifique.  Además, todas las direcciones IP públicas creadas a partir del prefijo deben existir en la misma región y suscripción de Azure que el prefijo. Las direcciones se deben asignar a recursos de la misma región y suscripción.
- Puede crear un prefijo de hasta 16 direcciones IP. Para más información, consulte [Solicitudes de aumento de los límites de redes](../../azure-portal/supportability/networking-quota-requests.md) y [Límites de Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- El tamaño del intervalo no se puede modificar una vez creado el prefijo.
- Solo se pueden asignar direcciones IP públicas estáticas con la SKU estándar desde el intervalo del prefijo. Para más información sobre las SKU de direcciones IP públicas, consulte [Direcciones IP publicas](public-ip-addresses.md#public-ip-addresses).
- Las direcciones del intervalo solo se pueden asignar a recursos de Azure Resource Manager. En el modelo de implementación clásico no se pueden asignar direcciones a recursos.
- No se puede eliminar un prefijo si tiene direcciones asignadas a recursos de dirección IP públicas asociados a un recurso. En primer lugar, debe desasociar todos los recursos de dirección IP pública que tienen asignadas direcciones IP del prefijo. Para obtener más información sobre la desasociación de direcciones IP públicas, vea [Administración de direcciones IP públicas](virtual-network-public-ip-address.md#view-modify-settings-for-or-delete-a-public-ip-address).
- Los prefijos de dirección IP pública no son compatibles actualmente con la **preferencia de enrutamiento** de *Internet* o el **nivel** *global* (para el equilibrio de carga entre regiones).
- IPv6 se admite en direcciones IP públicas básicas solo con la asignación **dinámica**. La asignación dinámica significa que la dirección IPv6 cambiará si elimina y vuelve a implementar el recurso en Azure. 
- Las direcciones IP públicas IPv6 estándar admiten la asignación estática (reservada). 
- Los equilibradores de carga internos estándar admiten la asignación dinámica desde dentro de la subred a la que están asignados.  

## <a name="pricing"></a>Precios
 
Para los costos asociados con el uso de direcciones IP públicas de Azure, tanto direcciones IP individuales como intervalos IP, consulte [Precios de las direcciones IP públicas](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="next-steps"></a>Pasos siguientes

- [Creación](manage-public-ip-address-prefix.md) de un prefijo de dirección IP pública
