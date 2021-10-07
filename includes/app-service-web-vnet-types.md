---
author: madsd
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: madsd
ms.openlocfilehash: 8d4c2968e3b21269d233f584747856804f8ff9c6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641942"
---
* Los sistemas multiinquilino que admiten todos los planes de precios, excepto Aislado.
* App Service Environment, que se implementa en la VNet y admite aplicaciones de planes de precios aislados.

La característica Integración con red virtual se usa en aplicaciones multiinquilino. Si la aplicación está en [App Service Environment][ASEintro], ya está en una red virtual y no requiere el uso de la característica Integración con red virtual para acceder a recursos en la misma red virtual. Para obtener más información sobre todas las características de redes, consulte [Características de redes de App Service][Networkingfeatures].

Integración con red virtual proporciona a la aplicación el acceso a los recursos de la VNet, pero no concede el acceso privado de entrada a la aplicación desde la VNet. El acceso privado a sitios se refiere a que solo se puede acceder a la aplicación desde una red privada; por ejemplo, desde dentro de una red virtual de Azure. Integración con red virtual solo se usa para realizar llamadas de salida desde la aplicación a la VNet. La característica Integración con red virtual se comporta de forma diferente cuando se usa con las VNet de la misma región y con las VNet de otras regiones. Esta característica tiene dos variantes:

* **Integración con red virtual regional**: cuando se conecta a redes virtuales de Azure Resource Manager en la misma región, debe tener una subred dedicada en la VNet con la que se va a realizar la integración.
* **Integración con red virtual con requisito de puerta de enlace**: para conectarse a las redes virtuales de otras regiones o a una red virtual clásica de la misma región, necesita una puerta de enlace de Azure Virtual Network aprovisionada en la red virtual de destino.

Integración con red virtual tiene las siguientes características:

* Necesita un plan de precios Estándar, Prémium, Prémium v2, Prémium v3 o Elástico Prémium.
* Es compatible con TCP y UDP.
* Funciona con aplicaciones de Azure App Service y de funciones.

Hay algunos aspectos que Integración con red virtual no admite, como los siguientes:

* Montar una unidad.
* Integración de Windows Server Active Directory.
* NetBIOS.

Cuando Integración con red virtual necesita una puerta de enlace, solo proporciona acceso a los recursos de la VNet de destino o de las redes conectadas a esta que tienen emparejamiento o redes privadas virtuales. Integración con red virtual con requisito de puerta de enlace no permite acceder a los recursos disponibles en las conexiones de Azure ExpressRoute ni trabajar con puntos de conexión de servicio.

Independientemente de la versión que se use, Integración con red virtual ofrece a la aplicación el acceso a los recursos de la VNet, pero no concede el acceso privado de entrada a la aplicación desde la VNet. El acceso privado a sitios se refiere a que solo se puede acceder a la aplicación desde una red privada (por ejemplo, desde dentro de una VNet de Azure). Integración con red virtual solo sirve para realizar llamadas salientes desde la aplicación hacia la red virtual.

<!--Links-->
[ASEintro]: ../articles/app-service/environment/intro.md
[Networkingfeatures]: ../articles/app-service/networking-features.md
