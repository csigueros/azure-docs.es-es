---
title: Solución de problemas comunes de Azure Load Balancer
description: Aprenda a solucionar problemas comunes de Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: 6ab256d58284e79323b988b2b81f4c664fa3fc16
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456694"
---
# <a name="troubleshoot-azure-load-balancer"></a>Solución de problemas de Azure Load Balancer

En esta página se proporcionan soluciones de problemas para preguntas frecuentes sobre Azure Load Balancer Estándar y Básico. Para más información sobre Load Balancer Estándar, consulte [Introducción a Azure Load Balancer Estándar (versión preliminar)](load-balancer-standard-diagnostics.md).

Cuando el equilibrador de carga no tenga conectividad, los síntomas más comunes son los siguientes:

- Las máquinas virtuales detrás de Load Balancer no responden a los sondeos de estado. 
- Las máquinas virtuales detrás de Load Balancer no responden al tráfico del puerto configurado.

Cuando los clientes externos a las VM de back-end pasan a través del equilibrador de carga, se usará la dirección IP de los clientes para la comunicación. Asegúrese de que la dirección IP de los clientes se agregue a la lista de permitidos de NSG.

## <a name="no-outbound-connectivity-from-standard-internal-load-balancers-ilb"></a>no hay conectividad de salida desde los equilibradores de carga internos (ILB) estándar

**Validación y resolución**

Los ILB estándar son **seguros de manera predeterminada**. Los ILB básicos permitían conectarse a Internet a través de una dirección IP pública *oculta* denominada IP de acceso de salida predeterminada. Esto no es recomendable para cargas de trabajo de producción, ya que la dirección IP no es estática ni se bloquea mediante NSG de su propiedad. Si ha pasado recientemente de un ILB básico a uno estándar, debe crear una dirección IP pública de forma explícita mediante la configuración [Solo salida](egress-only.md), que bloquea la IP por medio de NSG. También puede usar una instancia de [NAT Gateway](../virtual-network/nat-gateway/nat-overview.md) en la subred. NAT Gateway es la solución recomendada para la salida.

## <a name="cant-change-backend-port-for-existing-lb-rule-of-a-load-balancer-that-has-virtual-machine-scale-set-deployed-in-the-backend-pool"></a>No se puede cambiar el puerto de back-end para la regla de LB existente de un equilibrador de carga que tenga un conjunto de escalado de máquinas virtuales implementado en el grupo de back-end.

### <a name="cause-the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-virtual-machine-scale-set"></a>Causa: No se puede modificar el puerto de back-end para una regla de equilibrio de carga que usa un sondeo de estado para el equilibrador de carga al que el conjunto de escalado de máquinas virtuales hace referencia.

**Resolución** Para cambiar el puerto, puede quitar el sondeo de estado mediante la actualización del conjunto de escalado de máquinas virtuales, actualizar el puerto y, a continuación, volver a configurar el sondeo de estado.

## <a name="small-traffic-is-still-going-through-load-balancer-after-removing-vms-from-backend-pool-of-the-load-balancer"></a>El tráfico pequeño sigue pasando a través del equilibrador de carga después de quitar las VM del grupo de back-end del equilibrador de carga.

### <a name="cause-vms-removed-from-backend-pool-should-no-longer-receive-traffic-the-small-amount-of-network-traffic-could-be-related-to-storage-dns-and-other-functions-within-azure"></a>Causa: Las VM que se quitan del grupo de back-end ya no deben recibir tráfico. La pequeña cantidad de tráfico de red podría estar relacionada con el almacenamiento, DNS y otras funciones de Azure.

Para comprobarlo, puede realizar un seguimiento de red. El FQDN que se usa para las cuentas de almacenamiento de blobs se muestra en las propiedades de cada cuenta de almacenamiento.  Desde una máquina virtual dentro de la suscripción de Azure, puede realizar una operación nslookup para determinar la dirección IP de Azure asignada a esa cuenta de almacenamiento.

## <a name="additional-network-captures"></a>Capturas de red adicionales

Si decide abrir un caso de soporte técnico, recopile la información siguiente para una resolución más rápida. Elija una sola máquina virtual de back-end para realizar las pruebas siguientes:

- Use ps ping de una de las máquinas virtuales de back-end de la red virtual para probar la respuesta del puerto de sondeo (ejemplo: ps ping 10.0.0.4:3389) y registre los resultados. 
- Si no se recibe respuesta con estas pruebas de ping, ejecute un seguimiento Netsh simultáneo en la máquina virtual de back-end y la máquina virtual de prueba de la red virtual mientras ejecuta PsPing y detenga el seguimiento Netsh.

## <a name="load-balancer-in-failed-state"></a>equilibrador de carga en estado erróneo

**Resolución**

- Una vez identificado el recurso que se encuentra en estado erróneo, vaya a [Azure Resource Explorer](https://resources.azure.com/) e identifique el recurso en este estado.
- Actualice el control de alternancia de la esquina superior derecha para que sea de lectura y escritura.
- Haga clic en Editar en el recurso en estado erróneo.
- Haga clic en PUT seguido de GET para asegurarse de que el estado de aprovisionamiento se haya actualizado a Correcto.
- Luego puede continuar con otras acciones, ya que el recurso ya no está en estado erróneo.

## <a name="next-steps"></a>Pasos siguientes

Si los pasos anteriores no resuelven el problema, abra una [incidencia de soporte técnico](https://azure.microsoft.com/support/options/).
