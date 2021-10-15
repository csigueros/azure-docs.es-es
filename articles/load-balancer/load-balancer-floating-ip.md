---
title: Configuración de la dirección IP flotante de Azure Load Balancer
description: Información general de la dirección IP flotante de Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: a72eceba85e14e058fa7c8ba2b109009e46e6df3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357460"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Configuración de la dirección IP flotante de Azure Load Balancer

Load Balancer proporciona varias funcionalidades para las aplicaciones UDP y TCP.

## <a name="floating-ip"></a>Dirección IP flotante

Algunos escenarios de aplicación prefieren o requieren que varias instancias de la aplicación usen el mismo puerto en una sola máquina virtual en el grupo back-end. Entre los ejemplos comunes de reutilización de puertos se incluyen los siguientes: 
- agrupación en clústeres para alta disponibilidad
- aplicaciones virtuales de red
- exposición de varios puntos de conexión TLS sin volver a cifrar. 

Si desea reutilizar el puerto back-end en varias reglas, debe habilitar la IP flotante en la definición de la regla.

Cuando se habilita la IP flotante, Azure cambia la asignación de direcciones IP a la dirección IP de front-end del front-end de Load Balancer en lugar de la dirección IP de la instancia de back-end. 

Sin la dirección IP flotante, Azure expone la IP de las instancias de máquina virtual. Habilitar la dirección IP flotante cambia el esquema de asignación de direcciones IP a la IP de servidor front-end del equilibrador de carga para permitir una flexibilidad adicional. Obtenga más información [aquí](load-balancer-multivip-overview.md).

## <a name="limitations"></a><a name = "limitations"></a>Limitaciones

- La dirección IP flotante no se admite actualmente en las configuraciones de IP secundarias para los escenarios de equilibrio de carga.

## <a name="next-steps"></a>Pasos siguientes

- Consulte el artículo sobre cómo [crear una instancia de Standard Load Balancer pública](quickstart-load-balancer-standard-public-portal.md) para empezar a usar un equilibrador de carga.
- Información sobre [Conexiones salientes de Azure Load Balancer](load-balancer-outbound-connections.md).
- Más información sobre [Azure Load Balancer](load-balancer-overview.md).
- Más información sobre [sondeos de mantenimiento](load-balancer-custom-probe-overview.md).
- Más información acerca de los [diagnósticos de Load Balancer Estándar](load-balancer-standard-diagnostics.md).
- Más información sobre los [grupos de seguridad de red](../virtual-network/network-security-groups-overview.md).
