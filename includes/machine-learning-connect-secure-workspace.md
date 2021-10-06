---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 08/26/2021
ms.author: larryfr
ms.openlocfilehash: 3bd72c4930c510df03856ad31bbea51d21829c1e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646120"
---
Para conectarse a un área de trabajo protegida en una red virtual, use uno de los métodos siguientes:

* [Azure VPN Gateway:](/azure/vpn-gateway/vpn-gateway-about-vpngateways) conecta redes locales a la VNet mediante una conexión privada. La conexión se realiza mediante la red pública de Internet. Hay dos tipos de puertas de enlace de VPN que puede usar:

    * [De punto a sitio](/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal): cada equipo cliente usa un cliente VPN para conectarse a la red virtual.
    * [De sitio a sitio](/azure/vpn-gateway/tutorial-site-to-site-portal): un dispositivo VPN conecta la red virtual a la red local.

* [ExpressRoute:](https://azure.microsoft.com/services/expressroute/) conecta redes locales a la nube mediante una conexión privada. La conexión se realiza mediante un proveedor de conectividad.
* [Azure Bastion:](/azure/bastion/bastion-overview) en este escenario, se crea una máquina virtual de Azure (a veces denominada "jump box") dentro de la VNet. A continuación, se conecta a la máquina virtual mediante Azure Bastion. Bastion permite conectarse a la máquina virtual mediante una sesión RDP o SSH desde el explorador web local. A continuación, use la jump box como entorno de desarrollo. Puesto que está dentro de la VNet, puede acceder directamente al área de trabajo. Para obtener un ejemplo de cómo usar un jumpbox, vea [Tutorial: Creación de un área de trabajo segura](/azure/machine-learning/tutorial-create-secure-workspace).

> [!IMPORTANT]
> Al usar una __puerta de enlace de VPN__ o __ExpressRoute__, deberá planear cómo funciona la resolución de nombres entre los recursos locales y los de la red virtual. Para obtener más información, vea [Creación de un servidor DNS personalizado](/azure/machine-learning/how-to-custom-dns).
