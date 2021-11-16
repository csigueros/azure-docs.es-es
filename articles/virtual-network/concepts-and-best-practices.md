---
title: Conceptos y procedimientos recomendados de Azure Virtual Network
description: Obtenga más información sobre los conceptos y procedimientos recomendados de Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/03/2020
ms.author: kumud
ms.openlocfilehash: be9bae5d67b7ce341e7444a183788ab1f158ce72
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130217456"
---
# <a name="azure-virtual-network-concepts-and-best-practices"></a>Conceptos y procedimientos recomendados de Azure Virtual Network

En este artículo se describen los conceptos clave y los procedimientos recomendados de Azure Virtual Network.

## <a name="vnet-concepts"></a>Conceptos de VNet

- **Espacio de direcciones**: Al crear una instancia de VNet tiene que especificar un espacio de direcciones IP privado personalizado mediante direcciones públicas y privadas (RFC 1918). Azure asigna a los recursos de una red virtual una dirección IP privada desde el espacio de direcciones que asigne. Por ejemplo, si implementa una máquina virtual en una red virtual con el espacio de direcciones, 10.0.0.0/16, la máquina virtual se asignará a una dirección IP privada como 10.0.0.4.
- **Subredes:** Las subredes le permiten segmentar la red virtual en una o varias subredes y asignar una parte del espacio de direcciones de la red virtual para cada subred. A continuación, puede implementar los recursos de Azure en una subred específica. Al igual que en una red tradicional, las subredes permiten segmentar el espacio de direcciones de red virtual en segmentos que sean adecuados para la red interna de la organización. Esto también mejora la eficacia de la asignación de dirección. Puede proteger los recursos dentro de las subredes mediante grupos de seguridad de red. Para más información, consulte[Grupo de seguridad de red](./network-security-groups-overview.md).
- **Regiones**: El ámbito de VNet es una sola región o ubicación; sin embargo, se pueden conectar entre sí varias redes virtuales de diferentes regiones mediante el emparejamiento de red virtual.
- **Subscription** (Suscripción): VNet limita su ámbito a una suscripción. Puede implementar varias redes virtuales dentro de cada [suscripción](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) y [región](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) de Azure.

## <a name="best-practices"></a>Procedimientos recomendados

Al crear la red en Azure, es importante tener en cuenta los siguientes principios universales de diseño:

- Asegúrese de que los espacios de dirección no se superponen. Asegúrese de que el espacio de direcciones (bloque CIDR) de red virtual no se superponen con otros intervalos de red de la organización.
- Las subredes no deben cubrir el espacio de direcciones completo de la red virtual. Planee con antelación y reserve algún espacio de direcciones para el futuro.
- Se recomienda que tenga menos redes virtuales de gran tamaño, en lugar de varias redes virtuales pequeñas. Esto impedirá la sobrecarga de administración.
- Proteja las redes virtuales mediante la asignación de grupos de seguridad de red (NSG) a las subredes situadas debajo de ellas. Para obtener más información sobre los conceptos de seguridad de red, vea [Introducción a la seguridad de red de Azure](../security/fundamentals/network-overview.md).

## <a name="next-steps"></a>Pasos siguientes

 Para empezar a usar una red virtual, créela, implemente en ella varias máquinas virtuales y establezca comunicación entre las máquinas virtuales. Para aprender a hacerlo, consulte la guía de inicio rápido [Creación de una red virtual](quick-create-portal.md).
