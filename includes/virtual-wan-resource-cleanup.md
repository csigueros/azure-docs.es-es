---
author: cherylmc
ms.author: cherylmc
ms.date: 10/15/2021
ms.service: virtual-wan
ms.topic: include
ms.openlocfilehash: 5cac86b599e7e52cbc6018de6d97392bf28e4608
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520506"
---
1. Abra la red WAN virtual que ha creado.

1. Seleccione un centro de conectividad virtual asociado a la WAN virtual para abrir su página.

1. Elimine todas las entidades de puerta de enlace siguiendo el orden que se indica a continuación para el tipo de puerta de enlace. Esta operación puede tardar hasta 30 minutos.

    **VPN:**  
   1. Desconecte los sitios de VPN  
   1. Elimine las conexiones de VPN  
   1. Elimine las puertas de enlace de VPN  

    **ExpressRoute:**  
   1. Elimine las conexiones de ExpressRoute  
   1. Elimine las puertas de enlace de ExpressRoute  

1. Puede eliminar el centro de conectividad en este momento o más adelante, cuando elimine el grupo de recursos.

1. Repita el procedimiento para todos los centros de conectividad asociados a la WAN virtual.

1. En Azure Portal, vaya al grupo de recursos.

1. Seleccione **Eliminar grupo de recursos**. Así se elimina todo lo que haya en el grupo de recursos, incluidos los centros de conectividad y la WAN virtual.