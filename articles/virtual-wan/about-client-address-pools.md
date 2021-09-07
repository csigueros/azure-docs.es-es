---
title: Acerca de los grupos de direcciones de cliente para VPN de usuario P2S
titleSuffix: Azure Virtual WAN
description: Obtenga información sobre los grupos de direcciones de cliente para VPN de usuario P2S.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/29/2021
ms.author: cherylmc
ms.openlocfilehash: 58b642eeff7b20ccb04e2b5817f8f5a8ee5bcc2c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780840"
---
# <a name="about-client-address-pools-for-point-to-site-configurations"></a>Acerca de los grupos de direcciones de cliente para configuraciones de punto a sitio

En este artículo se describen la guía y los requisitos de Virtual WAN para asignar espacios de direcciones de cliente cuando **Unidades de escalado de la puerta de enlace** de punto a sitio del centro virtual son 40 o más.

Las puertas de enlace de VPN de punto a sitio en el centro de conectividad de Virtual WAN se implementan con varias instancias. Cada instancia de una puerta de enlace de VPN de punto a sitio admite hasta 10 000 conexiones de usuario de punto a sitio simultáneas. Como consecuencia, en el caso de las unidades de escalado superiores a 40, Virtual WAN necesita implementar capacidad adicional, lo cual requiere la asignación de un número mínimo de grupos de direcciones para diferentes unidades de escalado.

Por ejemplo, si se elige una unidad de escalado de 100, se implementarán 5 instancias para la puerta de enlace de VPN de punto a sitio en el centro virtual. Esta implementación admite hasta 50 000 conexiones simultáneas y **al menos** 5 grupos de direcciones diferentes.

**Unidades de escalado disponibles**

| Unidad de escalado | Número máximo de clientes compatibles | Número mínimo de grupos de direcciones |
|--- |--- |--- |
| 40 | 20000 | 2 |
| 60 | 30000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100000 | 10 |

## <a name="specifying-address-pools"></a><a name="specify-address-pools"></a>Especificación de grupos de direcciones

Virtual WAN realiza automáticamente las asignaciones de grupos de direcciones VPN de punto a sitio. Consulte la siguiente guía básica para conocer cómo especificar grupos de direcciones.

* Una instancia de puerta de enlace permite 10 000 conexiones simultáneas como máximo. Por lo tanto, cada grupo de direcciones debe contener al menos 10 000 direcciones IP RFC1918 únicas.
* Se combinan automáticamente varios intervalos de grupos de direcciones y se asignan a una única instancia de puerta de enlace **única**. Este proceso se lleva a cabo con un enfoque Round Robin para cualquier instancia de puerta de enlace que tenga menos de 10 000 direcciones IP. Por ejemplo, un grupo con 5000 direcciones se puede combinar automáticamente mediante Virtual WAN con otro grupo que tenga 8000 direcciones y esté asignado a una sola instancia de puerta de enlace.
* Un único grupo de direcciones solo se asigna a una única instancia de puerta de enlace mediante Virtual WAN.
* Los grupos de direcciones deben ser distintos. No pueden superponerse.

> [!NOTE] 
> Si un grupo de direcciones está asociado a una instancia de puerta de enlace que recibiendo mantenimiento, el grupo de direcciones no se puede reasignar a otra instancia.

### <a name="example"></a>Ejemplo 

En el ejemplo siguiente se describe un caso en la que 60 unidades de escalado admiten hasta 30 000 conexiones, pero los grupos de direcciones asignadas dan como resultado menos de 30 000 conexiones simultáneas.

El número total de conexiones simultáneas que se admiten en esta configuración es 28 192. La primera instancia de puerta de enlace admite 10 000 direcciones; la segunda, 8192 conexiones, y la tercera también admite 10 000 direcciones.

| Número de grupos de direcciones | Grupo de direcciones | Conexiones admitidas |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | 10.14.0.0/15 | 10000|

#### <a name="recommendations"></a>Recomendaciones

* Asegúrese de que el grupo de direcciones n.º 2 tenga al menos 10 000 direcciones IP distintas. (Ejemplo: 10.13.0.0/15).
* Agregue un grupo de direcciones más. (Ejemplo: el grupo de direcciones n.º 4 10.15.0.0/21, con 2048 direcciones). Los grupos de direcciones 2 y 4 se combinarán automáticamente y permitirán que la instancia de puerta de enlace admita 10 000 conexiones simultáneas.

## <a name="configure-or-modify-this-setting"></a>Configuración o modificación de este valor

Este valor se configura en la página **De punto a sitio** al crear el centro virtual. Puede modificarlo más adelante.

Para modificar este valor:

[!INCLUDE [Modify client address pool](../../includes/virtual-wan-client-address-pool-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Para ver los pasos de configuración, consulte [Configuración de VPN de usuario P2S](virtual-wan-point-to-site-portal.md).
