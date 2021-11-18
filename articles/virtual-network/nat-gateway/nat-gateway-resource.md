---
title: Diseño de redes virtuales con recursos de puertas de enlace de NAT
titleSuffix: Azure Virtual Network NAT
description: Aprenda a diseñar redes virtuales con los recursos de puertas de enlace de NAT.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2021
ms.author: allensu
ms.openlocfilehash: d0af0d38ef53e4ba90dcf0e7f4cbcc60c52b87a8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373144"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Diseño de redes virtuales con recursos de puertas de enlace de NAT

Los recursos de puerta de enlace de NAT forman parte de [Virtual Network NAT](nat-overview.md) y proporcionan conectividad saliente a Internet para una o varias subredes de una red virtual. La subred de la red virtual indica qué puerta de enlace de NAT se usará. NAT proporciona traducción de direcciones de red de origen (SNAT) para una subred.  Los recursos de puerta de enlace de NAT especifican las direcciones IP estáticas que usan las máquinas virtuales al crear flujos de salida. Las direcciones IP estáticas proceden de recursos de direcciones IP públicas (PIP), recursos de prefijos IP públicos, o ambos. Si se usa un recurso de prefijo de dirección IP pública, un recurso de puerta de enlace NAT consume todas las direcciones IP de todos los recursos con prefijo de dirección IP pública. Un recurso de puerta de enlace de NAT puede usar un máximo de 16 direcciones IP desde cualquiera de ellas.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Virtual Network NAT para la salida a Internet">
</p>

*Ilustración: Virtual Network NAT para la salida a Internet*

## <a name="how-to-deploy-nat"></a>Implementación de NAT

La configuración y el uso de la puerta de enlace de NAT se ha hecho sencilla a propósito:  

Recurso de puerta de enlace de NAT:
- Cree un recurso de puerta de enlace NAT regional o zonal,
- Asigne direcciones IP.
- Si es necesario, modifique el tiempo de espera de inactividad de TCP (opcional).  Revise los [temporizadores](#timers) <ins>antes de</ins> cambiar el valor predeterminado.

Red virtual:
- Configure una subred de red virtual para que use una puerta de enlace de NAT.

No se necesitan rutas definidas por el usuario.


## <a name="design-guidance"></a>Guía de diseño

Lea esta sección para familiarizarse con las consideraciones para diseñar redes virtuales con NAT.  

### <a name="connecting-to-azure-services"></a>Conexión a los servicios de Azure

Al conectarse a los servicios de Azure, el enfoque recomendado es aprovechar [Private Link](../../private-link/private-link-overview.md). 

Private Link enlaza los recursos de Azure a la red virtual y controla el acceso a los recursos del servicio de Azure. Por ejemplo, al acceder a Azure Storage, utilice un punto de conexión privado para el almacenamiento para asegurarse de que la conexión sea totalmente privada.

### <a name="connecting-to-the-internet"></a>Conexión a Internet

Se recomienda NAT para escenarios de salida para todas las cargas de trabajo de producción en las que necesite conectarse a un punto de conexión público. Los escenarios siguientes son ejemplos de cómo garantizar la coexistencia de entrada con la puerta de enlace NAT para la salida.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT y máquina virtual con IP pública de nivel de instancia

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="Virtual Network NAT y máquina virtual con IP pública de nivel de instancia">
</p>

*Ilustración: Virtual Network NAT y máquina virtual con IP pública de nivel de instancia*

| Dirección | Recurso |
|:---:|:---:|
| Entrada | Máquina virtual con IP pública de nivel de instancia |
| Salida | NAT Gateway |

La máquina virtual usará una puerta de enlace de NAT para la salida.  La entrada originada no resulta afectada.

#### <a name="nat-and-vm-with-standard-public-load-balancer"></a>NAT y máquina virtual con equilibrador de carga público estándar

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="Virtual Network NAT y máquina virtual con equilibrador de carga público">
</p>

*Ilustración: Virtual Network NAT y máquina virtual con equilibrador de carga público*

| Dirección | Recurso |
|:---:|:---:|
| Entrada | Equilibrador de carga público |
| Salida | NAT Gateway |

La puerta de enlace de NAT sustituye la configuración de salida de una regla de equilibrio de carga o las reglas de salida.  La entrada originada no resulta afectada.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-standard-public-load-balancer"></a>VirNAT y máquina virtual con IP pública de nivel de instancia y equilibrador de carga pública estándar

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="Virtual Network NAT y máquina virtual con IP pública de nivel de instancia y equilibrador de carga pública">
</p>

*Ilustración: Virtual Network NAT y máquina virtual con IP pública de nivel de instancia y equilibrador de carga pública*

| Dirección | Recurso |
|:---:|:---:|
| Entrada | Máquina virtual con IP pública de nivel de instancia y equilibrador de carga pública |
| Salida | NAT Gateway |

La puerta de enlace de NAT sustituye la configuración de salida de una regla de equilibrio de carga o las reglas de salida.  La máquina virtual también usará una puerta de enlace de NAT para la salida.  La entrada originada no resulta afectada.

## <a name="performance"></a>Rendimiento

Cada recurso de puerta de enlace NAT puede proporcionar hasta 50 Gbps de rendimiento. Puede dividir las implementaciones en varias subredes y asignar a cada subred o grupos de subredes una puerta de enlace NAT para realizar escalar horizontalmente.

Cada puerta de enlace NAT puede admitir 64 000 flujos para TCP y UDP respectivamente por dirección IP de salida asignada.  Revise la siguiente sección sobre la traducción de direcciones de red de origen (SNAT) para más información, así como el [artículo de solución de problemas ](./troubleshoot-nat.md) para obtener instrucciones específicas sobre la resolución de problemas.

## <a name="source-network-address-translation"></a>Traducción de direcciones de red de origen

La traducción de direcciones de red de origen (SNAT) reescribe el origen de un flujo para que parta de otra dirección IP.  Los recursos de la puerta de enlace de NAT usan una variante de SNAT que habitualmente se denomina traducción de direcciones de puertos (PAT). PAT reescribe la dirección de origen y el puerto de origen. Con SNAT, no hay una relación fija entre el número de direcciones privadas y sus direcciones públicas traducidas.  

### <a name="fundamentals"></a>Aspectos básicos

Examinemos un ejemplo de cuatro flujos que explican el concepto básico.  La puerta de enlace NAT usa el recurso de dirección IP pública 65.52.1.1 y la máquina virtual realiza las conexiones con la dirección 65.52.0.1.

| Flujo | Tupla de origen | Tupla de destino |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

Estos flujos pueden tener el siguiente aspecto después de que se haya producido la traducción de puertos de origen:

| Flujo | Tupla de origen | Tupla de origen con SNAT | Tupla de destino | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

El destino verá el origen del flujo como 65.52.0.1 (tupla de origen con SNAT) con el puerto asignado que se muestra.  PAT como se muestra en la tabla anterior también se denomina SNAT de enmascaramiento de puertos.  Varios orígenes privados se enmascaran detrás de una IP y un puerto.  

#### <a name="source-snat-port-reuse"></a>Reutilización del puerto de origen (SNAT)

Las puertas de enlace NAT reutilizan los puertos de origen (SNAT) oportunamente.  A continuación se ilustra este concepto como un flujo adicional del conjunto anterior de flujos.  La máquina virtual del ejemplo es un flujo a la dirección 65.52.0.2.

| Flujo | Tupla de origen | Tupla de destino |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Probablemente, una puerta de enlace NAT traducirá el flujo 4 a un puerto que también se puede usar para otros destinos.  Consulte [Escalado](#scaling) para más información sobre el ajuste correcto del aprovisionamiento de direcciones IP.

| Flujo | Tupla de origen | Tupla de origen con SNAT | Tupla de destino | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

No dependa de la forma específica en la que se asignan los puertos de origen en el ejemplo anterior.  Lo anterior es una ilustración solo del concepto fundamental.

El SNAT que proporciona NAT es diferente del [equilibrador de carga](../../load-balancer/load-balancer-outbound-connections.md) en varios aspectos.

### <a name="on-demand"></a>A petición

NAT proporciona puertos SNAT a petición para nuevos flujos de tráfico de salida. Todos los puertos SNAT disponibles en el inventario los usa la máquina virtual en las subredes configuradas con NAT. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="SNAT de salida a petición de Virtual Network NAT">
</p>

*Ilustración: SNAT de salida a petición de Virtual Network NAT*

Todas las configuraciones de IP de una máquina virtual pueden crear flujos de salida a petición si fuera necesario.  No es necesario realizar un planeamiento de cada instancia previo a la asignación, ni siquiera el sobreaprovisionamiento por instancia para el caso más desfavorable.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="Diferencias en escenarios de agotamiento">
</p>

*Ilustración: Diferencias en escenarios de agotamiento*

Una vez que se libera un puerto SNAT, este está disponible para que lo use cualquier máquina virtual de las subredes configuradas con NAT.  La asignación a petición permite que las cargas de trabajo dinámicas y divergentes en subredes usen los puertos SNAT a medida que se necesiten.  Mientras haya un inventario de puertos SNAT disponible, los flujos de SNAT funcionarán correctamente. Las zonas activas de los puertos SNAT se benefician de que el inventario sea mayor. Los puertos SNAT se quedan sin usar en las máquinas virtuales que no los necesiten de forma activa.

### <a name="scaling"></a>Ampliación

El escalado de NAT es primordialmente una función de la administración del inventario de puertos SNAT compartidos disponibles. NAT necesita inventario de puertos SNAT suficiente para afrontar el flujo máximo de salida previsto en todas las subredes conectadas a un recurso de puerta de enlace de NAT.  Puede usar recursos de direcciones IP públicas, recursos de prefijos IP públicos, o ambos para crear un inventario de puertos SNAT.  

>[!NOTE]
>Si va a asignar un recurso de prefijo de dirección IP pública, se usará todo el prefijo de dirección IP pública.  No se puede asignar un recurso de prefijo de dirección IP pública y, después, descomponer las direcciones IP individuales para asignarlas a otros recursos.  Si desea asignar direcciones IP individuales de un prefijo de dirección IP pública a varios recursos, debe crear direcciones IP públicas individuales desde el recurso de prefijo de dirección IP pública y asignarlas según sea necesario en lugar del propio recurso de prefijo de dirección IP pública.

SNAT asigna direcciones privadas a una o varias direcciones IP públicas y reescribe la dirección de origen y el puerto de origen en los procesos. Un recurso de puerta de enlace de NAT usará 64 000 puertos (puertos SNAT) por dirección IP pública para esta traducción. Los recursos de puerta de enlace de NAT se pueden escalar verticalmente hasta un máximo de 16 direcciones IP y un millón de puertos SNAT. Si se proporciona un recurso de prefijo de dirección IP pública, cada dirección IP dentro del prefijo proporciona un inventario de puertos SNAT. Y agregar más direcciones IP públicas aumenta los puertos SNAT del inventario disponibles. TCP y UDP son inventarios de puertos SNAT independientes y no están relacionados.

Los recursos de puerta de enlace NAT reutilizan los puertos de origen (SNAT) oportunamente. Como guía de diseño con fines de escalado, se debe asumir que cada flujo requiere un nuevo puerto SNAT y escalar el número total de direcciones IP disponibles para el tráfico de salida.  Debe considerar detenidamente la escala para la que está diseñando y aprovisionar las cantidades de direcciones IP en consecuencia.

Es más probable que se reutilicen los puertos SNAT a destinos diferentes cuando sea posible. Y, a medida que se aproxime el agotamiento de los puertos SNAT, los flujos pueden no realizarse correctamente.  

Consulte [Aspectos básicos de SNAT](#source-network-address-translation) por ver ejemplos.


### <a name="protocols"></a>Protocolos

Los recursos de puerta de enlace de NAT interactúan con la IP y los encabezados de transporte de IP de los flujos UDP y TCP, y son independientes de las cargas de la capa de aplicaciones.  No se admiten otros protocolos de IP.

### <a name="timers"></a>Temporizadores

>[!IMPORTANT]
>Un temporizador de inactividad prolongado puede aumentar innecesariamente la probabilidad de agotamiento de SNAT. Cuanto mayor sea el temporizador que especifique, más tiempo retendrá la NAT los puertos SNAT hasta que finalmente se agote el tiempo de espera. Si los flujos están inactivos, producirán un error en este momento y consumirán innecesariamente el inventario de puertos SNAT.  Los flujos que producen un error a las dos horas también lo habrían producido al valor predeterminado de cuatro minutos. Aumentar el tiempo de espera de inactividad es una opción de último recurso que debe usarse con moderación. Si un flujo nunca está inactivo, no se verá afectado por el temporizador de inactividad.

El tiempo de espera de inactividad de TCP se puede ajustar de cuatro minutos (valor predeterminado) a 120 minutos (dos horas) para todos los flujos.  Además, puede restablecer el temporizador inactivo con tráfico del flujo.  Un patrón recomendado para actualizar las largas conexiones inactivas y para detectar la ejecución de puntos de conexión es usar mensajes TCP Keepalive.  Estos mensajes aparecen como ACK duplicados en los puntos de conexión, tienen una sobrecarga baja y son invisibles para la capa de aplicaciones.

Para la liberación de los puertos SNAT se usan los siguientes temporizadores:

| Timer | Value |
|---|---|
| TCP FIN | 60 segundos |
| TCP RST | 10 segundos |
| TCP semiabierto | 30 segundos |

Los puertos SNAT están disponibles para volver a usarlos con la misma dirección IP y puerto de destino a los 5 segundos.

>[!NOTE] 
>Esta configuración del temporizador está sujeta a cambios. Los valores se proporcionan como ayuda para la solución de problemas y no se debe asumir una dependencia de temporizadores concretos en este momento.

## <a name="limitations"></a>Limitaciones

- El equilibrador de cara estándar y las direcciones IP públicas básicas no son compatibles con NAT. En su lugar, deben utilizarse equilibradores de carga de SKU estándar e IP públicas.
- La fragmentación de IP no es compatible mediante la puerta de enlace NAT.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre [Virtual Network NAT](nat-overview.md).
* Obtenga información acerca de las [métricas y alertas de los recursos de puerta de enlace NAT](nat-metrics.md).
* Obtenga información sobre la [solución de los problemas de los recursos de la puerta de enlace de NAT](troubleshoot-nat.md).
