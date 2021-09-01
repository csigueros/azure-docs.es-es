---
title: Referencia de datos de supervisión de Load Balancer
titleSuffix: Azure Load Balancer
description: Material de referencia importante necesario al supervisar Load Balancer
author: asudbring
ms.topic: reference
ms.author: allensu
ms.service: load-balancer
ms.custom: subject-monitoring
ms.date: 06/29/2021
ms.openlocfilehash: ac5a7394b13ac34ad9d398458627765316369de9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296518"
---
# <a name="monitoring-load-balancer-data-reference"></a>Referencia de datos de supervisión de Load Balancer

Consulte [Supervisión de Load Balancer](monitor-load-balancer.md) para más información sobre la recopilación y el análisis de los datos de supervisión de Load Balancer.

## <a name="metrics"></a>Métricas

### <a name="load-balancer-metrics"></a>Métricas de Load Balancer 

| Métrica | Tipo de recurso | Descripción | Agregación recomendada |
| ------ | ------------- | ----------- | ----------------------- |
| Disponibilidad de la ruta de acceso de datos | Equilibrador de carga interno y público |  Load Balancer Estándar usa continuamente la ruta de acceso a los datos desde una región hasta el servidor front-end del equilibrador de carga y, finalmente, hasta la pila de SDN que respalda la máquina virtual. Siempre que permanezcan las instancias correctas, la medida sigue la misma ruta de acceso que el tráfico con equilibrio de carga de las aplicaciones. También se valida la ruta de acceso a los datos que usan los clientes. La medida es invisible para la aplicación y no interfiere con otras operaciones. | Average |
| Estado del sondeo de mantenimiento | Equilibrador de carga interno y público | Load Balancer Estándar usa un servicio de sondeo de mantenimiento distribuido que supervisa el mantenimiento del punto de conexión de la aplicación de acuerdo con la configuración. Esta métrica proporciona una vista agregada o filtrada por punto de conexión de cada punto de conexión de instancia del grupo del equilibrador de carga. Puede ver cómo Load Balancer observa el estado de su aplicación según se indica en la configuración de sondeo de estado. | Average |
| Recuento SYN (sincronizar) | Equilibrador de carga interno y público | Load Balancer Estándar usa un servicio de sondeo de mantenimiento distribuido que supervisa el mantenimiento del punto de conexión de la aplicación de acuerdo con la configuración. Esta métrica proporciona una vista agregada o filtrada por punto de conexión de cada punto de conexión de instancia del grupo del equilibrador de carga. Puede ver cómo Load Balancer observa el estado de su aplicación según se indica en la configuración de sondeo de estado. | Average |
| Recuento de conexiones SNAT | Equilibrador de carga público | Load Balancer Estándar informa del número de flujos salientes enmascarados en el servidor front-end de dirección IP pública. Los puertos de traducción de direcciones de red de origen (SNAT) son un recurso agotable. Esta métrica puede proporcionar una indicación de la dependencia que su aplicación tiene de SNAT en los flujos salientes originados. Los contadores de los flujos de salida de SNAT que se realizaron con éxito y los que tuvieron algún error se notifican y se pueden utilizar para solucionar problemas y comprender el estado de los flujos de salida. | Sum |
| Puertos SNAT asignados | Equilibrador de carga público | Standard Load Balancer informa del número de puertos SNAT asignados por cada instancia de back-end. | Media |
| Puertos SNAT usados | Equilibrador de carga público | Standard Load Balancer informa del número de puertos SNAT usados por instancia de back-end. | Average |
| Recuento de bytes | Equilibrador de carga interno y público | Load Balancer Estándar informa de los datos procesados por front-end. Es posible que observe que los bytes no se distribuyen equitativamente entre las instancias de back-end. Esto es algo esperado, ya que el algoritmo de Azure Load Balancer se basa en flujos. | Sum |
| Recuento de paquetes | Equilibrador de carga interno y público | Load Balancer Estándar informa de los paquetes procesados por front-end. | Sum |

Para más información, consulte una lista de [todas las métricas de la plataforma que se admiten en Azure Monitor para Load Balancer](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkloadbalancers).

## <a name="metric-dimensions"></a>Dimensiones de métricas

Para obtener más información sobre las dimensiones de métricas, consulte [Métricas multidimensionales](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Load Balancer tiene las siguientes dimensiones asociadas a sus métricas.

| Nombre de dimensión | Descripción |
| -------------- | ----------- |
| Dirección IP de front-end | Dirección IP de front-end de las reglas de equilibrio de carga pertinentes |
| Puerto de front-end | Puerto de front-end de las reglas de equilibrio de carga pertinentes | 
| IP de back-end | Dirección IP de back-end de las reglas de equilibrio de carga pertinentes |
| Puerto back-end | Puerto de back-end de las reglas de equilibrio de carga pertinentes |
| Tipo de protocolo | Protocolo de la regla de equilibrio de carga pertinente, puede ser TCP o UDP. |
| Dirección | Dirección en la que fluye el tráfico. Puede ser de entrada o de salida. | 
| Estado de conexión | Estado de la conexión SNAT, puede ser pendiente, correcto o con errores. | 

## <a name="resource-logs"></a>Registros del recurso

Los registros de recursos no son compatibles actualmente con Azure Load Balancer.

## <a name="azure-monitor-logs-tables"></a>Tablas de registros de Azure Monitor
### <a name="diagnostics-tables"></a>Tablas de diagnóstico

Los tablas de diagnóstico no son compatibles actualmente con Azure Load Balancer.
## <a name="activity-log"></a>Registro de actividades

En la tabla siguiente, se enumeran las operaciones relacionadas con Load Balancer que se pueden crear en el registro de actividad.

| Operación | Descripción |
| --- | --- |
| Microsoft.Network/loadBalancers/read | Obtiene una definición del equilibrador de carga |
| Microsoft.Network/loadBalancers/write | Crea un equilibrador de carga o actualiza uno que ya existe |
| Microsoft.Network/loadBalancers/delete | Elimina un equilibrador de carga |
| Microsoft.Network/loadBalancers/backendAddressPools/queryInboundNatRulePortMapping/action | Consulta de la asignación de puertos de la regla NAT de entrada. |
| Microsoft.Network/loadBalancers/backendAddressPools/read | Obtiene una definición de grupo de direcciones de back-end del equilibrador de carga |
| Microsoft.Network/loadBalancers/backendAddressPools/write | Crea un grupo de direcciones del back-end del equilibrador de carga o actualiza un grupo de direcciones del back-end del equilibrador de carga existente. |
| Microsoft.Network/loadBalancers/backendAddressPools/delete | Elimina un grupo de direcciones de back-end del equilibrador de carga. |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Se une a un grupo de direcciones de back-end del equilibrador de carga. No genera alertas. |
| Microsoft.Network/loadBalancers/backendAddressPools/backendPoolAddresses/read | Enumera las direcciones de back-end del grupo de direcciones del back-end de Load Balancer. |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Obtiene una definición de configuración de dirección IP de front-end del equilibrador de carga |
| Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Combina una configuración de dirección IP de front-end del equilibrador de carga. No genera alertas. |
| Microsoft.Network/loadBalancers/inboundNatPools/read | Obtiene una definición de conjuntos NAT de entrada del equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Se une a conjuntos NAT de entrada del equilibrador de carga. No genera alertas. |
| Microsoft.Network/loadBalancers/inboundNatRules/read | Obtiene una definición de reglas NAT de entrada del equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/write | Crea una regla NAT de entrada del equilibrador de carga o actualiza una que ya existe |
| Microsoft.Network/loadBalancers/inboundNatRules/delete | Elimina una regla NAT de entrada del equilibrador de carga |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Se une a una regla NAT de entrada del equilibrador de carga. No genera alertas. |
| Microsoft.Network/loadBalancers/loadBalancingRules/read | Obtiene una definición de regla de equilibrado de carga del equilibrador de carga |
| Microsoft.Network/loadBalancers/networkInterfaces/read | Obtiene referencias a todas las interfaces de red en un equilibrador de carga |
| Microsoft.Network/loadBalancers/outboundRules/read | Obtiene una definición de reglas de salida del equilibrador de carga. |
| Microsoft.Network/loadBalancers/probes/read | Obtiene un sondeo del equilibrador de carga |
| Microsoft.Network/loadBalancers/probes/join/action | Permite usar sondeos de un equilibrador de carga. Por ejemplo, con este permiso, la propiedad healthProbe de un conjunto de escalado de máquinas virtuales puede hacer referencia al sondeo. No genera alertas. |
| Microsoft.Network/loadBalancers/virtualMachines/read | Obtiene referencias a todas las máquinas virtuales de un equilibrador de carga |

Para más información sobre el esquema de las entradas del registro de actividad, consulte [Esquema del registro de actividad](../azure-monitor/essentials/activity-log-schema.md). 

## <a name="see-also"></a>Consulte también

- Consulte [Supervisión de Azure Load Balancer](./monitor-load-balancer.md) para ver una descripción de la supervisión de Azure Load Balancer.