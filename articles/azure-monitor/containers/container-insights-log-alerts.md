---
title: Alertas de registro de Container Insights | Microsoft Docs
description: En este artículo se describe cómo crear alertas de registro personalizadas para el uso de memoria y de CPU desde Container Insights.
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 82d6629ba903b656db9932b3c6bd6f5a2b92ea6a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738488"
---
# <a name="how-to-create-log-alerts-from-container-insights"></a>Procedimiento para crear alertas de registro desde Container Insights

Container Insights supervisa el rendimiento de las cargas de trabajo de contenedor implementadas en clústeres de Kubernetes administrados o autoadministrados. Para alertar sobre lo que importa, en este artículo se describe cómo crear alertas basadas en registro para las siguientes situaciones con los clústeres de AKS:

- Cuando el uso de CPU o memoria en los nodos del clúster supera un umbral
- Cuando el uso de CPU o memoria en cualquier contenedor dentro de un controlador supera un umbral en comparación con un límite que se establece en el recurso correspondiente
- Recuentos del nodo de estado *NotReady*
- Recuentos de fase de pod *Erróneo*, *Pendiente*, *Desconocido*, *Ejecución* o *Correcto*
- Cuando el espacio libre en disco de los nodos del clúster supera un umbral

Para generar una alerta sobre el uso elevado de CPU o de memoria o sobre poco espacio libre en disco en los nodos del clúster, use las consultas que se proporcionan para crear una alerta de métrica o una alerta de medida de métrica. Mientras que las alertas de métricas tienen una latencia menor que las alertas de registro, estas proporcionan consultas avanzadas y una mayor sofisticación. Las consultas de alertas de registro comparan una fecha y hora con la actual mediante el operador *now* y retroceden una hora. (Container Insights almacena todas las fechas en el formato Hora universal coordinada [UTC]).

> [!IMPORTANT]
> La mayoría de las reglas de alerta tienen un costo que depende del tipo de regla, de las dimensiones que incluye y de la frecuencia con que se ejecuta. Consulte **Reglas de alertas** en [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) antes de crear reglas de alerta.

Si no está familiarizado con las alertas en Azure Monitor, consulte [Información general sobre las alertas en Microsoft Azure](../alerts/alerts-overview.md) antes de empezar. Para más información acerca de las alertas que usan consultas de registro, consulte [Alertas de registro en Azure Monitor](../alerts/alerts-unified-log.md). Para más información acerca de las alertas de métrica consulte [Comprender cómo funcionan las alertas de métricas en Azure Monitor](../alerts/alerts-metric-overview.md).

## <a name="log-query-measurements"></a>Medidas de consulta de registro
Las alertas de consulta de registro pueden realizar dos medidas diferentes del resultado de una consulta de registro, cada una de las cuales admite distintos escenarios para supervisar máquinas virtuales.

[Unidades métricas](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value) crea una alerta independiente para cada registro de los resultados de la consulta que tiene un valor numérico que supera un umbral definido en la regla de alerta. Son ideales para datos numéricos, como la CPU.

[Número de resultados](../alerts/alerts-unified-log.md#count-of-the-results-table-rows) crea una única alerta cuando una consulta devuelve al menos un número especificado de registros. Son ideales para datos no numéricos o para analizar las tendencias de rendimiento en varios equipos. También puede elegir esta estrategia si desea minimizar el número de alertas o crear una alerta solo cuando varios componentes tengan la misma condición de error.

> [!NOTE]
> Las reglas de alerta de registro centradas en recursos, actualmente en versión preliminar pública, simplificarán las alertas de consulta de registro y reemplazarán la funcionalidad proporcionada actualmente por las consultas de medida de métricas. Puede usar el clúster de AKS como destino de la regla, que lo identificará mejor como el recurso afectado. Cuando las alertas de consulta de registro del centro de recursos estén disponibles con carácter general, se actualizarán las instrucciones de este escenario.

## <a name="create-a-log-query-alert-rule"></a>Creación de una regla de alerta de consulta de registro
La [comparación de las medidas de alertas de consulta de registro](../vm/monitor-virtual-machine-alerts.md#comparison-of-log-query-alert-measures) proporciona un tutorial completo de las reglas de alerta de consulta de registro para cada tipo de medida, incluida una comparación de las consultas de registro que admiten cada una de ellas. Puede usar estos mismos procesos para crear reglas de alerta para clústeres de AKS mediante consultas similares a las de este artículo.

## <a name="resource-utilization"></a>Uso de recursos 

**Uso medio de la CPU como promedio de uso de la CPU de los nodos miembros cada minuto (unidad métrica)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

**Uso medio de la memoria como promedio de uso de la memoria de los nodos miembros cada minuto (unidad métrica)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```


>[!IMPORTANT]
>En las consultas siguientes se usan los valores de marcador de posición \<your-cluster-name> y \<your-controller-name> para representar el clúster y el controlador. Reemplácelos por valores específicos del entorno al configurar las alertas.

**Uso medio de la CPU de todos los contenedores de un controlador como promedio de uso de la CPU de cada instancia de contenedor en un controlador cada minuto (unidad métrica)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

**Uso medio de la memoria de todos los contenedores de un controlador como promedio de uso de la memoria de cada instancia de contenedor en un controlador cada minuto (unidad métrica)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

## <a name="resource-availability"></a>Disponibilidad de recursos 

**Nodos y recuentos que tienen el estado Ready y NotReady (unidad métrica)**

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
En la siguiente consulta se devuelven recuentos de fase de pod basados en todas las fases: *Erróneo*, *Pendiente*, *Desconocido*, *Ejecución* o *Correcto*.  

```kusto
let endDateTime = now(); 
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | summarize PodStatus=any(PodStatus) by TimeGenerated, PodUid, ClusterName
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Para generar una alerta en ciertas fases de pod, tales como *Pendiente*, *Erróneo* o *Desconocido*, modifique la última línea de la consulta. Por ejemplo, para generar alertas sobre *FailedCount*, use: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

En la consulta siguiente se devuelven los discos de los nodos de clúster que superan el 90 % del espacio libre utilizado. Para obtener el id. de clúster, primero ejecute la siguiente consulta y copie el valor de la propiedad `ClusterId`:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```



**Reinicios de contenedores individuales (número de resultados)**<br>
Alertas cuando el recuento de reinicios de contenedores del sistema individuales supera un umbral durante los últimos 10 minutos.

 
```kusto
let _threshold = 10m; 
let _alertThreshold = 2;
let Timenow = (datetime(now) - _threshold); 
let starttime = ago(5m); 
KubePodInventory
| where TimeGenerated >= starttime
| where Namespace in ('default', 'kube-system') // the namespace filter goes here
| where ContainerRestartCount > _alertThreshold
| extend Tags = todynamic(ContainerLastStatus)
| extend startedAt = todynamic(Tags.startedAt)
| where startedAt >= Timenow
| summarize arg_max(TimeGenerated, *) by Name
```

## <a name="next-steps"></a>Pasos siguientes

- En los [ejemplos de consultas de registro](container-insights-log-query.md) encontrará consultas predefinidas y ejemplos para evaluar o personalizar las alertas, la visualización o el análisis de los clústeres.

- Para más información sobre Azure Monitor y cómo supervisar otros aspectos del clúster de Kubernetes, consulte [Visualización del rendimiento del clúster de Kubernetes](container-insights-analyze.md) y [Visualización del estado del clúster de Kubernetes](./container-insights-overview.md).