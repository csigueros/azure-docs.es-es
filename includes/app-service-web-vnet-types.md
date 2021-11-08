---
author: madsd
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: madsd
ms.openlocfilehash: 2064586fafd82cfc67e14b289797a562e3512afd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130245159"
---
* Los planes de tarifa de proceso dedicados, que incluyen los niveles Básico, Estándar, Prémium, Prémium v2 y Prémium v3.
* App Service Environment que se implementa directamente en la red virtual con una infraestructura de soporte técnico dedicada y usa los planes de tarifa Aislado y Aislado v2.

La característica de integración con red virtual se usa en planes de tarifa de proceso dedicado de App Service. Si la aplicación está en [App Service Environment](../articles/app-service/environment/overview.md), ya está en una red virtual y no requiere el uso de la característica de integración con red virtual para acceder a recursos en la misma red virtual. Para obtener más información sobre todas las características de redes, consulte [Características de redes de App Service](../articles/app-service/networking-features.md).

La integración con red virtual proporciona a la aplicación el acceso a los recursos de la VNet, pero no concede el acceso privado de entrada a la aplicación desde la VNet. El acceso privado a sitios se refiere a que solo se puede acceder a la aplicación desde una red privada; por ejemplo, desde dentro de una red virtual de Azure. La integración con red virtual solo se usa para realizar llamadas de salida desde la aplicación a la VNet. La característica de integración con red virtual se comporta de forma diferente cuando se usa con las VNet de la misma región y con las VNet de otras regiones. La característica de integración con red virtual tiene dos variantes:

* **Integración con red virtual regional**: cuando se conecta a redes virtuales de la misma región, debe tener una subred dedicada en la VNet con la que se va a realizar la integración.
* **Integración con red virtual con requisito de puerta de enlace**: cuando se conecta directamente a VNet de otras regiones o a una red virtual clásica de la misma región, necesita una puerta de enlace de Azure Virtual Network creada en la VNet de destino.

La característica de integración con red virtual:

* Necesita un plan de precios **Estándar**, **Prémium**, **Prémium v2**, **Prémium v3** o **Elástico Prémium** de App Service.
* Es compatible con TCP y UDP.
* Funciona con aplicaciones de Azure App Service y de funciones.

Hay algunos aspectos que la integración con red virtual no admite, como los siguientes:

* Montar una unidad.
* Integración de Windows Server Active Directory.
* NetBIOS.

Cuando la integración con red virtual necesita una puerta de enlace, solo proporciona acceso a los recursos de la VNet de destino o de las redes conectadas a esta que tienen emparejamiento o redes privadas virtuales. La integración con red virtual con requisito de puerta de enlace no permite acceder a los recursos disponibles en las conexiones de Azure ExpressRoute ni trabajar con puntos de conexión de servicio.

Independientemente de la versión que se use, la integración con red virtual ofrece a la aplicación el acceso a los recursos de la VNet, pero no concede el acceso privado de entrada a la aplicación desde la VNet. El acceso privado a sitios se refiere a que solo se puede acceder a la aplicación desde una red privada (por ejemplo, desde dentro de una red virtual de Azure). La integración con red virtual solo sirve para realizar llamadas salientes desde la aplicación hacia la red virtual.
