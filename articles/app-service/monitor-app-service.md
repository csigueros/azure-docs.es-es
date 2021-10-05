---
title: Supervisión de App Service con Azure Monitor
description: Aquí puede empezar a aprender cómo supervisar App Service
author: msangapu-msft
ms.author: msangapu
ms.topic: article
ms.service: app-service
ms.custom: subject-monitoring
ms.date: 04/16/2021
ms.openlocfilehash: 1c3ff0fb224d5c1f8fe0133f184044b8aa5b30a3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837145"
---
# <a name="monitoring-app-service"></a>Supervisión de App Service

Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. En este artículo se describen los datos de supervisión que genera App Service y se envían a [Azure Monitor](../azure-monitor/overview.md). También puede usar los [diagnósticos integrados para supervisar los recursos](troubleshoot-diagnostic-logs.md) para ayudar a depurar aplicaciones de App Service. Si no está familiarizado con las características de Azure Monitor comunes a todos los servicios de Azure que lo usan, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).

> [!NOTE]
> La integración de la configuración de diagnóstico con App Service se encuentra en [versión preliminar](https://aka.ms/appsvcblog-azmon).
>

## <a name="monitoring-data"></a>Supervisión de datos 

App Service recopila los mismos tipos de datos de supervisión que otros recursos de Azure que se describen en [Supervisión de datos de recursos de Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data). 

Consulte el artículo sobre la [supervisión de la referencia de datos de *App Service*](monitor-app-service-reference.md) para obtener información detallada sobre las métricas y las métricas de registros creadas por App Service.

App Service también proporciona diagnósticos integrados para ayudar a depurar aplicaciones. Para más información sobre cómo habilitar los registros integrados, consulte [Habilitación del registro de diagnóstico](troubleshoot-diagnostic-logs.md). Para supervisar las instancias de App Service, consulte [Supervisión de instancias de App Service mediante la comprobación de estado](monitor-instances-health-check.md).

## <a name="collection-and-routing"></a>Recopilación y enrutamiento

Las métricas de la plataforma y el registro de actividad se recopilan y almacenan de forma automática, pero se pueden enrutar a otras ubicaciones mediante una configuración de diagnóstico.  

Los registros de recursos no se recopilan ni almacenan hasta que se crea una configuración de diagnóstico y se enrutan a una o varias ubicaciones.

Consulte [Creación de una configuración de diagnóstico para recopilar registros de plataforma y métricas en Azure](../azure-monitor/essentials/diagnostic-settings.md) para ver el proceso detallado para crear una configuración de diagnóstico mediante el Azure Portal, la CLI o PowerShell. Cuando se crea una configuración de diagnóstico, se especifican las categorías de registros que se van a recopilar. Las categorías de *App Service* se enumeran en la [referencia de datos de supervisión de App Service](monitor-app-service-reference.md#resource-logs).

En las secciones siguientes se describen las métricas y los registros que se pueden recopilar.

## <a name="analyzing-metrics"></a>Análisis de métricas

Las métricas de *App Service* se pueden analizar con métricas de otros servicios de Azure mediante el Explorador de métricas. Para ello, hay que abrir **Métricas** en el menú de **Azure Monitor**. Consulte [Introducción al explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md) para más información sobre esta herramienta. 

Para ver una lista de las métricas de la plataforma que se recopilan para App Service, consulte [Supervisión de las métricas de referencia de datos de App Service](monitor-app-service-reference.md#metrics).  

Como referencia, puede ver una lista de [todas las métricas de recursos que se admiten en Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

## <a name="analyzing-logs"></a>Análisis de datos

Los datos de los registros de Azure Monitor se almacenan en tablas, cada una con un conjunto propio de propiedades únicas.  

Todos los registros de recursos de Azure Monitor tienen los mismos campos seguidos de campos específicos del servicio. El esquema común se describe en [Esquema de registros de recursos de Azure Monitor](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview).

El [registro de actividad](../azure-monitor/essentials/activity-log.md) es un tipo de registro de plataforma que proporciona información de eventos de nivel de suscripción. Puede verlo como algo independiente o enrutado a los registros de Azure Monitor. El enrutamiento a los registros de Azure Monitor proporciona la ventaja de usar Log Analytics para ejecutar consultas complejas.

Para ver una lista de los tipos de registros de recursos que se recopilan para App Service, consulte [Supervisión de la referencia de datos de App Service](monitor-app-service-reference.md#resource-logs).  

Para obtener una lista de las tablas que se pueden consultar que utilizan Log Analytics y los registros de Azure Monitor, consulte [Referencia de datos de App Service](monitor-app-service-reference.md#azure-monitor-logs-tables).  

### <a name="sample-kusto-queries"></a>Ejemplos de consultas de Kusto

> [!IMPORTANT]
> Al seleccionar **Registros** en el menú de App Service, Log Analytics se abre con el ámbito de la consulta establecido en el recurso actual. Esto significa que las consultas de registro solo incluirán datos de ese recurso. Si desea ejecutar una consulta que incluya datos de otros recursos o servicios de Azure, seleccione **Registros** en el menú **Azure Monitor**. Consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](/azure/azure-monitor/log-query/scope/) para obtener más información.

La siguiente consulta de ejemplo puede ayudarle a supervisar los registros de aplicaciones mediante `AppServiceAppLogs`:

```Kusto
AppServiceAppLogs 
| project CustomLevel, _ResourceId
| summarize count() by CustomLevel, _ResourceId
```

La siguiente consulta de ejemplo puede ayudarle a supervisar los registros HTTP mediante `AppServiceHTTPLogs`, donde `HTTP response code` es `500` o superior:

```Kusto
AppServiceHTTPLogs 
//| where ResourceId = "MyResourceId" // Uncomment to get results for a specific resource Id when querying over a group of Apps
| where ScStatus >= 500
| reduce by strcat(CsMethod, ':\\', CsUriStem)
```

La siguiente consulta de ejemplo puede ayudarle a supervisar los errores HTTP 500, ya que combina `AppServiceConsoleLogs` y `AppserviceHTTPLogs`:

```Kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;   
```

Para más consultas de ejemplo, consulte [Consultas de Azure Monitor para App Service](https://github.com/microsoft/AzureMonitorCommunity/tree/master/Azure%20Services/App%20Services/Queries).

## <a name="alerts"></a>Alertas

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Puede establecer alertas en [métricas](../azure-monitor/alerts/alerts-metric-overview.md), [registros](../azure-monitor/alerts/alerts-unified-log.md) y el [registro de actividad](../azure-monitor/alerts/activity-log-alerts.md).

Si ejecuta una aplicación en App Service, [Azure Monitor Application Insights](../azure-monitor/overview.md#application-insights) puede ofrecer tipos adicionales de alertas.

En la tabla siguiente se enumeran las reglas de alertas de métricas comunes y recomendadas para App Service.

| Tipo de alerta | Condición | Ejemplos  |
|:---|:---|:---|
| Métrica | Media de conexiones| Cuando el número de conexiones supera un valor establecido|
| Métrica | HTTP 404| Cuando las respuestas HTTP 404 superan un valor establecido|
| Métrica | Errores de servidor HTTP| Cuando los errores HTTP 5xx superan un valor establecido|
| Registro de actividad | Creación o actualización de una aplicación web | Cuando se crea o actualiza una aplicación|
| Registro de actividad | Eliminar aplicación web | Cuando se elimina una aplicación|
| Registro de actividad | Reinicio de una aplicación web| Cuando se reinicia una aplicación|
| Registro de actividad | Detener aplicación web| Cuando se detiene una aplicación|

## <a name="next-steps"></a>Pasos siguientes

- En [Supervisión de referencia de datos de App Service](monitor-app-service-reference.md), encontrará una referencia de las métricas, los registros y otros valores importantes creados por App Service.

- Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).
