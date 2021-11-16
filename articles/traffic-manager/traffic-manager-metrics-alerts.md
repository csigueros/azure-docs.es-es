---
title: Métricas y alertas en Azure Traffic Manager
description: En este artículo, obtendrá información acerca de las métricas y alertas disponibles para Traffic Manager en Azure.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: duau
ms.openlocfilehash: eaa1ef35432a0e31376bcff8f50bc8bdffbfaf58
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054680"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Métricas y alertas de Traffic Manager

Traffic Manager le proporciona equilibrio de carga basado en DNS, que incluye varios métodos de enrutamiento y opciones de supervisión de puntos de conexión. En este artículo se describen las métricas y las alertas asociadas que están a disposición de los clientes. 

## <a name="metrics-available-in-traffic-manager"></a>Métricas disponibles en Traffic Manager 

Traffic Manager proporciona las siguientes métricas para cada perfil individual que los clientes pueden usar para comprender su uso de Traffic Manager y el estado de sus puntos de conexión con ese perfil.  

### <a name="queries-by-endpoint-returned"></a>Consultas devueltas por punto de conexión
Use [esta métrica](../azure-monitor/essentials/metrics-supported.md) para ver el número de consultas que procesa un perfil de Traffic Manager durante un periodo determinado. También puede ver la misma información con una granularidad de nivel de punto de conexión, lo que le ayudará a comprender cuántas veces se devolvió un punto de conexión en las respuestas a consultas desde Traffic Manager.

En el ejemplo siguiente, la figura 1 muestra todas las respuestas a consultas que devuelve el perfil de Traffic Manager. 

  
![Vista agregada de todas las consultas](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Figura 1: Vista agregada con todas las consultas*
  
La figura 2 muestra la misma información, sin embargo, se divide por puntos de conexión. Como resultado, puede ver el volumen de las respuestas a consultas en la que se devolvió un punto de conexión en particular.

![Métricas de Traffic Manager: vista dividida del volumen de consultas por punto de conexión](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Figura 2: Vista dividida con el volumen de consultas por punto de conexión devuelto*

## <a name="endpoint-status-by-endpoint"></a>Estado de punto de conexión por punto de conexión
Use [esta métrica](../azure-monitor/essentials/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) para conocer el estado de mantenimiento de los puntos de conexión en el perfil. Toma dos valores:
 - Use **1**, si el punto de conexión está activo.
 - Use **0**, si el punto de conexión está inactivo.

Esta métrica puede mostrarse como valor agregado que representa el estado de todas las métricas (figura 3), o bien se puede dividir (consulte la figura 4) para mostrar el estado de puntos de conexión específicos. En el caso anterior, si el nivel de agregación se selecciona como **Avg**, el valor de esta métrica es la media aritmética del estado de todos los puntos de conexión. Por ejemplo, si un perfil tiene dos puntos de conexión y solo uno es correcto, esta métrica tendrá un valor de **0,50**, tal como se muestra en la figura 3. 


![Métricas de Traffic Manager: vista compuesta del estado de los puntos de conexión](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Figura 3: Vista compuesta de la métrica de estado de los puntos de conexión, agregación "Avg" seleccionada*


![Métricas de Traffic Manager: vista dividida del estado de los puntos de conexión](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Figura 4: Vista dividida de las métricas de estado de los puntos de conexión*

Puede consumir estas métricas a través del portal del [servicio Azure Monitor](../azure-monitor/essentials/metrics-supported.md), la [API de REST](/rest/api/monitor/), [CLI de Azure](/cli/azure/monitor) y [Azure PowerShell](/powershell/module/az.applicationinsights), o a través de la sección de métricas de la experiencia del portal de Traffic Manager.

## <a name="alerts-on-traffic-manager-metrics"></a>Alertas relativas a métricas de Traffic Manager
Además de procesar y mostrar las métricas de Traffic Manager, Azure Monitor permite a los clientes configurar y recibir alertas asociadas a estas métricas. Puede elegir qué condiciones deben cumplirse en estas métricas para que se produzca una alerta, con qué frecuencia deben supervisarse esas condiciones y cómo se le deberían enviar las alertas. Para obtener más información, consulte la [documentación sobre alertas de Azure Monitor](../azure-monitor/alerts/alerts-metric.md).

La supervisión de alertas es importante para asegurarse de que el sistema envía notificaciones cuando los sondeos están fuera de servicio. La supervisión extremadamente confidencial puede ser una distracción. Traffic Manager implementa varios sondeos para aumentar la resistencia. El umbral de los estados de sondeo debe ser inferior a 0,5. Si el promedio del estado **en servicio** es inferior a 0,5 (lo que significa que menos del 50 % de los sondeos están en servicio), debe haber una alerta para un error de punto de conexión.

> [!NOTE]
> Se implementan varios sondeos para aumentar la resistencia. Si uno de los muchos sondeos que se envían está fuera de servicio, no refleja necesariamente que el punto de conexión está fuera de servicio. El punto de conexión solo se clasifica como fuera de servicio si la mayoría de los sondeos devueltos están fuera de servicio.

La siguiente es un ejemplo de una configuración de alerta.

:::image type="content" source="./media/traffic-manager-metrics-alerts/alert-example.png" alt-text="Captura de pantalla del ejemplo de alerta de umbral de sondeo.":::

Para más información acerca de los sondeos y la supervisión, consulte [Supervisión de los puntos de conexión de Traffic Manager](traffic-manager-monitoring.md).

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre el [servicio Azure Monitor](../azure-monitor/essentials/metrics-supported.md)
- Más información sobre cómo [crear un gráfico con Azure Monitor](../azure-monitor/essentials/metrics-getting-started.md#create-your-first-metric-chart)