---
title: Introducción a los grupos de seguridad de aplicaciones de Azure
titlesuffix: Azure Virtual Network
description: Aprenda a usar los grupos de seguridad de aplicaciones.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: ea9d59629ac843020ef8aa29e9140552b26b13be
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725953"
---
# <a name="application-security-groups"></a>Grupos de seguridad de aplicaciones

Los grupos de seguridad de aplicaciones le permiten configurar la seguridad de red como una extensión natural de la estructura de una aplicación, lo que le permite agrupar máquinas virtuales y directivas de seguridad de red basadas en esos grupos. Puede reutilizar la directiva de seguridad a escala sin mantenimiento manual de direcciones IP explícitas. La plataforma controla la complejidad de las direcciones IP explícitas y de varios conjuntos de reglas, lo que le permite centrarse en su lógica de negocios. Para entender mejor los grupos de seguridad de aplicaciones, considere el ejemplo siguiente:

![Grupos de seguridad de aplicaciones](./media/security-groups/application-security-groups.png)

En la imagen anterior, *NIC1* y *NIC2* son miembros del grupo de seguridad de aplicaciones *AsgWeb*. *NIC3* es miembro del grupo de seguridad de aplicaciones *AsgLogic*. *NIC4* es miembro del grupo de seguridad de aplicaciones *AsgDb*. Aunque cada interfaz de red de este ejemplo solo es miembro de un grupo de seguridad de aplicaciones, una interfaz de red puede ser miembro de varios grupos de seguridad de aplicaciones, hasta los [límites de Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Ninguna de las interfaces de red tiene un grupo de seguridad de red asociado. *NSG1* está asociado a ambas subredes y contiene las siguientes reglas:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Esta regla es necesaria para permitir el tráfico de Internet a los servidores web. Dado que la regla de seguridad predeterminada **DenyAllInbound** deniega el tráfico entrante desde Internet, no es necesaria ninguna regla adicional para los grupos de seguridad de aplicaciones *AsgLogic* y *AsgDb*.

|Priority|Source|Puertos de origen| Destination | Puertos de destino | Protocolo | Acceso |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Allow |

## <a name="deny-database-all"></a>Deny-Database-All

Dado que la regla de seguridad predeterminada **AllowVNetInBound** permite todas las comunicaciones entre los recursos de la misma red virtual, se necesita esta regla para denegar el tráfico desde todos los recursos.

|Priority|Source|Puertos de origen| Destination | Puertos de destino | Protocolo | Acceso |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Any | Denegar |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Esta regla permite el tráfico desde el grupo de seguridad de aplicaciones *AsgLogic* al grupo de seguridad de aplicaciones *AsgDb*. La prioridad de esta regla es mayor que la prioridad de la regla *Deny-Database-All*. Como resultado, esta regla se procesa antes que la regla *Deny-Database-All*, por lo que se permite el tráfico del grupo de seguridad de aplicaciones *AsgLogic*, mientras que el resto del tráfico es bloqueado.

|Priority|Source|Puertos de origen| Destination | Puertos de destino | Protocolo | Acceso |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Allow |

Las reglas que especifican un grupo de seguridad de aplicaciones como origen o destino solo se aplican a las interfaces de red que son miembros del grupo de seguridad de aplicaciones. Si la interfaz de red no es miembro de un grupo de seguridad de aplicaciones, la regla no se aplica a la interfaz de red aunque el grupo de seguridad de red esté asociado a la subred.

Los grupos de seguridad de aplicaciones presentan las siguientes restricciones:

-    Hay límites en el número de grupos de seguridad de aplicaciones que puede tener en una suscripción, así como otros límites relacionados con los grupos de seguridad de aplicaciones. Para más información, consulte el artículo acerca de los [límites de Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Todas las interfaces de red asignadas a un grupo de seguridad de aplicaciones deben existir en la misma red virtual en la que se encuentra la primera interfaz de red asignada a dicho grupo. Por ejemplo, si la primera interfaz de red asignada a un grupo de seguridad de aplicaciones llamado *AsgWeb* está en la red virtual llamada *VNet1*, todas las sucesivas interfaces de red asignadas a *ASGWeb* deben existir en *VNet1*. No se pueden agregar interfaces de red de distintas redes virtuales al mismo grupo de seguridad de aplicaciones.
- Si especifica grupos de seguridad de aplicaciones como origen y destino de una regla de seguridad, las interfaces de red de ambos grupos de seguridad de aplicaciones deben existir en la misma red virtual. Por ejemplo, si *AsgLogic* contiene interfaces de red de *VNet1* y *AsgDb* contiene interfaces de red de *VNet2*, no puede asignar *AsgLogic* como origen y *AsgDb* como destino en una regla. Todas las interfaces de red para los grupos de seguridad de aplicaciones de origen y de destino deben existir en la misma red virtual.

> [!TIP]
> Para minimizar el número de reglas de seguridad que necesita y la necesidad de cambiar las reglas, planee los grupos de seguridad de aplicaciones que necesita y cree reglas mediante etiquetas de servicio o grupos de seguridad de aplicaciones en lugar de direcciones IP individuales o intervalos de direcciones IP siempre que sea posible.

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [crear un grupo de seguridad de red](tutorial-filter-network-traffic.md).
