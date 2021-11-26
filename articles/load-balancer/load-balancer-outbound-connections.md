---
title: Traducción de direcciones de red de origen (SNAT) para conexiones salientes
titleSuffix: Azure Load Balancer
description: Aprenda a usar Azure Load Balancer para la conectividad saliente de Internet (SNAT).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 11/11/2021
ms.author: allensu
ms.openlocfilehash: be42635a3d9c15287b4698f02bee91ab1ed38826
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723691"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Uso de la Traducción de direcciones de red de origen (SNAT) para conexiones salientes

Algunos escenarios requieren que las máquinas virtuales o las instancias de proceso tengan conectividad de salida a Internet. Las direcciones IP de front-end de un equilibrador de carga público de Azure se pueden usar para proporcionar conectividad saliente a Internet para las instancias de back-end. Esta configuración usa la **traducción de direcciones de red de origen (SNAT)** para traducir la dirección IP privada de la máquina virtual a una dirección IP pública del equilibrador de carga. SNAT asigna la dirección IP del back-end a la dirección IP pública del equilibrador de carga. SNAT impide que los orígenes externos tengan una dirección directa a las instancias de back-end.  

## <a name="azures-outbound-connectivity-methods"></a><a name="scenarios"></a>Métodos de conectividad de salida de Azure

En Azure, la conectividad de salida a Internet se puede habilitar de las siguientes maneras:

| # | Método | Tipo de asignación de puertos | ¿De nivel de producción? | Rating |
| ------------ | ------------ | ------ | ------------ | ------------ |
| 1 | Uso de las direcciones IP de front-end de un equilibrador de carga para la salida mediante reglas de salida | Estática, explícita | Sí, pero no a gran escala | Aceptar | 
| 2 | Asociación de una puerta de enlace NAT a la subred | Estática, explícita | Sí | Óptima | 
| 3 | Asignación de una dirección IP pública a la máquina virtual | Estática, explícita | Sí | Aceptar | 
| 4 | Uso del [acceso saliente predeterminado](../virtual-network/ip-services/default-outbound-access.md) | Implícita | No | El peor |

## <a name="1-using-the-frontend-ip-address-of-a-load-balancer-for-outbound-via-outbound-rules"></a><a name="outboundrules"></a>1. Uso de la dirección IP de front-end de un equilibrador de carga para realizar la salida mediante reglas de salida

Las reglas de salida permiten definir de forma explícita SNAT (traducción de direcciones de red de origen) para un equilibrador de carga público estándar. Esta configuración permite usar las direcciones IP públicas del equilibrador de carga para la conectividad saliente de las instancias de back-end.

Esta configuración habilita:

- El enmascaramiento de IP
- La simplificación de las listas de permitidos
- La reducción del número de recursos de IP pública para la implementación

Con las reglas de salida, tiene un control declarativo completo sobre la conectividad saliente de Internet. Las reglas de salida le permiten escalar y ajustar esta capacidad a sus necesidades específicas mediante la asignación manual de puertos. La asignación manual del puerto SNAT en función del tamaño del grupo de back-end y el número de frontendIPConfigurations puede ayudar a evitar el agotamiento de SNAT. 

Puede asignar manualmente los puertos SNAT por "puertos por instancia" o por "número máximo de instancias de back-end". Si hay máquinas virtuales en el back-end, se recomienda asignar los puertos por "puertos por instancia" para obtener el uso máximo de puertos SNAT. 

Los puertos por instancia se deben calcular como se indica a continuación: 

**Número de direcciones IP de front-end * 64 000 / número de instancias de back-end** 

Si hay conjuntos de escalado de máquinas virtuales en el back-end, se recomienda asignar los puertos por "número máximo de instancias de back-end". Si se agregan más VM al back-end que puertos SNAT restantes permitidos, es posible que el escalado vertical del conjunto de escalado de máquinas virtuales se bloquee o que las nuevas VM no obtengan suficientes puertos SNAT. 

Para obtener más información sobre las reglas de salida, consulte [Reglas de salida](outbound-rules.md).

## <a name="2-associating-a-nat-gateway-to-the-subnet"></a>2. Asociación de una puerta de enlace NAT a la subred

Virtual Network NAT simplifica la conectividad a Internet solo de salida en las redes virtuales. Cuando se configura en una subred, todas las conexiones salientes usan las direcciones IP públicas estáticas que se hayan especificado. La conectividad saliente es posible sin que el equilibrador de carga ni las direcciones IP públicas estén conectados directamente a máquinas virtuales. La NAT está totalmente administrada y es muy resistente.

El uso de una puerta de enlace NAT es el mejor método para la conectividad saliente. Las puertas de enlace NAT tienen gran capacidad de ampliación, son muy confiable y no tienen los mismos problemas de agotamiento de puertos SNAT.

Para obtener más información sobre Azure Virtual Network NAT, consulte [¿Qué es Azure Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md)

##  <a name="3-assigning-a-public-ip-to-the-virtual-machine"></a>3. Asignación de una dirección IP pública a la máquina virtual

 | Asociaciones | Método | Protocolos IP |
 | ---------- | ------ | ------------ |
 | IP pública en la NIC de la VM | SNAT (traducción de direcciones de red de origen) </br> no se usa. | TCP (protocolo de control de transmisión) </br> UDP (protocolo de datagramas de usuario) </br> ICMP (protocolo de mensajes de control de Internet) </br> ESP (carga de seguridad encapsuladora) |

El tráfico volverá al cliente solicitante desde la dirección IP pública de la máquina virtual (IP de nivel de instancia).
 
Azure usa la dirección IP pública asignada a la configuración IP de la NIC de la instancia para todos los flujos de salida. La instancia tiene disponibles todos los puertos efímeros. Es irrelevante si la máquina virtual es de carga equilibrada o no. Este escenario tiene prioridad sobre las demás. 

Una dirección IP pública asignada a una máquina virtual es una relación 1:1 (en lugar de 1:muchos) y se implementa como NAT de 1:1 sin estado.


## <a name="4-default-outbound-access"></a>4. Acceso saliente predeterminado

>[!NOTE]
> Este método **NO se recomienda** para cargas de trabajo de producción, ya que aumenta el riesgo de que se agoten los puertos. No use este método para cargas de trabajo de producción para evitar posibles errores de conexión. 

Los recursos de Azure que no tengan una dirección IP pública asociada, que no tengan un equilibrador de carga con reglas de salida delante, que no formen parte del modo de orquestación flexible de conjuntos de escalado de máquinas virtuales o que no tengan un recurso puerta de enlace de NAT asociado a su subred tienen asignado un número mínimo de puertos para la salida. Este acceso se conoce como acceso saliente predeterminado y es el peor método para proporcionar conectividad de salida a las aplicaciones. 

Estos son otros ejemplos de acceso saliente predeterminado:

- Cuando se usa una instancia de Load Balancer Básico.
- Una máquina virtual de Azure (sin las asociaciones mencionadas anteriormente). En este caso, la IP de acceso de salida predeterminada proporciona la conectividad saliente. Esta IP es dinámica, la asigna Azure y no se puede controlar. No se recomienda la SNAT predeterminada para cargas de trabajo de producción, ya que puede provocar errores de conectividad.
- Una máquina virtual en el grupo de back-end de Load Balancer sin reglas de salida. Como resultado, se usa la dirección IP de front-end de un equilibrador de carga para la salida y la entrada, y es más propenso a errores de conectividad debido al agotamiento del puerto SNAT.

### <a name="what-are-snat-ports"></a>¿Qué son los puertos SNAT?

Se usan puertos para generar identificadores únicos que se emplean para mantener flujos distintos. Internet usa una tupla de cinco elementos para proporcionar esta distinción.

Si se usa un puerto para las conexiones entrantes, tendrá un **agente de escucha** para las solicitudes de conexión entrantes en ese puerto. Recuerde que ese puerto no se puede usar para las conexiones salientes. Para establecer una conexión saliente, se usa un **puerto efímero** para proporcionar al destino un puerto con el que comunicarse y mantener un flujo de tráfico distinto. Cuando estos puertos efímeros se usan para la SNAT, se denominan **puertos SNAT**.

Por definición, todas las direcciones IP tienen 65 535 puertos. Cada puerto se puede usar para las conexiones de entrada o salida para TCP (Protocolo de control de transmisión) y UDP (Protocolo de datagramas de usuario). Cuando se agrega una dirección IP pública como una IP de front-end a un equilibrador de carga, hay 64 000 puertos aptos para SNAT. Aunque se pueden asignar todas las IP públicas que se agregan como IP de front-end, las IP de front-end se consumen de una en una. Por ejemplo, si dos instancias de back-end tienen asignados 64 000 puertos cada una, con acceso a 2 direcciones IP de front-end, ambas instancias de back-end consumirán puertos desde la primera dirección IP de front-end hasta que se hayan agotado los 64 000 puertos.  

Un puerto usado para una regla NAT de entrada o de equilibrio de carga consume ocho de los 64 000 puertos. Este uso reduce el número de puertos aptos para SNAT. Si una regla NAT de entrada o de equilibrio de carga está en el mismo intervalo de ocho puertos que otra, no usará puertos adicionales. 

### <a name="how-does-default-snat-work"></a>¿Cómo funciona SNAT predeterminado?

Cuando la máquina virtual crea un flujo de salida, Azure traduce la dirección IP de origen a una dirección IP efímera. Esta traducción se realiza a través de SNAT. 

Si usa SNAT sin reglas salientes a través de un equilibrador de carga público, los puertos SNAT se asignan previamente como se describe en la tabla de asignación de puertos SNAT predeterminados siguiente.

## <a name="default-port-allocation-table"></a><a name="preallocatedports"></a> Tabla de asignación de puertos predeterminados

En la <a name="snatporttable"></a>tabla siguiente se muestran las asignaciones previas de puertos SNAT para los tamaños del grupo de back-end:

| Tamaño del grupo (instancias de máquina virtual) | Puertos SNAT predeterminados por cada configuración IP |
| --- | --- |
| 1-50 | 1024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 | 

## <a name="exhausting-ports"></a> Agotamiento de puertos

Cada conexión a la misma dirección IP de destino y puerto de destino usará un puerto SNAT. Esta conexión mantiene un **flujo de tráfico** distinto desde la instancia de back-end o **cliente** a un **servidor**. Este proceso proporciona al servidor un puerto distinto en el que dirigir el tráfico. Sin este proceso, el equipo cliente no sabe a qué flujo pertenece un paquete.

Imagine que varios exploradores van a https://www.microsoft.com, que tiene los valores siguientes:

* Dirección IP de destino = 23.53.254.142
* Puerto de destino = 443
* Protocolo = TCP

Sin puertos de destino diferentes para el tráfico de retorno (el puerto SNAT usado para establecer la conexión), el cliente no tendrá forma de separar el resultado de una consulta de otro.

Las conexiones salientes pueden aumentar. Una instancia de back-end podría tener asignados puertos insuficientes. Use la funcionalidad de **reutilización de conexiones** en la aplicación. Si la **reutilización de conexiones** no está habilitada, aumenta el riesgo de **agotamiento de puertos** de SNAT. Para obtener más información sobre la agrupación de conexiones con Azure App Service, consulte [Solución de problemas de errores intermitentes de conexión saliente en Azure App Service](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#avoiding-the-problem).

Se producirá un error en las nuevas conexiones salientes a una dirección IP de destino cuando se agoten los puertos. Las conexiones se realizarán correctamente cuando esté disponible un puerto. Este agotamiento se produce cuando los 64 000 puertos de una dirección IP se distribuyen entre muchas instancias de back-end. Para obtener instrucciones sobre la mitigación del agotamiento de puertos SNAT, consulte la [guía de solución de problemas](./troubleshoot-outbound-connection.md).  

En el caso de las conexiones TCP, el equilibrador de carga usará un solo puerto SNAT para cada IP y puerto de destino. Este uso múltiple permite varias conexiones a la misma dirección IP de destino con el mismo puerto SNAT, pero se limita si la conexión no es a puertos de destino diferentes.

En el caso de las conexiones UDP, el equilibrador de carga usa un algoritmo de **NAT de cono con restricción de puerto**, que consume un puerto SNAT por cada IP de destino, sea cual sea el puerto de destino. 

Un puerto se reutiliza para un número ilimitado de conexiones. El puerto solo se reutiliza si la dirección IP o puerto de destino son diferentes.


## <a name="constraints"></a>Restricciones

*   Cuando una conexión está inactiva y no se envían paquetes nuevos, los puertos se liberarán después de un intervalo entre 4 y 120 minutos.
  * Este umbral se puede configurar a través de reglas de salida.
*   Cada dirección IP proporciona 64 000 puertos que se pueden usar para SNAT.
*   Cada puerto se puede usar para conexiones TCP y UDP con una dirección IP de destino.
  * Se necesita un puerto SNAT UDP tanto si el puerto de destino es único como si no. Para cada conexión UDP con una dirección IP de destino, se usa un puerto SNAT UDP.
  * Se puede usar un puerto SNAT TCP para varias conexiones con la misma dirección IP de destino, siempre que los puertos de destino sean diferentes.
*   El agotamiento de SNAT se produce cuando una instancia de back-end se ejecuta fuera de los puertos SNAT establecidos. Un equilibrador de carga puede tener puertos SNAT sin usar. Si los puertos SNAT usados de una instancia de back-end superan sus puertos SNAT, no podrá establecer nuevas conexiones salientes.
*   Los paquetes fragmentados se descartarán, salvo que la salida se realice a través de una IP pública de nivel de instancia en la NIC de la máquina virtual.
*   Las configuraciones IP secundarias de una interfaz de red no tienen comunicación saliente (a menos que una dirección IP pública esté asociada a ella) a través de Load Balancer.

## <a name="next-steps"></a>Pasos siguientes

*   [Solución de errores de conexiones salientes debidos a un agotamiento de SNAT](./troubleshoot-outbound-connection.md)
*   [Revise las métricas de SNAT](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) y familiarícese con la manera correcta de filtrarlas, dividirlas y visualizarlas.
