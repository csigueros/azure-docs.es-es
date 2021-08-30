---
title: Supervisión de Azure Application Gateway
description: Comience aquí para aprender a supervisar Azure Application Gateway
author: vhorne
ms.author: victorh
ms.service: application-gateway
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 7789590a2d13e6811e0ad8e7d2fa53cea9e0069a
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114295987"
---
<!-- VERSION 2.2
Template for the main monitoring article for Azure services. 
Keep the required sections and add/modify any content for any information specific to your service. 
This article should be in your TOC with the name *monitor-[Azure Application Gateway].md* and the TOC title "Monitor Azure Application Gateway". 
Put accompanying reference information into an article in the Reference section of your TOC with the name *monitor-[service-name]-reference.md* and the TOC title "Monitoring data". 
Keep the headings in this order. 
-->

# <a name="monitoring-azure-application-gateway"></a>Supervisión de Azure Application Gateway
<!-- REQUIRED. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Add to it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. 

En este artículo se describen los datos de supervisión generados por Azure Application Gateway. Azure Application Gateway utiliza [Azure Monitor](../azure-monitor/overview.md). Si no está familiarizado con las características de Azure Monitor comunes a todos los servicios de Azure que lo usan, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).


<!-- Optional diagram showing monitoring for your service. If you need help creating one, contact robb@microsoft.com -->

## <a name="monitoring-overview-page-in-azure-portal"></a>Página de información general de supervisión en Azure Portal
<!-- OPTIONAL. Please keep headings in this order -->
<!-- Most services can use this section unchanged. Edit it if there are any unique charges if your service has significant monitoring beyond Azure Monitor. -->

La página **Información general** de Azure Portal para cada Application Gateway incluye las métricas siguientes:

- Sum Total Requests
- Sum Failed Requests
- Sum Response Status by HttpStatus
- Sum Throughput
- Sum CurrentConnections
- Avg Healthy Host Count By BackendPool HttpSettings
- Avg Unhealthy Host Count By BackendPool HttpSettings

Este es solo un subconjunto de las métricas disponibles para Application Gateway. Para más información, consulte el artículo sobre [supervisión de referencia de datos de Azure Application Gateway](monitor-application-gateway-reference.md).


## <a name="azure-monitor-network-insights"></a>Azure Monitor Network Insights

<!-- OPTIONAL SECTION.  Only include if your service has an "insight" associated with it. Examples of insights include
  - CosmosDB https://docs.microsoft.com/azure/azure-monitor/insights/cosmosdb-insights-overview
  - If you still aren't sure, contact azmondocs@microsoft.com.>
-->

Algunos servicios en Azure tienen un panel de supervisión pregenerado enfocado especial en Azure Portal que proporciona un punto inicial para supervisar el servicio. Estos paneles especiales se denominan "conclusiones".

<!-- Give a quick outline of what your "insight page" provides and refer to another article that gives details -->

Azure Monitor Network Insights proporciona una vista completa del estado y las métricas de todos los recursos de red implementados (incluido Application Gateway) sin necesidad de realizar ninguna configuración. Para más información, consulte [Azure Monitor Network Insights](../azure-monitor/insights/network-insights-overview.md).

## <a name="monitoring-data"></a>Supervisión de datos 

<!-- REQUIRED. Please keep headings in this order -->
Azure Application Gateway recopila los mismos tipos de datos de supervisión que otros recursos de Azure que se describen en [Supervisión de datos de recursos de Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consulte el artículo sobre [supervisión de referencia de datos de Azure Application Gateway](monitor-application-gateway-reference.md) para más información sobre las métricas y las métricas de registros que crea Azure Application Gateway.

<!-- If your service has additional non-Azure Monitor monitoring data then outline and refer to that here. Also include that information in the data reference as appropriate. -->

## <a name="collection-and-routing"></a>Recopilación y enrutamiento

<!-- REQUIRED. Please keep headings in this order -->

Las métricas de la plataforma y el registro de actividad se recopilan y almacenan de forma automática, pero se pueden enrutar a otras ubicaciones mediante una configuración de diagnóstico.  

Los registros de recursos no se recopilan ni almacenan hasta que se crea una configuración de diagnóstico y se enrutan a una o varias ubicaciones.

<!-- Include any additional information on collecting logs.  The number of things that diagnostics settings control is expanding -->

Consulte [Creación de una configuración de diagnóstico para recopilar registros de plataforma y métricas en Azure](../azure-monitor/essentials/diagnostic-settings.md) para ver el proceso detallado para crear una configuración de diagnóstico mediante el Azure Portal, la CLI o PowerShell. Cuando se crea una configuración de diagnóstico, se especifican las categorías de registros que se van a recopilar. Las categorías de Azure Application Gateway se enumeran en la sección de [referencia de datos de supervisión de Azure Application Gateway](monitor-application-gateway-reference.md#resource-logs).

<!-- OPTIONAL: Add specific examples of configuration for this service. For example, CLI and PowerShell commands for creating diagnostic setting. Ideally, customers should set up a policy to automatically turn on collection for services. Azure monitor has Resource Manager template examples you can point to. See https://docs.microsoft.com/azure/azure-monitor/samples/resource-manager-diagnostic-settings.  Contact azmondocs@microsoft.com if you have questions.   -->

En las secciones siguientes se describen las métricas y los registros que se pueden recopilar.

## <a name="analyzing-metrics"></a>Análisis de métricas

<!-- REQUIRED. Please keep headings in this order 
If you don't support metrics, say so. Some services may be only onboarded to logs -->

Puede analizar las métricas de Azure Application Gateway con las de otros servicios de Azure mediante el explorador de métricas si abre **Métricas** en el menú de **Azure Monitor**. Consulte [Introducción al explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md) para más información sobre esta herramienta. 

<!-- Point to the list of metrics available in your monitor-service-reference article. -->
Para una lista de las métricas de plataforma recopiladas para Azure Application Gateway, consulte la sección de [supervisión de métricas de referencia de datos de Application Gateway](monitor-application-gateway-reference.md#metrics).  


Como referencia, puede ver una lista de [todas las métricas de recursos que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about how to use metrics explorer specifically for your service. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

## <a name="analyzing-logs"></a>Análisis de datos

<!-- REQUIRED. Please keep headings in this order
If you don't support resource logs, say so. Some services may be only onboarded to metrics and the activity log. -->

Los datos de los registros de Azure Monitor se almacenan en tablas, cada una con un conjunto propio de propiedades únicas.  

Todos los registros de recursos de Azure Monitor tienen los mismos campos seguidos de campos específicos del servicio. El esquema común se describe en [Esquema específico de servicio y común para los registros de recursos de Azure](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). 

El [registro de actividad](../azure-monitor/essentials/activity-log.md) es un registro de plataforma de Azure que proporciona conclusiones sobre los eventos del nivel de suscripción. Puede verlo de forma independiente o enrutarlo a registros de Azure Monitor, donde puede realizar consultas mucho más complejas mediante Log Analytics.  

Para una lista de los tipos de registros de recursos recopilados para Azure Application Gateway, consulte el artículo sobre [supervisión de referencia de datos de Azure Application Gateway](monitor-application-gateway-reference.md#resource-logs).

Para una lista de las tablas utilizadas por los registros de Azure Monitor y en la que Log Analytics puede realizar consultas, consulte el artículo sobre la [supervisión de referencias de datos de Azure Application Gateway](monitor-application-gateway-reference.md#azure-monitor-logs-tables).  

<!--  Optional: Call out additional information to help your customers. For example, you can include additional information here about log usage or what logs are most important. Remember that the UI is subject to change quite often so you will need to maintain these screenshots yourself if you add them in. -->

### <a name="sample-kusto-queries"></a>Ejemplos de consultas de Kusto

<!-- REQUIRED if you support logs. Please keep headings in this order -->
<!-- Add sample Log Analytics Kusto queries for your service. -->

> [!IMPORTANT]
> Al seleccionar **Registros** en el menú de Application Gateway, se abre Log Analytics con el ámbito de la consulta establecido en la instancia de Application Gateway actual. Esto significa que las consultas de registro solo incluirán datos de ese recurso. Si quiere ejecutar una consulta que incluya datos de otras instancias de Application Gateway o de otros servicios de Azure, seleccione **Registros** en el menú **Azure Monitor**. Consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](/azure/azure-monitor/log-query/scope/) para obtener más información.

<!-- REQUIRED: Include queries that are helpful for figuring out the health and state of your service. Ideally, use some of these queries in the alerts section. It's possible that some of your queries may be in the Log Analytics UI (sample or example queries). Check if so.  -->

Las consultas siguientes pueden ayudarlo a supervisar el recurso de Application Gateway. 

<!-- Put in a code section here. -->  
```Kusto
// Requests per hour 
// Count of the incoming requests on the Application Gateway. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart 
```

```kusto
// Failed requests per hour 
// Count of requests to which Application Gateway responded with an error. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by bin(TimeGenerated, 1h), _ResourceId
| render timechart
```

```kusto
// Top 10 Client IPs 
// Count of requests per client IP. 
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess"
| summarize AggregatedValue = count() by clientIP_s
| top 10 by AggregatedValue
```

```kusto
// Errors by user agent 
// Number of errors by user agent. 
// To create an alert for this query, click '+ New alert rule'
AzureDiagnostics
| where ResourceType == "APPLICATIONGATEWAYS" and OperationName == "ApplicationGatewayAccess" and httpStatus_d > 399
| summarize AggregatedValue = count() by userAgent_s, _ResourceId
| sort by AggregatedValue desc
```

## <a name="alerts"></a>Alertas

<!-- SUGGESTED: Include useful alerts on metrics, logs, log conditions or activity log. Ask your PMs if you don't know. 
This information is the BIGGEST request we get in Azure Monitor so do not avoid it long term. People don't know what to monitor for best results. Be prescriptive  
-->

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Puede establecer alertas en [métricas](../azure-monitor/alerts/alerts-metric-overview.md), [registros](../azure-monitor/alerts/alerts-unified-log.md) y el [registro de actividad](../azure-monitor/alerts/activity-log-alerts.md). Cada tipo de alerta tiene sus ventajas y desventajas.

<!-- only include next line if applications run on your service and work with App Insights. --> 

Si crea o ejecuta una aplicación que usa Application Gateway, [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) puede ofrecer otros tipos de alertas.
<!-- end -->

En la tabla siguiente se enumeran las reglas de alertas comunes y recomendadas para Application Gateway.

<!-- Fill in the table with metric and log alerts that would be valuable for your service. Change the format as necessary to make it more readable -->

**Application Gateway v1**

| Tipo de alerta | Condición | Descripción  |
|:---|:---|:---|
|Métrica|El uso de CPU supera el 80 %|En condiciones normales, el uso de CPU no debe superar con regularidad el 90 %, ya que esto puede provocar latencia en los sitios web hospedados tras Application Gateway e interrumpir la experiencia del cliente.|
|Métrica|El número de hosts incorrectos supera el umbral|Indica el número de servidores back-end que Application Gateway no puede sondear correctamente. Detecta problemas en los que las instancias de Application Gateway no pueden conectarse al servidor back-end. Envíe una alerta si este número supera el 20 % de la capacidad de back-end.|
|Métrica|El estado de respuesta (4xx, 5xx) supera el umbral|Cuando el estado de respuesta de Application Gateway es 4xx o 5xx. Podría haber una respuesta 4xx o 5xx ocasional debido a problemas transitorios. Debe observar la puerta de enlace en producción para determinar el umbral estático, o usar el umbral dinámico para la alerta.|
|Métrica|Las solicitudes con error superan el umbral|Cuando la métrica de solicitudes con error supera un umbral. Debe observar la puerta de enlace en producción para determinar el umbral estático, o usar el umbral dinámico para la alerta.|


**Application Gateway v2**

| Tipo de alerta | Condición | Descripción  |
|:---|:---|:---|
|Métrica|El uso de la unidad de proceso supera el 75 % del uso promedio|La unidad de proceso es la medición del uso de proceso por parte de la instancia de Application Gateway. Compruebe el promedio de uso de la unidad de proceso en el último mes y configure una alerta si supera el 75 % del uso.|
|Métrica|El uso de la unidad de capacidad supera el 75 % del uso máximo|Las unidades de capacidad representan el uso general de la puerta de enlace en términos de rendimiento, proceso y recuento de conexiones. Compruebe el uso máximo de la unidad de capacidad en el último mes y configure una alerta si supera el 75 % del uso.|
|Métrica|El número de hosts incorrectos supera el umbral|Indica el número de servidores back-end que Application Gateway no puede sondear correctamente. Detectará problemas en los que las instancias de Application Gateway no pueden conectarse al servidor back-end. Envíe una alerta si este número supera el 20 % de la capacidad de back-end.|
|Métrica|El estado de respuesta (4xx, 5xx) supera el umbral|Cuando el estado de respuesta de Application Gateway es 4xx o 5xx. Podría haber una respuesta 4xx o 5xx ocasional debido a problemas transitorios. Debe observar la puerta de enlace en producción para determinar el umbral estático, o usar el umbral dinámico para la alerta.|
|Métrica|Las solicitudes con error superan el umbral|Cuando la métrica de solicitudes con error supera el umbral. Debe observar la puerta de enlace en producción para determinar el umbral estático, o usar el umbral dinámico para la alerta.|
|Métrica|El tiempo de respuesta del último byte de back-end supera el umbral|Indica el intervalo de tiempo entre el inicio del establecimiento de una conexión con el servidor back-end y la recepción del último byte del cuerpo de la respuesta. Cree una alerta si la latencia de respuesta de back-end es mayor que un umbral determinado en relación con lo habitual.|
|Métrica|El tiempo total de Application Gateway supera el umbral|Este es el intervalo desde el momento en que Application Gateway recibe el primer byte de la solicitud HTTP hasta el momento en que se envía el último byte de respuesta al cliente. Debe crear una alerta si la latencia de respuesta de back-end es mayor que un umbral determinado en relación con lo habitual.|

## <a name="next-steps"></a>Pasos siguientes

<!-- Add additional links. You can change the wording of these and add more if useful.   -->

- Consulte el artículo de [supervisión de referencia de datos de Application Gateway](monitor-application-gateway-reference.md) para una referencia de las métricas, los registros y otros valores importantes creados por Application Gateway.

- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).