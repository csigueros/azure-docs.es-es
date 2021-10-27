---
author: asudbring
ms.service: virtual-network
ms.topic: include
ms.date: 07/22/2021
ms.author: allensu
ms.openlocfilehash: 35b4f49638d64a1de322fc712602a2d6cbad290a
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130019303"
---
> [!NOTE]
> Azure proporciona una dirección IP de acceso de salida predeterminado para las instancias de Azure Virtual Machines que no tienen asignada una dirección IP pública o que se encuentran en el grupo de back-end de una instancia de Azure Load Balancer del nivel Básico. El mecanismo de dirección IP de acceso de salida predeterminado proporciona una dirección IP de salida que no se puede configurar. 
>
> Para más información sobre el acceso de salida predeterminado, consulte [Acceso de salida predeterminado en Azure](../articles/virtual-network/ip-services/default-outbound-access.md).
>
>La dirección IP de acceso de salida predeterminado se deshabilita cuando se asigna una dirección IP pública a la máquina virtual o cuando se coloca la máquina virtual en el grupo de back-end de una instancia de Standard Load Balancer con o sin reglas de salida. Si se asigna un recurso de puerta de enlace de [Azure Virtual Network NAT](../articles/virtual-network/nat-gateway/nat-overview.md) a la subred de la máquina virtual, la dirección IP de acceso de salida predeterminado se deshabilita.
>
> Las máquinas virtuales creadas por conjuntos de escalado de máquinas virtuales en modo de orquestación flexible no tienen acceso de salida predeterminado.
>
> Para más información sobre las conexiones salientes en Azure, consulte [Uso de la Traducción de direcciones de red de origen (SNAT) para conexiones salientes](../articles/load-balancer/load-balancer-outbound-connections.md).