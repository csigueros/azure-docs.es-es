---
title: Conceptos de Azure Load Balancer
description: Información general acerca de los conceptos de Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 946741c8aa70040dd0186deceab0fb090cf38c11
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129271752"
---
# <a name="azure-load-balancer-algorithm"></a>Algoritmo de Azure Load Balancer

Load Balancer proporciona varias funcionalidades para las aplicaciones UDP y TCP.

## <a name="load-balancing-algorithm"></a>Algoritmo de equilibrio de carga

Al crear una regla de equilibrador de carga, puede distribuir los flujos de tráfico entrantes desde el front-end de un equilibrador de carga a sus grupos de back-end. Azure Load Balancer usa un algoritmo hash de tupla de cinco elementos para la distribución de los flujos (no bytes) entrantes.  El equilibrador de carga reescribe los encabezados de los flujos de encabezados TCP/UDP al dirigir el tráfico a las instancias del grupo de back-end (el equilibrador de carga no reescribe los encabezados HTTP/HTTPS). Cuando el sondeo de estado del equilibrador de carga indica un punto de conexión de back-end correcto, las instancias de back-end estarán disponibles para recibir nuevos flujos de tráfico.

De manera predeterminada, Load Balancer usa un hash de tupla de cinco elementos.

El hash incluye:

- **Dirección IP de origen**
- **Puerto de origen**
- **Dirección IP de destino**
- **Puerto de destino**
- **Número de protocolo IP para asignar flujos a servidores disponibles**

## <a name="session-persistence"></a>Persistencia de la sesión

Los paquetes del mismo flujo llegarán en la misma instancia de grupo de back-end. Sin embargo, cuando un cliente inicia un nuevo flujo desde la misma dirección IP de origen, el puerto de origen cambia. En consecuencia, el hash de tupla de cinco elementos puede hacer que el tráfico vaya a otro punto de conexión de back-end. 

La persistencia de la sesión con una dirección IP de origen se crea mediante un hash de tuplas de dos o tres elementos. Cuando la persistencia de la sesión está habilitada, la misma máquina virtual controla las solicitudes sucesivas de la misma dirección IP de cliente. Para obtener más información sobre los modos de distribución del equilibrador de carga, consulte [Configuración de un modo de distribución de Azure Load Balancer](./load-balancer-distribution-mode.md).

En la siguiente imagen se muestra la distribución basada en hash:

![Distribución basada en hash](./media/load-balancer-overview/load-balancer-distribution.png)

*Ilustración: distribución basada en hash*

## <a name="application-independence-and-transparency"></a>Independencia y transparencia de aplicaciones

El equilibrador de carga admite cualquier escenario de aplicación TCP/UDP y no cierra ni origina flujos. El equilibrador de carga tampoco interactúa con la carga de ningún flujo. 

- Las cargas útiles de las aplicaciones son transparentes para Load Balancer. Se puede admitir cualquier aplicación UDP o TCP.

El equilibrador de carga funciona en la capa 4 y no proporciona la funcionalidad de puerta de enlace del nivel de aplicación. Los protocolos de enlace de protocolo siempre se producen directamente entre el cliente y la instancia del grupo de back-end. 

- Como el equilibrador de carga no interactúa con la carga de TCP y proporciona descarga de TLS, puede compilar escenarios cifrados completos. Con Load Balancer se obtiene una mayor escalabilidad horizontal de las aplicaciones TLS, ya que la conexión TLS finaliza en la propia máquina virtual. Por ejemplo, la capacidad para claves de sesión TLS solo está limitada por el tipo y número de máquinas virtuales que se agregan al grupo de servidores back-end.

Una respuesta a un flujo de entrada siempre es una respuesta de una máquina virtual. Cuando llega el flujo a la máquina virtual, también se conserva la dirección IP de origen original.

- A cada punto de conexión le responde una máquina virtual. Por ejemplo, un protocolo de enlace TCP se realiza entre el cliente y la máquina virtual de back-end seleccionada. Una respuesta a una solicitud a un front-end es una respuesta generada por una máquina virtual de back-end. Cuando se valida correctamente la conectividad con un front-end, se valida la conectividad de un extremo a otro con al menos una máquina virtual de back-end.


## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de los [componentes](components.md) que conforman Azure Load Balancer.
- Consulte [Creación de una instancia de Standard Load Balancer](quickstart-load-balancer-standard-public-portal.md) para empezar a usar una instancia de Load Balancer: crear una, crear máquinas virtuales con una extensión de IIS personalizada instalada y equilibrar la carga de la aplicación web entre las máquinas virtuales.
- Información sobre [Conexiones salientes de Azure Load Balancer](load-balancer-outbound-connections.md).
- Más información sobre [Azure Load Balancer](load-balancer-overview.md).
- Más información sobre [sondeos de mantenimiento](load-balancer-custom-probe-overview.md).
- Más información acerca de los [diagnósticos de Load Balancer Estándar](load-balancer-standard-diagnostics.md).
- Más información sobre los [grupos de seguridad de red](../virtual-network/network-security-groups-overview.md).
