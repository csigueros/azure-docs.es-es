---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: d6ee1a0c747681d6da85ae1275a09d6ad5443b27
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114472434"
---
> [!NOTE]
> Azure proporciona una dirección IP de acceso de salida predeterminado para las instancias de Azure Virtual Machines que no tienen asignada una dirección IP pública o que se encuentran en el grupo de back-end de una instancia de Azure Load Balancer del nivel Básico. El mecanismo de dirección IP de acceso de salida predeterminado proporciona una dirección IP de salida que no se puede configurar. 
>
>La dirección IP de acceso de salida predeterminado se deshabilita cuando se asigna una dirección IP pública a la máquina virtual o cuando se coloca la máquina virtual en el grupo de back-end de una instancia de Load Balancer Estándar con o sin reglas de salida. Si se asigna un recurso de puerta de enlace de [Azure Virtual Network NAT](../articles/virtual-network/nat-overview.md) a la subred de la máquina virtual, la dirección IP de acceso de salida predeterminado se deshabilita.
>
> Para más información sobre las conexiones salientes en Azure, consulte [Uso de la Traducción de direcciones de red de origen (SNAT) para conexiones salientes](../articles/load-balancer/load-balancer-outbound-connections.md).