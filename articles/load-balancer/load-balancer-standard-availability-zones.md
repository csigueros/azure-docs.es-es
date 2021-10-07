---
title: Azure Load Balancer y Availability Zones
titleSuffix: Azure Load Balancer
description: Con esta ruta de aprendizaje, empiece a usar Azure Standard Load Balancer y Availability Zones.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 87a7d10c9748a2e173d8f43dcca3611666277792
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653712"
---
# <a name="load-balancer-and-availability-zones"></a>Load Balancer y Availability Zones

Azure Load Balancer admite escenarios de zonas de disponibilidad. Puede usar Standard Load Balancer para aumentar la disponibilidad en todo el escenario mediante la alineación de recursos con zonas y su distribución entre ellas.  Revise este documento para conocer estos conceptos y la guía de diseño de un escenario básico.

Una instancia de Load Balancer puede ser con **redundancia de zona, zonal** o **no zonal**. Para configurar las propiedades relacionadas con la zona (mencionadas anteriormente) para el equilibrador de carga, seleccione el tipo de front-end adecuado que se requiera.

## <a name="zone-redundant"></a>Redundancia de zona

En una región con Availability Zones, una instancia de Standard Load Balancer puede tener redundancia de zona. El tráfico se sirve mediante una dirección IP única.

Una única dirección IP de servidor front-end sobrevivirá a los errores de zona. Se puede usar la IP de front-end para llegar a todos los miembros del grupo de back-end (no afectados), independientemente de la zona. Se puede producir un error en una o más zonas de disponibilidad y la ruta de acceso de datos sobrevive siempre que una zona de la región permanezca correcta.

La dirección IP del front-end se suministra a la vez en varias implementaciones de infraestructura independientes de varias zonas de disponibilidad. Los reintentos o el restablecimiento se realizarán correctamente en otras zonas a las que no haya afectado por el error de zona.

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="Virtual Network NAT">
</p>

*Ilustración: Equilibrador de carga con redundancia de zona*

## <a name="zonal"></a>Zonal

Puede optar por tener un front-end garantizado para una sola zona, lo que se conoce como *zonal*.  Este escenario significa que cualquier flujo de entrada o de salida es atendido por una sola zona en una región.  El front-end comparte destino con el estado de la zona.  La ruta de acceso de datos no se ve afectada por errores en zonas distintas a la garantizada. Puede utilizar front-end zonales para exponer una dirección IP por cada zona de disponibilidad.  

Además, se admite el uso de servidores front-end zonales directamente para puntos de conexión con equilibrio de carga dentro de cada zona. Puede usar esta configuración para exponer puntos de conexión con equilibrio de carga por zona para supervisar de forma individual cada zona. En el caso de los puntos de conexión públicos, puede integrarlos con un producto de equilibrio de carga de DNS como [Traffic Manager](../traffic-manager/traffic-manager-overview.md) y usar un nombre DNS único.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="Virtual Network NAT">
</p>

*Ilustración: Equilibrador de carga zonal*

En el caso de un servidor front-end de equilibrador de carga público, se agrega un parámetro de **zonas** a la dirección IP pública. La configuración IP del servidor front-end que usa la regla correspondiente hace referencia a esta dirección IP pública.

En el caso de un servidor front-end de equilibrador de carga interno, se agrega un parámetro de **zonas** a la configuración IP del front-end del recurso de equilibrador de carga interno. Un servidor front-end zonal garantiza una dirección IP en una subred a una zona específica.

## <a name="non-zonal"></a>No zonal

Los equilibradores de carga también se pueden crear en una configuración no zonal mediante un front-end "sin zona" (dirección IP pública o prefijo de dirección IP pública).  Esta opción no ofrece una garantía de redundancia. Tenga en cuenta que todas las direcciones IP públicas que [se actualicen](https://docs.microsoft.com/azure/virtual-network/public-ip-upgrade-portal) de una SKU Básica a otra Estándar serán de tipo "sin zona".

## <a name="design-considerations"></a><a name="design"></a> Consideraciones de diseño

Ahora que ya conoce las propiedades relacionadas con las zonas de Standard Load Balancer, las consideraciones de diseño siguientes pueden ayudarle a la hora de diseñar para lograr una alta disponibilidad.

### <a name="tolerance-to-zone-failure"></a>Tolerancia a errores de zona

- Una instancia de Load Balancer **con redundancia de zona** puede servir a un recurso zonal en cualquier zona con una dirección IP.  La IP puede sobrevivir a uno o más errores de zona, siempre que al menos una de las zonas tenga un estado correcto dentro de la región.
- Un front-end **zonal** es una reducción del servicio a una única zona y comparte el mismo destino que la zona correspondiente. Si la zona en la que se encuentra la implementación deja de estar activa, la implementación no sobrevivirá a este error.

Se recomienda usar Load Balancer con redundancia de zona para las cargas de trabajo de producción.

### <a name="multiple-frontends"></a>Varios servidores front-end

El uso de varios front-ends le permite equilibrar la carga del tráfico en más de un puerto o dirección IP.  Al diseñar la arquitectura, es importante tener en cuenta la forma en la que pueden interactuar la redundancia de zona y varios front-ends.  Tenga en cuenta que si el objetivo es hacer que cada front-end sea resistente a errores, todas las direcciones IP que se usan asignadas como front-ends deben tener redundancia de zona.   Si un conjunto de front-ends está pensado para asociarse a una sola zona, todas las direcciones IP de ese conjunto se deben asociar a esa zona específica.  No es necesario tener un equilibrador de carga para cada zona; en su lugar, cada front-end zonal (o conjunto de front-ends zonales) se podría asociar con máquinas virtuales del grupo de back-end que forman parte de esa zona de disponibilidad específica.

### <a name="transition-between-regional-zonal-models"></a>Transición entre modelos zonales regionales

En el caso de que una región se amplíe para que tenga [zonas de disponibilidad](https://docs.microsoft.com/azure/availability-zones/az-overview), las direcciones IP de front-end existentes permanecerán no zonales. Para asegurarse de que la arquitectura puede aprovechar las ventajas de las nuevas zonas, se recomienda crear nuevas direcciones IP de front-end y replicar las reglas y configuraciones adecuadas para usar estas nuevas direcciones IP públicas.

### <a name="control-vs-data-plane-implications"></a>Implicaciones de los planos de control frente a los planos de datos

La redundancia de zona no implica un plano de control o un plano de datos sin incidencias. Los flujos con redundancia de zona pueden usar cualquier zona y los flujos que use utilizarán todas las zonas correctas de una región. En el caso de un error de zona, los flujos de tráfico que usan zonas correctas no se ven afectados.

Los flujos de tráfico que están usando una zona en el momento en que se produce un error en esta pueden resultar afectados, pero las aplicaciones se pueden recuperar. El tráfico continúa por las zonas correctas de la región después de la retransmisión cuando Azure ha detectado el error en la zona.

Revise [los patrones de diseño en la nube de Azure](/azure/architecture/patterns/) para mejorar la resistencia de la aplicación a los escenarios de error.

## <a name="limitations"></a>Limitaciones

* Las zonas no se pueden cambiar, actualizar ni crear para el recurso después de su creación.
* Los recursos no se pueden actualizar de zonal a con redundancia de zona y viceversa después de su creación.

## <a name="next-steps"></a>Pasos siguientes
- Aprenda más sobre [zonas de disponibilidad](../availability-zones/az-overview.md).
- Más información sobre [Load Balancer Estándar](./load-balancer-overview.md)
- Más información sobre cómo [equilibrar la carga de las máquinas virtuales dentro de una zona con una instancia de Standard Load Balancer zonal](./quickstart-load-balancer-standard-public-cli.md)
- Más información sobre cómo [equilibrar la carga de las máquinas virtuales entre zonas con una instancia de Standard Load Balancer con redundancia de zona](./quickstart-load-balancer-standard-public-cli.md)
- Más información sobre [los patrones de diseño en la nube de Azure](/azure/architecture/patterns/) para mejorar la resistencia de la aplicación a los escenarios de error.
