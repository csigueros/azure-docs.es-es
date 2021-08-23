---
title: Preguntas frecuentes sobre Azure Load Balancer
description: Respuestas a las preguntas frecuentes sobre Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: article
ms.date: 04/22/2020
ms.author: errobin
ms.openlocfilehash: dea87239d3e7e9de04d7ebded1c8431445dad8b0
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110068903"
---
# <a name="load-balancer-frequently-asked-questions"></a>Preguntas frecuentes sobre Load Balancer

## <a name="what-types-of-load-balancer-exist"></a>¿Qué tipos de Load Balancer existen?
Equilibradores de carga internos que equilibran el tráfico dentro de una red virtual y equilibradores de carga externos que equilibran el tráfico hacia y desde un punto de conexión conectado a Internet. Para más información, consulte [Tipos de Load Balancer](components.md#frontend-ip-configurations). 

Para ambos tipos, Azure ofrece una SKU básica y una SKU estándar que tienen diferentes capacidades funcionales, de rendimiento, de seguridad y de seguimiento de estado. Estas diferencias se explican en el artículo [Comparación de SKU](skus.md).

 ## <a name="how-can-i-upgrade-from-a-basic-to-a-standard-load-balancer"></a>¿Cómo puedo actualizar de la versión Basic de Load Balancer a la versión Standard?
Consulte el artículo [Actualización de Basic a Standard](upgrade-basic-standard.md) para obtener una secuencia de comandos automatizada e instrucciones sobre la actualización de una SKU de Load Balancer.

 ## <a name="what-are-the-different-load-balancing-options-in-azure"></a>¿Cuáles son las diferentes opciones de equilibrio de carga en Azure?
Consulte la [Guía de tecnología de Load Balancer](/azure/architecture/guide/technology-choices/load-balancing-overview) para conocer los servicios de equilibrio de carga disponibles y los usos recomendados para cada uno.

## <a name="where-can-i-find-load-balancer-arm-templates"></a>¿Dónde puedo encontrar plantillas de Resource Manager para Load Balancer?
Consulte la [lista de plantillas de inicio rápido de Azure Load Balancer](/azure/templates/microsoft.network/loadbalancers#quickstart-templates) para conocer las plantillas de Resource Manager de las implementaciones comunes.

## <a name="how-are-inbound-nat-rules-different-from-load-balancing-rules"></a>¿En qué se diferencian las reglas NAT de entrada de las reglas de equilibrio de carga?
Las reglas NAT se usan para especificar un recurso de back-end al que enrutar el tráfico. Por ejemplo, la configuración de un puerto de equilibrador de carga específico para enviar el tráfico RDP a una máquina virtual específica. Las reglas de equilibrio de carga se usan para especificar un grupo de recursos de back-end para enrutar el tráfico y equilibrar la carga en cada instancia. Por ejemplo, una regla de equilibrador de carga puede enrutar los paquetes TCP en el puerto 80 del equilibrador de carga en un grupo de servidores web.

## <a name="what-is-ip-1686312916"></a>¿Qué es la IP 168.63.129.16?
La dirección IP virtual para el host etiquetada como equilibrador de carga de la infraestructura de Azure en la que se originan los sondeos del estado de Azure. Al configurar las instancias de back-end, deben permitir que el tráfico procedente de esta dirección IP responda correctamente a los sondeos de estado. Esta regla no interactúa con el acceso al front-end de Load Balancer. Si no usa Azure Load Balancer, puede anular esta regla. [Aquí](../virtual-network/service-tags-overview.md#available-service-tags) encontrará más información sobre las etiquetas de servicio.

## <a name="can-i-use-global-vnet-peering-with-basic-load-balancer"></a>¿Puedo usar el emparejamiento de VNET global con Load Balancer básico?
No. Load Balancer básico no admite el emparejamiento de VNET global. En su lugar, puede usar Standard Load Balancer. Consulte el artículo de [actualización de básico a Standard](upgrade-basic-standard.md) para una actualización sin problemas.

## <a name="how-can-i-discover-the-public-ip-that-an-azure-vm-uses"></a>¿Cómo puedo descubrir la dirección IP pública que usa una máquina virtual de Azure?

Hay muchas maneras de determinar la dirección IP de origen público de una conexión saliente. OpenDNS proporciona un servicio que puede mostrar la dirección IP pública de la máquina virtual.
Mediante el comando nslookup, puede enviar una consulta DNS del nombre myip.opendns.com para la resolución de OpenDNS. El servicio devuelve la dirección IP de origen que se usó para enviar la consulta. Si ejecuta la siguiente consulta desde la máquina virtual, la respuesta es la dirección IP pública que se usa para esa máquina virtual:

 ```nslookup myip.opendns.com resolver1.opendns.com```
 
## <a name="can-i-add-a-vm-from-the-same-availability-set-to-different-backend-pools-of-a-load-balancer"></a>¿Puedo agregar una máquina virtual del mismo conjunto de disponibilidad a diferentes grupos de back-end de una instancia de Load Balancer?
No, no es posible.

## <a name="what-is-the-maximum-data-throughput-that-can-be-achieved-via-an-azure-load-balancer"></a>¿Cuál es el rendimiento máximo de datos que se puede lograr a través de una instancia de Azure Load Balancer?
Dado que Azure LB es un equilibrador de carga de red de tránsito, el tipo de máquina virtual que se usa en el grupo de back-end dicta las limitaciones de rendimiento. Para obtener más información relacionada con el rendimiento de la red, consulte [Rendimiento de red de máquinas virtuales](../virtual-network/virtual-machine-network-throughput.md).

## <a name="how-do-connections-to-azure-storage-in-the-same-region-work"></a>¿Cómo funcionan las conexiones a Azure Storage en la misma región?
La conectividad saliente a través de los escenarios anteriores no es necesaria para conectarse al almacenamiento en la misma región que la máquina virtual. Si no quiere que pase esto, use grupos de seguridad de red (NSG) como se explicó anteriormente. Para la conectividad con el almacenamiento en otras regiones, se requiere conectividad de salida. Tenga en cuenta que, al conectarse al almacenamiento desde una máquina virtual en la misma región, la dirección IP de origen en los registros de diagnóstico de almacenamiento será una dirección de proveedor interna y no la dirección IP pública de la máquina virtual. Si desea restringir el acceso a la cuenta de almacenamiento a las máquinas virtuales de una o varias subredes de Virtual Network en la misma región, use [Virtual Network los puntos de conexión de servicio](../virtual-network/virtual-network-service-endpoints-overview.md) y no la dirección IP pública al configurar el firewall de la cuenta de almacenamiento. Una vez configurados los puntos de conexión de servicio, verá la dirección IP privada de su instancia de Virtual Network en los registros de diagnóstico de almacenamiento y no la dirección interna del proveedor.

## <a name="does-azure-load-balancer-support-tlsssl-termination"></a>¿Admite Azure Load Balancer la terminación TLS/SSL?
No, Azure Load Balancer no admite actualmente la terminación, ya que es un equilibrador de carga de red de paso a través. Application Gateway podría ser una posible solución si la aplicación lo requiere.

## <a name="what-are-best-practices-with-respect-to-outbound-connectivity"></a>¿Cuáles son los procedimientos recomendados con respecto a la conectividad de salida?
Standard Load Balancer y la IP pública estándar presentan capacidades nuevas y comportamientos diferentes en la conectividad saliente. No son lo mismo que las SKU de nivel Básico. Si quiere conectividad saliente al trabajar con las SKU de nivel Estándar, debe definirlas con las direcciones IP públicas estándar o con la instancia pública de Load Balancer estándar. Esto incluye establecer conectividad saliente cuando se usa una instancia interna de Standard Load Balancer. Se recomienda que use siempre las reglas de salida en una instancia pública de Load Balancer estándar. Esto significa que cuando se usa una instancia interna de Standard Load Balancer, es necesario seguir los pasos para establecer la conectividad saliente para las máquinas virtuales en el grupo de back-end si se quiere contar con conectividad saliente. En el contexto de la conectividad saliente, una máquina virtual independiente, todas las máquinas virtuales de un conjunto de disponibilidad y todas las instancias de un conjunto de escalado de máquinas virtuales se comportan como un grupo. Es decir que, si una máquina virtual en un conjunto de disponibilidad está asociada con una SKU de nivel Estándar, todas las instancias de máquina virtual dentro de dicho conjunto de disponibilidad ahora siguen las mismas reglas que están asociadas con la SKU de nivel Estándar, incluso si una instancia individual no está directamente asociada con ella. Este comportamiento también se observa en el caso de una máquina virtual independiente con varias tarjetas de interfaz de red conectadas a un equilibrador de carga. Si una NIC se agrega como independiente, tendrá el mismo comportamiento. Revise cuidadosamente todo el documento para entender los conceptos generales, consulte [Standard Load Balancer](./load-balancer-overview.md) para conocer las diferencias entre las SKU y consulte las [reglas de salida](load-balancer-outbound-connections.md#outboundrules).
Al usar las reglas de salida, obtiene un control avanzado de todos los aspectos de la conectividad saliente.
 
 ## <a name="how-can-i-view-the-traffic-from-my-configured-health-probes"></a><a name="probes"></a> ¿Cómo se puede ver el tráfico desde los sondeos de estado configurados?
Para ver el tráfico enviado a cada instancia de back-end desde el sondeo de estado, puede usar las estadísticas de la pila de IP con una herramienta como netstat. En esta herramienta puede ver que el tráfico de sondeo de estado procederá de 168.63.129.16.
 
## <a name="next-steps"></a>Pasos siguientes
Si su pregunta no aparece en la lista anterior, envíe sus comentarios sobre esta página junto con su pregunta. Esto creará un problema de GitHub para que el equipo del producto se asegure de que las preguntas de todos nuestros valiosos clientes se respondan.
