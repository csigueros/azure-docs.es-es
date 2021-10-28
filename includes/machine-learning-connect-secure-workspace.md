---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 966232f893ef404c3c4d6e5d3bc4929f74d7bd62
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215151"
---
Para conectarse a un área de trabajo protegida en una red virtual, use uno de los métodos siguientes:

* [Azure VPN Gateway:](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md) conecta redes locales a la VNet mediante una conexión privada. La conexión se realiza a través de la red pública de Internet. Hay dos tipos de puertas de enlace de VPN que puede usar:

    * [De punto a sitio](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md): cada equipo cliente usa un cliente VPN para conectarse a la red virtual.
    * [De sitio a sitio](../articles/vpn-gateway/tutorial-site-to-site-portal.md): un dispositivo VPN conecta la red virtual a la red local.

* [ExpressRoute:](https://azure.microsoft.com/services/expressroute/) conecta redes locales a la nube mediante una conexión privada. La conexión se realiza mediante un proveedor de conectividad.
* [Azure Bastion:](../articles/bastion/bastion-overview.md) en este escenario, se crea una máquina virtual de Azure (a veces denominada "jump box") dentro de la VNet. A continuación, se conecta a la máquina virtual mediante Azure Bastion. Bastion permite conectarse a la máquina virtual mediante una sesión RDP o SSH desde el explorador web local. A continuación, use la jump box como entorno de desarrollo. Puesto que está dentro de la VNet, puede acceder directamente al área de trabajo. Para obtener un ejemplo de cómo usar un jumpbox, vea [Tutorial: Creación de un área de trabajo segura](../articles/machine-learning/tutorial-create-secure-workspace.md).

> [!IMPORTANT]
> Al usar una __puerta de enlace de VPN__ o __ExpressRoute__, deberá planear cómo funciona la resolución de nombres entre los recursos locales y los de la red virtual. Para obtener más información, vea [Creación de un servidor DNS personalizado](../articles/machine-learning/how-to-custom-dns.md).