---
title: SKU de Azure Load Balancer
description: Información general de las SKU de Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2021
ms.author: allensu
ms.openlocfilehash: 5000ac68cc0e00cdbe9d0ebd430f8cd88fe49e98
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057711"
---
# <a name="azure-load-balancer-skus"></a>SKU de Azure Load Balancer

Azure Load Balancer tiene dos SKU.

## <a name="sku-comparison"></a><a name="skus"></a>Comparación de SKU

Load balancer admite SKU estándar y básicas. Estas SKU difieren en la escala, las características y los precios del escenario. Cualquier escenario que sea posible con Basic Load Balancer se puede crear con Standard Load Balancer.

Para comparar y comprender las diferencias, consulte la siguiente tabla. Para más información, consulte [Información general sobre Standard Load Balancer de Azure](./load-balancer-overview.md).

>[!NOTE]
> Microsoft recomienda Standard Load Balancer.
Las máquinas virtuales independientes, los conjuntos de disponibilidad y los conjuntos de escalado de máquinas virtuales solo se pueden conectar a una SKU, nunca a ambas. Las SKU de Load Balancer y de la IP pública deben coincidir cuando se usan con direcciones IP públicas. Las SKU de Load Balancer y de la IP pública no son mutables.

| | Standard Load Balancer | Versión Básico de Load Balancer |
| --- | --- | --- |
| **Tipo de back-end** | Basado en IP, basado en NIC | Basado en NIC |
| **[Tamaño de grupo de back-end](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer)** | Admite hasta 1000 instancias. | Admite hasta 300 instancias. |
| **Puntos de conexión del grupo de back-end** | Todas las máquinas virtuales o conjuntos de escalado de máquinas virtuales de una red virtual individual. | Máquinas virtuales en un único conjunto de disponibilidad o conjunto de escalado de máquinas virtuales. |
| **[Sondeos de estado](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[Comportamiento del sondeo de mantenimiento](./load-balancer-custom-probe-overview.md#probedown)** | Las conexiones TCP permanecen activas en el sondeo de la instancia __y__ en todos los sondeos. | Las conexiones TCP permanecen activas en un sondeo de instancia. Todas las conexiones TCP terminan cuando todos los sondeos están inactivos. |
| **Zonas de disponibilidad** | Servidores front-end con redundancia de zona y zonales para el tráfico de entrada y salida. | No disponible |
| **Diagnóstico** | [Métricas multidimensionales de Azure Monitor](./load-balancer-standard-diagnostics.md) | No compatible |
| **Puertos de alta disponibilidad** | [Disponibles para el equilibrador de carga interno](./load-balancer-ha-ports-overview.md) | No disponible |
| **Seguro de forma predeterminada** | Cerrado a los flujos de entrada, a menos que lo permita un grupo de seguridad de red. Se permite el tráfico interno desde la red virtual al equilibrador de carga interno. | Abrir de forma predeterminada. Grupo de seguridad de red opcional. |
| **Reglas de salida** | [Configuración declarativa de NAT de salida](./load-balancer-outbound-connections.md#outboundrules) | No disponible |
| **Restablecimiento de TCP en tiempo de espera de inactividad** | [Disponible en cualquier regla](./load-balancer-tcp-reset.md) | No disponible |
| **[Varios servidores front-end](./load-balancer-multivip-overview.md)** | Entrada y [salida](./load-balancer-outbound-connections.md) | Solo de entrada |
| **Operaciones de administración** | La mayoría de las operaciones en menos de 30 segundos | Normalmente, entre 60 y 90 segundos |
| **Acuerdo de Nivel de Servicio** | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | No disponible | 
| **Compatibilidad con Emparejamiento de VNET global** | Se admite ILB estándar a través del Emparejamiento de VNET global | No compatible | 

Para más información, consulte [Límites del equilibrador de carga](../azure-resource-manager/management/azure-subscription-service-limits.md#load-balancer). Para más información de Load Balancer Estándar, consulte los artículos de [introducción](./load-balancer-overview.md), [precios](https://aka.ms/lbpricing) y [Acuerdo de Nivel de Servicio](https://aka.ms/lbsla).

## <a name="limitations"></a>Limitaciones

- Puede [actualizar SKU de Load Balancer](upgrade-basic-standard.md).
- Un recurso de máquina virtual independiente, un recurso de conjunto de disponibilidad o un recurso de conjunto de escalado de máquinas virtuales puede hacer referencia únicamente a una SKU, nunca a ambas.
- [Operaciones de movimiento](../azure-resource-manager/management/move-resource-group-and-subscription.md):
  - Las operaciones de movimiento de grupos de recursos (dentro de la misma suscripción) **se admiten** para Standard Load Balancer y las direcciones IP pública estándar. 
  - Las [operaciones de movimiento de suscripción](../azure-resource-manager/management/move-support-resources.md) **no** se admiten en Standard Load Balancer ni en los recursos de direcciones IP públicas estándar.

## <a name="next-steps"></a>Pasos siguientes

- Consulte el artículo sobre cómo [crear una instancia de Standard Load Balancer pública](quickstart-load-balancer-standard-public-portal.md) para empezar a usar un equilibrador de carga.
- Más información en [Standard Load Balancer y Availability Zones](load-balancer-standard-availability-zones.md).
- Más información sobre [sondeos de mantenimiento](load-balancer-custom-probe-overview.md).
- Más información acerca de cómo usar [Load Balancer para conexiones salientes](load-balancer-outbound-connections.md).
- Más información acerca de [Standard Load Balancer con reglas de equilibrio de carga para puertos HA](load-balancer-ha-ports-overview.md).
- Más información sobre los [grupos de seguridad de red](../virtual-network/network-security-groups-overview.md).
