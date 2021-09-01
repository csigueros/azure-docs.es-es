---
title: Procedimiento para consultar registros desde Container Insights
description: Container Insights recopila datos de registro y métricas, y en este artículo se describen los registros y se incluyen consultas de ejemplo.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: a8df32440cf5e45184c62e4e05567d500d7d5a32
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780980"
---
# <a name="how-to-query-logs-from-container-insights"></a>Procedimiento para consultar registros desde Container Insights

Container Insights recopila métricas de rendimiento, datos de inventario e información de estado de mantenimiento de los contenedores y los hosts de contenedor. Los datos se recopilan cada tres minutos y se reenvían al área de trabajo de Log Analytics en Azure Monitor donde están disponibles para las [consultas de registro](../logs/log-query-overview.md) con el uso de [Log Analytics](../logs/log-analytics-overview.md) en Azure Monitor. Estos datos se pueden aplicar a escenarios que incluyen la planeación de la migración, el análisis de la capacidad, la detección y la solución de problemas de rendimiento a petición. Los registros de Azure Monitor pueden ayudarle a buscar tendencias, diagnosticar cuellos de botellas, realizar previsiones o correlacionar datos, que pueden servirle para determinar si la configuración actual del clúster funciona óptimamente.

Consulte [Uso de consultas en Log Analytics de Azure Monitor](../logs/queries.md) para obtener información sobre cómo usar estas consultas y [Tutorial de Log Analytics](../logs/log-analytics-tutorial.md) para acceder a un tutorial completo sobre cómo usar Log Analytics para ejecutar consultas y trabajar con sus resultados.

## <a name="open-log-analytics"></a>Apertura de Log Analytics
Hay varias opciones para iniciar Log Analytics, cada una a partir de un [ámbito](../logs/scope.md) diferente. Para acceder a todos los datos del área de trabajo, seleccione **Registros** en el menú **Supervisar**. Para limitar los datos a un único clúster de Kubernetes, seleccione **Registros** en el menú de ese clúster. 

:::image type="content" source="media/container-insights-log-query/start-log-analytics.png" alt-text="Inicio de Log Analytics" lightbox="media/container-insights-log-query/start-log-analytics.png":::

## <a name="existing-log-queries"></a>Consultas de registro existentes
No es necesario que sepa escribir una consulta de registro para usar Log Analytics. Hay varias consultas compiladas previamente que puede seleccionar y ejecutar sin modificaciones o usar como inicio de una consulta personalizada. Haga clic en **Consultas** en la parte superior de la pantalla de Log Analytics y vea las consultas con un **Tipo de recurso** de **Servicio de Kubernetes**. 

:::image type="content" source="media/container-insights-log-query/log-analytics-queries.png" alt-text="Consultas de Log Analytics para Kubernetes" lightbox="media/container-insights-log-query/log-analytics-queries.png":::

## <a name="container-tables"></a>Tablas de contenedor
Consulte la [referencia de tabla de Azure Monitor](/azure/azure-monitor/reference/tables/tables-resourcetype#kubernetes-services) para obtener una lista de tablas y sus descripciones detalladas que usa Container Insights. Todas estas tablas están disponibles para las consultas de registro.


## <a name="example-log-queries"></a>Consultas de registro de ejemplo
A menudo resulta útil crear consultas que comiencen con un ejemplo o dos y luego modificarlas para ajustarlas a sus requisitos. Para ayudarle a crear consultas más avanzadas, puede experimentar con las siguientes consultas de ejemplo:

### <a name="list-all-of-a-containers-lifecycle-information"></a>Se muestra toda la información del ciclo de vida de un contenedor

```kusto
ContainerInventory
| project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime
| render table
```

### <a name="kubernetes-events"></a>Eventos de Kubernetes

``` kusto
KubeEvents_CL
| where not(isempty(Namespace_s))
| sort by TimeGenerated desc
| render table
```
### <a name="image-inventory"></a>Inventario de imágenes

``` kusto
ContainerImageInventory
| summarize AggregatedValue = count() by Image, ImageTag, Running
```

### <a name="container-cpu"></a>CPU de contenedor

``` kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" 
| summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName 
```

### <a name="container-memory"></a>Memoria de contenedor

```kusto
Perf
| where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes"
| summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName
```

### <a name="requests-per-minute-with-custom-metrics"></a>Solicitudes por minuto con métricas personalizadas

```kusto
InsightsMetrics
| where Name == "requests_count"
| summarize Val=any(Val) by TimeGenerated=bin(TimeGenerated, 1m)
| sort by TimeGenerated asc<br> &#124; project RequestsPerMinute = Val - prev(Val), TimeGenerated
| render barchart 
```
### <a name="pods-by-name-and-namespace"></a>Pods por nombre y espacio de nombres

```kusto
let startTimestamp = ago(1h);
KubePodInventory
| where TimeGenerated > startTimestamp
| project ContainerID, PodName=Name, Namespace
| where PodName contains "name" and Namespace startswith "namespace"
| distinct ContainerID, PodName
| join
(
    ContainerLog
    | where TimeGenerated > startTimestamp
)
on ContainerID
// at this point before the next pipe, columns from both tables are available to be "projected". Due to both
// tables having a "Name" column, we assign an alias as PodName to one column which we actually want
| project TimeGenerated, PodName, LogEntry, LogEntrySource
| extend TimeGenerated = TimeGenerated - 21600s | order by TimeGenerated desc
| summarize by TimeGenerated, LogEntry
| order by TimeGenerated desc
```

### <a name="pod-scale-out-hpa"></a>Escalabilidad horizontal de pods (HPA)
Devuelve el número de réplicas escaladas horizontalmente en cada implementación. Calcula el porcentaje de escalado horizontal con el número máximo de réplicas configuradas en HPA.


```kusto
let _minthreshold = 70; // minimum threshold goes here if you want to setup as an alert
let _maxthreshold = 90; // maximum threshold goes here if you want to setup as an alert
let startDateTime = ago(60m);
KubePodInventory
| where TimeGenerated >= startDateTime 
| where Namespace !in('default', 'kube-system') // List of non system namespace filter goes here.
| extend labels = todynamic(PodLabel)
| extend deployment_hpa = reverse(substring(reverse(ControllerName), indexof(reverse(ControllerName), "-") + 1))
| distinct tostring(deployment_hpa)
| join kind=inner (InsightsMetrics 
    | where TimeGenerated > startDateTime 
    | where Name == 'kube_hpa_status_current_replicas'
    | extend pTags = todynamic(Tags) //parse the tags for values
    | extend ns = todynamic(pTags.k8sNamespace) //parse namespace value from tags
    | extend deployment_hpa = todynamic(pTags.targetName) //parse HPA target name from tags
    | extend max_reps = todynamic(pTags.spec_max_replicas) // Parse maximum replica settings from HPA deployment
    | extend desired_reps = todynamic(pTags.status_desired_replicas) // Parse desired replica settings from HPA deployment
    | summarize arg_max(TimeGenerated, *) by tostring(ns), tostring(deployment_hpa), Cluster=toupper(tostring(split(_ResourceId, '/')[8])), toint(desired_reps), toint(max_reps), scale_out_percentage=(desired_reps * 100 / max_reps)
    //| where scale_out_percentage > _minthreshold and scale_out_percentage <= _maxthreshold
    )
    on deployment_hpa
```

### <a name="nodepool-scale-outs"></a>Escalados horizontales de Nodepool 
Devuelve el número de nodos activos en cada grupo de nodos. Calcula el número de nodos activos disponibles y la configuración máxima del nodo en la configuración del escalador automático para determinar el porcentaje de escalado horizontal. Consulte líneas comentadas en la consulta y úsela para una regla de alertas de **número de resultados**.

```kusto
let nodepoolMaxnodeCount = 10; // the maximum number of nodes in your auto scale setting goes here.
let _minthreshold = 20;
let _maxthreshold = 90;
let startDateTime = 60m;
KubeNodeInventory
| where TimeGenerated >= ago(startDateTime)
| extend nodepoolType = todynamic(Labels) //Parse the labels to get the list of node pool types
| extend nodepoolName = todynamic(nodepoolType[0].agentpool) // parse the label to get the nodepool name or set the specific nodepool name (like nodepoolName = 'agentpool)'
| summarize nodeCount = count(Computer) by ClusterName, tostring(nodepoolName), TimeGenerated
//(Uncomment the below two lines to set this as an log search alert)
//| extend scaledpercent = iff(((nodeCount * 100 / nodepoolMaxnodeCount) >= _minthreshold and (nodeCount * 100 / nodepoolMaxnodeCount) < _maxthreshold), "warn", "normal")
//| where scaledpercent == 'warn'
| summarize arg_max(TimeGenerated, *) by nodeCount, ClusterName, tostring(nodepoolName)
| project ClusterName, 
    TotalNodeCount= strcat("Total Node Count: ", nodeCount),
    ScaledOutPercentage = (nodeCount * 100 / nodepoolMaxnodeCount),  
    TimeGenerated, 
    nodepoolName
```

### <a name="system-containers-replicaset-availability"></a>Disponibilidad de contenedores del sistema (replicaset)
Devuelve los contenedores del sistema (replicaset) e informa del porcentaje no disponible. Consulte líneas comentadas en la consulta y úsela para una regla de alertas de **número de resultados**.

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'ReplicaSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

### <a name="system-containers-daemonsets-availability"></a>Disponibilidad de contenedores del sistema (daemonsets)
Devuelve los contenedores del sistema (daemonsets) e informa del porcentaje no disponible. Consulte líneas comentadas en la consulta y úsela para una regla de alertas de **número de resultados**.

```kusto
let startDateTime = 5m; // the minimum time interval goes here
let _minalertThreshold = 50; //Threshold for minimum and maximum unavailable or not running containers
let _maxalertThreshold = 70;
KubePodInventory
| where TimeGenerated >= ago(startDateTime)
| distinct ClusterName, TimeGenerated
| summarize Clustersnapshot = count() by ClusterName
| join kind=inner (
    KubePodInventory
    | where TimeGenerated >= ago(startDateTime)
    | where Namespace in('default', 'kube-system') and ControllerKind == 'DaemonSet' // the system namespace filter goes here
    | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus, ServiceName, PodLabel, Namespace, ContainerStatus
    | summarize arg_max(TimeGenerated, *), TotalPODCount = count(), podCount = sumif(1, PodStatus == 'Running' or PodStatus != 'Running'), containerNotrunning = sumif(1, ContainerStatus != 'running')
        by ClusterName, TimeGenerated, ServiceName, PodLabel, Namespace
    )
    on ClusterName
| project ClusterName, ServiceName, podCount, containerNotrunning, containerNotrunningPercent = (containerNotrunning * 100 / podCount), TimeGenerated, PodStatus, PodLabel, Namespace, Environment = tostring(split(ClusterName, '-')[3]), Location = tostring(split(ClusterName, '-')[4]), ContainerStatus
//Uncomment the below line to set for automated alert
//| where PodStatus == "Running" and containerNotrunningPercent > _minalertThreshold and containerNotrunningPercent < _maxalertThreshold
| summarize arg_max(TimeGenerated, *), c_entry=count() by PodLabel, ServiceName, ClusterName
//Below lines are to parse the labels to identify the impacted service/component name
| extend parseLabel = replace(@'k8s-app', @'k8sapp', PodLabel)
| extend parseLabel = replace(@'app.kubernetes.io/component', @'appkubernetesiocomponent', parseLabel)
| extend parseLabel = replace(@'app.kubernetes.io/instance', @'appkubernetesioinstance', parseLabel)
| extend tags = todynamic(parseLabel)
| extend tag01 = todynamic(tags[0].app)
| extend tag02 = todynamic(tags[0].k8sapp)
| extend tag03 = todynamic(tags[0].appkubernetesiocomponent)
| extend tag04 = todynamic(tags[0].aadpodidbinding)
| extend tag05 = todynamic(tags[0].appkubernetesioinstance)
| extend tag06 = todynamic(tags[0].component)
| project ClusterName, TimeGenerated,
    ServiceName = strcat( ServiceName, tag01, tag02, tag03, tag04, tag05, tag06),
    ContainerUnavailable = strcat("Unavailable Percentage: ", containerNotrunningPercent),
    PodStatus = strcat("PodStatus: ", PodStatus), 
    ContainerStatus = strcat("Container Status: ", ContainerStatus)
```

## <a name="resource-logs"></a>Registros del recurso
Los registros de recursos de AKS se almacenan en la tabla [AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics). Puede distinguir distintos registros con la columna **Categoría**. Consulte [Registros de recursos de referencia de AKS](../../aks/monitor-aks-reference.md) para obtener una descripción de cada categoría. En los ejemplos siguientes se requiere una extensión de diagnóstico para enviar registros de recursos para un clúster de AKS a un área de trabajo de Log Analytics. Consulte [Supervisión de la configuración](../../aks/monitor-aks.md#configure-monitoring) para obtener más información.

### <a name="api-server-logs"></a>Registros de servidor de API

```kusto
AzureDiagnostics 
| where Category == "kube-apiserver"
```

### <a name="count-logs-for-each-category"></a>Recuento de registros para cada categoría

```kusto
AzureDiagnostics
| where ResourceType == "MANAGEDCLUSTERS"
| summarize count() by Category
```

## <a name="query-prometheus-metrics-data"></a>Consulta de los datos de las métricas de Prometheus

El ejemplo siguiente es una consulta de métricas de Prometheus que muestra lecturas de disco por segundo por disco y por nodo.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

Para ver las métricas de Prometheus que ha extraído Azure Monitor filtradas por espacio de nombres, especifique "prometheus". A continuación, se muestra una consulta de ejemplo para ver las métricas de Prometheus desde el espacio de nombres `default` de Kubernetes.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

Los datos de Prometheus también se pueden consultar directamente en función del nombre.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Consulta de errores de configuración o extracción

Para investigar los errores de configuración o extracción, la consulta de ejemplo siguiente devuelve los eventos informativos de la tabla `KubeMonAgentEvents`.

```
KubeMonAgentEvents | where Level != "Info" 
```

La salida muestra unos resultados similares al ejemplo siguiente:

![Registro de los resultados de la consulta de eventos informativos del agente](./media/container-insights-log-query/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Pasos siguientes

Container Insights no incluye un conjunto de alertas predefinido. Revise [Creación de alertas de rendimiento con Container Insights](./container-insights-log-alerts.md) para obtener información sobre cómo crear alertas recomendadas en caso de uso elevado de CPU y memoria a fin de permitir las operaciones de desarrollo o los procesos y procedimientos operativos.
