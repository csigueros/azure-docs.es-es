---
title: Actualización de septiembre de 2020 de Azure VMware Solution by CloudSimple
description: En este artículo, obtendrá información sobre qué esperar durante esta operación de mantenimiento y los cambios en la nube privada.
author: dikamath
ms.author: dikamath
ms.date: 09/3/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
manager: dikamath
ms.openlocfilehash: eea32da3da1748fb1049fb32e6af8dd2467b4f60
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339226"
---
# <a name="azure-vmware-solution-by-cloudsimple-september-2020-update"></a>Actualización de septiembre de 2020 de Azure VMware Solution by CloudSimple

En septiembre, se realizará una actualización importante del servicio Azure VMware Solution. Se incluirá la escala de tiempo del mantenimiento en una notificación por correo electrónico que enviaremos como parte del mantenimiento. En este artículo, obtendrá información sobre qué esperar durante esta operación de mantenimiento y los cambios en la nube privada.

> [!NOTE]
> Se trata de una actualización no disruptiva. Durante la actualización, es posible que alguno de los componentes redundantes desaparezca.

## <a name="vmware-infrastructure-upgrade"></a>Actualización de infraestructura de VMware

La infraestructura de VMware de la nube privada se actualizará a una versión más reciente. Esto incluye actualizaciones de los componentes vCenter, ESXi, NSX-T y extensión de nube híbrida (HCX) de su nube privada (si se han implementado).

Durante la actualización, se agregará un nuevo nodo a la nube privada antes de que se coloque un nodo en modo de mantenimiento para la operación de actualización. De este modo, se garantiza que la capacidad y disponibilidad de la nube privada se mantienen durante el proceso de actualización. Durante la actualización de los componentes de VMware, es posible que se muestren alarmas en la interfaz de usuario web de vCenter. Las alarmas forman parte de las operaciones de mantenimiento realizadas por el equipo de operaciones de servicio.

**Versión del componente**

- ESXi 6.7U3
- vCenter 6.7U3
- vSAN 6.7
- NSX Data Center 2.5.1
- HCX 3.5.2

## <a name="datacenter-updates"></a>Actualizaciones del centro de datos

Esta actualización incluye actualizaciones de la infraestructura del centro de datos. Durante el período de mantenimiento se realizarán actualizaciones no disruptivas. Notará una reducción de la redundancia durante el proceso de actualización. Pueden generarse alertas en la infraestructura de VMware de la nube privada, los circuitos ExpressRoute, las conexiones GlobalReach y los dispositivos VPN de sitio a sitio relacionados con alguna de las disponibilidades de vínculos. Esto es normal durante la actualización, ya que los componentes se reiniciarán como parte de la operación.

-   Si una VPN de sitio a sitio se implementa como una instancia única (no de alta disponibilidad), podría ser necesario volver a establecer la conexión VPN.

-   Si usa una conexión VPN de punto a sitio, tendrá que volver a establecer la conexión VPN.

## <a name="post-update"></a>Después de la actualización

Una vez completadas las actualizaciones, se deberían mostrar las versiones más recientes de los componentes de VMware. Si nota algún problema o tiene alguna pregunta, póngase en contacto con nuestro equipo de soporte técnico al abrir una [incidencia de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
