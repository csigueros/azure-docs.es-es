---
author: alexeyo26
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 03/19/2021
ms.author: alexeyo
ms.openlocfilehash: 201aaae4089790bce6a697eccc7b131b3ad0d17e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453993"
---
## <a name="private-endpoints-and-virtual-network-service-endpoints"></a>Puntos de conexión privados y puntos de conexión de servicio de red virtual

Azure proporciona puntos de conexión privados y puntos de conexión de servicio de red virtual para el tráfico que se tuneliza a través de la [red troncal privada de Azure](https://azure.microsoft.com/global-infrastructure/global-network/). El propósito y las tecnologías subyacentes de estos tipos de punto de conexión son similares, pero hay diferencias entre las dos tecnologías. Se recomienda conocer las ventajas y desventajas de ambas antes de diseñar la red.

Hay ciertos aspectos que se deben tener en cuenta al decidir qué tecnología se va a usar:
- Ambas tecnologías garantizan que el tráfico entre la red virtual y el recurso de Voz no viaje a través de la red pública de Internet.
- Un punto de conexión privado proporciona una dirección IP privada dedicada para el recurso de voz. Esta dirección IP solo es accesible dentro de una red virtual y una subred específicas. Tiene control total sobre el acceso a esta dirección IP dentro de su infraestructura de red.
- Los puntos de conexión de servicio de red virtual no proporcionan una dirección IP privada dedicada para el recurso de Voz. En su lugar, encapsulan todos los paquetes enviados al recurso de Voz y los entregan directamente a través de la red troncal de Azure.
- Ambas tecnologías admiten escenarios locales. De manera predeterminada, cuando usan los puntos de conexión de servicio de red virtual, no se puede acceder desde redes locales a los recursos de servicio de Azure protegidos en redes virtuales. Pero puede [cambiar ese comportamiento](../../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-service-access-from-on-premises).
- Los puntos de conexión de servicio de red virtual se usan a menudo para restringir el acceso de un recurso de Voz en función de las redes virtuales desde las que se origina el tráfico.
- En el caso de Cognitive Services, la habilitación del punto de conexión de servicio de red virtual fuerza el tráfico de todos los recursos de Cognitive Services a través de la red troncal privada. Esto requiere una configuración explícita de acceso a la red. (Para más información, consulte [Configuración de las redes virtuales y las opciones de redes del recurso de Voz](../speech-service-vnet-service-endpoint.md#configure-virtual-networks-and-the-speech-resource-networking-settings)). Los puntos de conexión privados no tienen esta limitación y proporcionan más flexibilidad para la configuración de la red. Puede acceder a un recurso a través de la red troncal privada y otro a través de la red pública de Internet mediante la misma subred de la misma red virtual.
- Los puntos de conexión privados acarrean [costos adicionales](https://azure.microsoft.com/pricing/details/private-link). Los puntos de conexión de servicio de red virtual son gratis.
- Los puntos de conexión privados requieren una [configuración de DNS adicional](../speech-services-private-link.md#turn-on-private-endpoints).
- Un recurso de Voz puede funcionar simultáneamente con puntos de conexión privados y puntos de conexión de servicio de red virtual.

Se recomienda probar ambos tipos de puntos de conexión antes de decidir el diseño de producción. 

Para obtener más información, vea estos recursos:

- [Documentación de Azure Private Link y puntos de conexión privados](../../../private-link/private-link-overview.md)
- [Documentación de puntos de conexión de servicio de red virtual](../../../virtual-network/virtual-network-service-endpoints-overview.md)
