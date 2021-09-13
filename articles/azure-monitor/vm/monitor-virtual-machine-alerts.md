---
title: 'Supervisión de máquinas virtuales con Azure Monitor: alertas'
description: Aprenda a crear alertas desde máquinas virtuales y sus cargas de trabajo de invitado mediante Azure Monitor.
ms.service: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/21/2021
ms.openlocfilehash: 86160cd24708088a2cc5917a4850ca029023c0ce
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322787"
---
# <a name="monitor-virtual-machines-with-azure-monitor-alerts"></a>Supervisión de máquinas virtuales con Azure Monitor: alertas

Este artículo forma parte del escenario [Supervisión de máquinas virtuales y sus cargas de trabajo en Azure Monitor](monitor-virtual-machine.md). En él se proporcionan instrucciones sobre cómo crear reglas de alertas para las máquinas virtuales y sus sistemas operativos invitados. Las [alertas de Azure Monitor](../alerts/alerts-overview.md) notifican al usuario proactivamente sobre datos y patrones interesantes en los datos de supervisión. No hay reglas de alerta preconfiguradas para las máquinas virtuales, pero puede crear las suyas propias en función de los datos recopilados por VM Insights. 

> [!NOTE]
> Las alertas creadas en este artículo no incluyen las creadas mediante el [estado de invitado de Azure Monitor para máquinas virtuales](vminsights-health-overview.md), que es una característica actualmente en versión preliminar pública. A medida que esta característica se acerque a la disponibilidad general, se consolidarán las instrucciones para las alertas.

> [!IMPORTANT]
> La mayoría de las reglas de alertas tienen un coste que depende del tipo de regla, del número de dimensiones que incluyen y de la frecuencia con que se ejecutan. Antes de crear reglas de alertas, consulte **Reglas de alertas** en [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="choose-the-alert-type"></a>Selección del tipo de alerta
Los tipos más comunes de reglas de alertas de Azure Monitor son las [alertas de métricas](../alerts/alerts-metric.md) y las [alertas de consultas de registro](../alerts/alerts-log-query.md). El tipo de regla de alertas que cree para un escenario determinado depende de dónde se encuentran los datos sobre los que genera alertas. Es posible que haya casos en los que los datos para un escenario de alerta concreto estén disponibles tanto en las métricas como en los registros, y tendrá que determinar qué tipo de regla utilizar. También puede tener flexibilidad en la forma de recopilar ciertos datos y dejar que su decisión sobre el tipo de regla de alerta dirija su decisión sobre el método de recopilación de datos.

Por lo general, la mejor estrategia es utilizar alertas de métricas en lugar de alertas de registro cuando sea posible, ya que son más sensibles y tienen estado. Para utilizar las alertas de las métricas, los datos sobre los que se alerta deben estar disponibles en las métricas. VM Insights envía actualmente todos sus datos a los registros, por lo que debe instalar el agente de Azure Monitor para usar alertas de métricas con datos del sistema operativo invitado. Utilice alertas de consulta de registro con los datos de métricas cuando no estén disponibles en las métricas o si necesita una lógica más allá de la lógica relativamente sencilla de una regla de alerta de métricas.

### <a name="metric-alert-rules"></a>Reglas de alertas de métricas
Las [reglas de alertas de métricas](../alerts/alerts-metric.md) son útiles para generar alertas cuando una métrica determinada supera un umbral. Por ejemplo, cuando se consumen muchos recursos de CPU en una máquina. El destino de una regla de alertas de métricas puede ser una máquina, un grupo de recursos o una suscripción concretos. En este caso, puede crear una única regla que se aplique a un grupo de máquinas.

Las reglas de métricas para máquinas virtuales pueden usar los datos siguientes:

- Métricas de host para máquinas virtuales de Azure, que se recopilan automáticamente. 
- Métricas recopiladas por el agente de Azure Monitor del sistema operativo invitado. 

> [!NOTE]
> Cuando VM Insights admite el agente de Azure Monitor, que actualmente se encuentra en versión preliminar pública, envía datos de rendimiento del sistema operativo invitado a la sección de métricas para que pueda usar alertas de métricas.

### <a name="log-alerts"></a>Alertas de registro
Las [alertas de registro](../alerts/alerts-metric.md) pueden realizar dos medidas diferentes del resultado de una consulta de registro, cada una de las cuales admite distintos escenarios para supervisar máquinas virtuales.

- [Unidades métricas](../alerts/alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value): crea una alerta distinta para cada registro en los resultados de consulta que tiene un valor numérico que supera un umbral definido en la regla de alertas. Las unidades métricas funcionan muy bien con datos no numéricos, como los eventos de Windows y Syslog recopilados por el agente de Log Analytics o para analizar las tendencias de rendimiento en varios equipos.
- [Número de resultados](../alerts/alerts-unified-log.md#count-of-the-results-table-rows): crea una única alerta cuando una consulta devuelve al menos un número especificado de registros. Las mediciones del número de resultados funcionan muy bien con datos no numéricos, como los eventos de Windows y Syslog recopilados por el [agente de Log Analytics](../agents/log-analytics-agent.md) o para analizar las tendencias de rendimiento en varios equipos. También puede elegir esta estrategia si quiere reducir el número de alertas o crear posiblemente una alerta solo cuando varias máquinas tengan la misma condición de error.

### <a name="target-resource-and-impacted-resource"></a>Recurso de destino y recurso afectado

> [!NOTE]
> Las reglas de alertas de registro centradas en recursos, actualmente en versión preliminar pública, simplifican las alertas de consulta de registro en las máquinas virtuales y reemplazan la funcionalidad proporcionada actualmente por las consultas de unidades métricas. Puede usar la máquina como destino de la regla, que la identifica mejor como el recurso afectado. También puede aplicar una única regla de alertas a todas las máquinas de un grupo de recursos o descripción determinados. Cuando las alertas de consulta de registro del centro de recursos estén disponibles con carácter general, se actualizarán las instrucciones de este escenario.
> 
Cada alerta de Azure Monitor tiene una propiedad **Recurso afectado**, que se define mediante el destino de la regla. En el caso de las reglas de alertas de métricas, el recurso afectado es el equipo, lo que permite identificarlo fácilmente en la vista de alertas estándar. Las alertas de consulta de registro están asociadas al recurso del área de trabajo en lugar de a la máquina, incluso cuando se usa una alerta de unidades métricas que crea una alerta para cada equipo. Debe ver los detalles de la alerta para comprobar el equipo afectado.

El nombre del equipo se almacena en la propiedad **Recurso afectado**, que puede ver en los detalles de la alerta. También se muestra como una dimensión en los correos electrónicos que se envían desde la alerta.

:::image type="content" source="media/monitor-virtual-machines/alert-metric-measurement.png" alt-text="Captura de pantalla que muestra una alerta con el recurso afectado." lightbox="media/monitor-virtual-machines/alert-metric-measurement.png":::

Es posible que quiera tener una vista que muestre las alertas con el equipo afectado. Para proporcionar esta vista, puede usar un libro personalizado que emplee una instancia personalizada de [Resource Graph](../../governance/resource-graph/overview.md). Use la consulta siguiente para mostrar alertas y utilice el origen de datos **Azure Resource Graph** en el libro.

```kusto
alertsmanagementresources
| extend dimension = properties.context.context.condition.allOf
| mv-expand dimension
| extend dimension = dimension.dimensions
| mv-expand dimension
| extend Computer = dimension.value
| extend AlertStatus = properties.essentials.alertState
| summarize count() by Alert=name, tostring(AlertStatus), tostring(Computer)
| project Alert, AlertStatus, Computer
```
## <a name="common-alert-rules"></a>Reglas de alerta comunes
En la sección siguiente se enumeran las reglas de alertas comunes para las máquinas virtuales en Azure Monitor. Para cada una, se proporcionan detalles de las alertas de métricas y las alertas de unidades métricas de registro. Para saber qué tipo de alerta usar, consulte [Selección del tipo de alerta](#choose-the-alert-type).

Si no está familiarizado con el proceso de creación de reglas de alertas en Azure Monitor, consulte los siguientes artículos como guía:

- [Creación, visualización y administración de alertas de métricas mediante Azure Monitor](../alerts/alerts-metric.md)
- [Creación, visualización y administración de alertas de registro mediante Azure Monitor](../alerts/alerts-log.md)

### <a name="machine-unavailable"></a>Máquina no disponible
El requisito más básico es enviar una alerta cuando una máquina no está disponible. Podría estar detenida o que el sistema operativo invitado o el agente no respondan. Hay varias maneras de configurar estas alertas, pero la más común es usar el latido enviado desde el agente de Log Analytics. 

#### <a name="log-query-alert-rules"></a>Reglas de alertas de consulta de registro
Las alertas de consulta de registro usan la [tabla Latido](/azure/azure-monitor/reference/tables/heartbeat), que debe tener un registro de latido cada minuto de cada máquina. 

**Alertas independientes**

Use una regla de unidades métricas con la consulta siguiente.

```kusto
Heartbeat
| summarize TimeGenerated=max(TimeGenerated) by Computer
| extend Duration = datetime_diff('minute',now(),TimeGenerated)
| summarize AggregatedValue = min(Duration) by Computer, bin(TimeGenerated,1)
```

**Alerta única**

Use una serie de alertas de resultados con la consulta siguiente.

```kusto
Heartbeat
| summarize LastHeartbeat=max(TimeGenerated) by Computer 
| where LastHeartbeat < ago(5m)
```

#### <a name="metric-alert-rules"></a>Reglas de alertas de métricas
Se incluye una métrica denominada *Latido* en cada área de trabajo de Log Analytics. Cada máquina virtual conectada a esa área de trabajo envía un valor de métrica de latido cada minuto. Dado que el equipo es una dimensión de la métrica, puede activar una alerta cuando un equipo no pueda enviar un latido. Establezca **Tipo de agregación** en **Recuento** y el valor de **Umbral** para que coincida con la **granularidad de la evaluación**.

### <a name="cpu-alerts"></a>Alertas de CPU
#### <a name="metric-alert-rules"></a>Reglas de alertas de métricas

| Destino | Métrica |
|:---|:---|
| Host | Porcentaje de CPU |
| Invitado de Windows | \Processor Information(_Total)\% de tiempo de procesador |
| Invitado de Linux | cpu/usage_active |

#### <a name="log-alert-rules"></a>Reglas de alertas de registro

**Uso de CPU** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**Utilización de la CPU para todos los recursos de proceso en una suscripción**

```kusto
 InsightsMetrics
 | where Origin == "vm.azm.ms"
 | where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/") 
 | where Namespace == "Processor" and Name == "UtilizationPercentage"<br>\| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

**Utilización de la CPU para todos los recursos de proceso en un grupo de recursos** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/" or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/"
| where Namespace == "Processor" and Name == "UtilizationPercentage"<br>\| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId 
```

### <a name="memory-alerts"></a>Alertas de memoria

#### <a name="metric-alert-rules"></a>Reglas de alertas de métricas

| Destino | Métrica |
|:---|:---|
| Invitado de Windows | \Memory\% de bytes confirmados en uso<br>\Memoria\Bytes disponibles |
| Invitado de Linux | mem/available<br>mem/available_percent |

#### <a name="log-alert-rules"></a>Reglas de alertas de registro

**Memoria disponible en MB** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

**Memoria disponible en porcentaje** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])<br>\| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer  
``` 

### <a name="disk-alerts"></a>Alertas de disco

#### <a name="metric-alert-rules"></a>Reglas de alertas de métricas

| Destino | Métrica |
|:---|:---|
| Invitado de Windows | \Logical Disk\(_Total)\% de espacio libre<br>\Logical Disk\(_Total)\Free Megabytes |
| Invitado de Linux | disk/free<br>disk/free_percent |

#### <a name="log-query-alert-rules"></a>Reglas de alertas de consulta de registro

**Disco lógico usado: todos los discos de cada equipo** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**Disco lógico usado: discos individuales** 

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk 
```

**IOPS de disco lógico**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk 
```
**Velocidad de datos de disco lógico**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk 
```

## <a name="network-alerts"></a>Alertas de red

#### <a name="metric-alert-rules"></a>Reglas de alertas de métricas

| Destino | Métrica |
|:---|:---|
| Invitado de Windows | \Rendimiento de interfaz de red\Bytes enviados/s<br>\Logical Disk\(_Total)\Free Megabytes |
| Invitado de Linux | disk/free<br>disk/free_percent |

### <a name="log-query-alert-rules"></a>Reglas de alertas de consulta de registro

**Bytes de interfaces de red recibidos: todas las interfaces**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId  
```

**Bytes de interfaces de red recibidos: interfaces individuales**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

**Bytes de interfaces de red enviados: todas las interfaces**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

**Bytes de interfaces de red enviados: interfaces individuales**

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface 
```

## <a name="comparison-of-log-query-alert-measures"></a>Comparación de medidas de alertas de consulta de registro
Para comparar el comportamiento de las dos medidas de alertas de registro, vamos a examinar cada una para crear una alerta cuando la CPU de una máquina virtual supere el 80 por ciento. Los datos que necesita están en la [tabla InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics). La consulta siguiente devuelve los registros que deben evaluarse para la alerta. Cada tipo de regla de alertas usa una variante de esta consulta.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
```

### <a name="metric-measurement"></a>Unidades métricas
Las **unidades métricas** crean una alerta independiente para cada registro de una consulta que tenga un valor que supere un umbral definido en la regla de alertas. Estas reglas de alertas funcionan muy bien con los datos de rendimiento de las máquinas virtuales, ya que se crean alertas individuales para cada equipo. La consulta de registro de esta medida debe devolver un valor para cada máquina. El umbral de la regla de alertas determina si el valor debe activar una alerta.

> [!NOTE]
> Las reglas de alertas de registro centradas en recursos, actualmente en versión preliminar pública, simplifican las alertas de consulta de registro en las máquinas virtuales y reemplazan la funcionalidad proporcionada actualmente por las consultas de unidades métricas. Puede usar la máquina como destino de la regla, que la identifica mejor como el recurso afectado. También puede aplicar una única regla de alertas a todas las máquinas de un grupo de recursos o descripción determinados. Cuando las alertas de consulta de registro del centro de recursos estén disponibles con carácter general, se actualizarán las instrucciones de este escenario.

#### <a name="query"></a>Consultar
La' consulta de las reglas que usan la medición de métricas debe incluir un registro para cada máquina con una propiedad numérica llamada **AggregatedValue**. Este valor se compara con el umbral de la regla de alertas. La consulta no necesita comparar este valor con un umbral porque el umbral se define en la regla de alertas.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer
```

#### <a name="alert-rule"></a>Regla de alertas
En el menú de Azure Monitor, seleccione **Registros** para abrir Log Analytics. Asegúrese de que está seleccionado el área de trabajo correcta para el ámbito. Si no es así, haga clic en **Seleccionar ámbito** en la esquina superior izquierda y seleccione el área de trabajo correcta. Pegue la consulta que tenga la lógica que quiere y seleccione **Ejecutar** para comprobar que se devuelven los resultados correctos.

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-query-results.png" alt-text="Captura de pantalla que muestra los resultados de la consulta de alerta de medición de métricas." lightbox="media/monitor-virtual-machines/log-alert-metric-query-results.png":::

Seleccione **Nueva regla de alertas** para crear una regla con la consulta actual. La regla usa el área de trabajo como **Recurso**.

Seleccione **Condición** para ver la configuración. La consulta ya se ha rellenado con una vista gráfica del valor devuelto por la consulta para cada equipo. Seleccione el equipo en la lista desplegable **Dinamizado en**.

Desplácese hasta **Lógica de alerta** y seleccione **Unidades métricas** para la propiedad **Basada en**. Dado que quiere activar una alerta cuando el uso supere el 80 por ciento, establezca **Valor agregado** en **Mayor que** y **Valor de umbral** en **80**.

Desplácese hasta **Lógica de alerta** y seleccione **Unidades métricas** para la propiedad **Basada en**. Proporcione un valor de **Umbral** para compararlo con el valor devuelto por la consulta. En este ejemplo, use **80**. En **Desencadenar alerta según**, especifique el número de veces que se debe superar el umbral antes de que se cree una alerta. Por ejemplo, puede que no le interese el escenario en el que el procesador supera un umbral una vez y luego vuelve a la normalidad, pero sí si sigue superando el umbral en varias mediciones consecutivas. En este ejemplo, establezca **Infracciones consecutivas** en **3**.

Desplácese hasta **Se avaluó basándose en**. **Período**, especifica el intervalo de tiempo de la consulta. Especifique un valor de **15** minutos, lo que significa que la consulta solo usa los datos recopilados en los últimos 15 minutos. **Frecuencia**, especifica con qué frecuencia se ejecuta la consulta. Un valor inferior hace que la regla de alertas tenga más capacidad de respuesta, pero también un costo mayor. Especifique **15** para ejecutar la consulta cada 15 minutos.

:::image type="content" source="media/monitor-virtual-machines/log-alert-metric-rule.png" alt-text="Captura de pantalla que muestra la consulta de regla de alertas de unidades métricas." lightbox="media/monitor-virtual-machines/log-alert-metric-rule.png":::

### <a name="number-of-results-rule"></a>Regla de número de resultados
La regla **Número de resultados** crea una única alerta cuando una consulta devuelve al menos un número especificado de registros. La consulta de registro de este tipo de regla de alertas normalmente identifica la condición de alerta, mientras que el umbral de la regla de alertas determina si se devuelve un número suficiente de registros.

#### <a name="query"></a>Consultar
En este ejemplo, el umbral de uso de CPU se incluye en la consulta. El número de registros devueltos por la consulta es el número de máquinas que superan ese umbral. El umbral de la regla de alertas es el número mínimo de máquinas necesarias para que se active la alerta. Si quiere que se genere una alerta cuando se produzca un error en una sola máquina, el umbral de la regla de alerta es cero.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AverageUtilization = avg(Val) by Computer
| where AverageUtilization > 80
```

#### <a name="alert-rule"></a>Regla de alertas
En el menú de Azure Monitor, seleccione **Registros** para abrir Log Analytics. Asegúrese de que está seleccionado el área de trabajo correcta para el ámbito. Si no es así, haga clic en **Seleccionar ámbito** en la esquina superior izquierda y seleccione el área de trabajo correcta. Pegue la consulta que tenga la lógica que quiere y seleccione **Ejecutar** para comprobar que se devuelven los resultados correctos. Es probable que no tenga una máquina actualmente por encima del umbral, así que cambie temporalmente a un umbral inferior para comprobar los resultados. A continuación, establezca el umbral adecuado antes de crear la regla de alertas.

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-query-results.png" alt-text="Captura de pantalla que muestra los resultados de consulta de la regla de número de resultados." lightbox="media/monitor-virtual-machines/log-alert-number-query-results.png":::

Seleccione **Nueva regla de alertas** para crear una regla con la consulta actual. La regla usa el área de trabajo como **Recurso**.

Seleccione **Condición** para ver la configuración. La consulta ya se ha rellenado con una vista gráfica del número de registros que se han devuelto por esa consulta durante los últimos minutos. 

Desplácese hasta **Lógica de alerta** y seleccione **Número de resultados** como valor de la propiedad **Basada en**. En este ejemplo, quiere una alerta si se devuelve algún registro, lo que significa que al menos una máquina virtual tiene un procesador por encima del 80 por ciento. Seleccione **Mayor que** en **Operador** y **0** en **Valor de umbral**.

Desplácese hasta **Se avaluó basándose en**. **Período**, especifica el intervalo de tiempo de la consulta. Especifique un valor de **15** minutos, lo que significa que la consulta solo usa los datos recopilados en los últimos 15 minutos. **Frecuencia**, especifica con qué frecuencia se ejecuta la consulta. Un valor inferior hace que la regla de alertas tenga más capacidad de respuesta, pero también un costo mayor. Especifique **15** para ejecutar la consulta cada 15 minutos.

:::image type="content" source="media/monitor-virtual-machines/log-alert-number-rule.png" alt-text="Captura de pantalla que muestra la consulta de la regla de alertas de número de resultados." lightbox="media/monitor-virtual-machines/log-alert-number-rule.png":::

## <a name="next-steps"></a>Pasos siguientes

* [Supervisión de cargas de trabajo que se ejecutan en máquinas virtuales.](monitor-virtual-machine-workloads.md)
* [Análisis de los datos de supervisión recopilados para las máquinas virtuales.](monitor-virtual-machine-analyze.md)
