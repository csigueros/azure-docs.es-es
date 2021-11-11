---
author: madsd
ms.service: app-service-web
ms.topic: include
ms.date: 10/01/2020
ms.author: madsd
ms.openlocfilehash: 2a95e92ce5bb6705fa42b2885c73480a9d649050
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892881"
---
* Los planes de tarifa de proceso dedicados, que incluyen los niveles Básico, Estándar, Premium, Premium v2 y Premium v3.
* App Service Environment que se implementa directamente en la red virtual con una infraestructura de soporte técnico dedicada y usa los planes de tarifa Aislado y Aislado v2.

La característica de integración con red virtual se usa en planes de tarifa de proceso dedicado de Azure App Service. Si la aplicación está en [App Service Environment](../articles/app-service/environment/overview.md), ya forma parte de una red virtual y no requiere el uso de la característica Integración con red virtual para acceder a recursos en la misma red virtual. Para más información sobre todas las características de redes, consulte [Características de redes de App Service](../articles/app-service/networking-features.md).

Integración con red virtual proporciona a la aplicación acceso a los recursos de la red virtual, pero no concede acceso privado de entrada a la aplicación desde esa red virtual. El acceso privado a sitios se refiere a que solo se puede acceder a la aplicación desde una red privada; por ejemplo, desde dentro de una red virtual de Azure. Integración de Virtual Network solo se usa para realizar llamadas salientes de la aplicación a la red virtual. La característica Integración con red virtual se comporta de forma distinta cuando se usa con redes virtuales de la misma región y con redes virtuales de otras regiones. La característica de integración con red virtual tiene dos variantes:

* **Integración con red virtual regional**: cuando se conecta a redes virtuales de la misma región, debe tener una subred dedicada en la red virtual con la que se va a realizar la integración.
* **Integración con red virtual con requisito de puerta de enlace**: cuando se conecta directamente a redes virtuales de otras regiones o a una red virtual clásica de la misma región, necesita una puerta de enlace de Azure Virtual Network creada en la red virtual de destino.

La característica de integración con red virtual:

* Necesita un plan de precios Estándar, Premium, Premium v2, Premium v3 o Elástico Premium de App Service.
* Es compatible con TCP y UDP.
* Funciona con aplicaciones de App Service y de Functions.

Hay algunos aspectos que la integración de red virtual no admite, como:

* Montar una unidad.
* Integración de Windows Server Active Directory.
* NetBIOS.

Integración con red virtual con requisito de puerta de enlace solo proporciona acceso a los recursos de la red virtual de destino o de las redes conectadas a esta que tienen emparejamiento o redes privadas virtuales. La integración con red virtual con requisito de puerta de enlace no permite acceder a los recursos disponibles en las conexiones de Azure ExpressRoute ni trabajar con puntos de conexión de servicio.

Independientemente de la versión que se use, la integración con red virtual proporciona a la aplicación acceso a los recursos de la red virtual, pero no concede acceso privado de entrada a la aplicación desde esa red virtual. El acceso privado a sitios se refiere a que solo se puede acceder a la aplicación desde una red privada (por ejemplo, desde dentro de una red virtual de Azure). La integración de red virtual solo sirve para realizar llamadas salientes desde la aplicación a la red virtual.
