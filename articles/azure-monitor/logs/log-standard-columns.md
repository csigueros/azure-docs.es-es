---
title: Columnas estándar en registros de Azure Monitor | Microsoft Docs
description: Describe las columnas que son comunes a varios tipos de datos en los registros de Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2021
ms.openlocfilehash: 909c02c53f753579d6788933277bca8f75f53859
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514869"
---
# <a name="standard-columns-in-azure-monitor-logs"></a>Columnas estándar en registros de Azure Monitor
Los datos de los registros de Azure Monitor se [almacenan como un conjunto de registros en un área de trabajo de Log Analytics o una aplicación de Application Insights](../logs/data-platform-logs.md), cada uno con un tipo de datos determinado que tiene un conjunto singular de columnas. Muchos tipos de datos tendrán columnas estándar que son comunes entre varios tipos. En este artículo se describen estas columnas y se proporcionan ejemplos de cómo puede usarlas en las consultas.

Las aplicaciones basadas en el área de trabajo de Application Insights almacenan sus datos en un área de trabajo de Log Analytics y usan las mismas columnas estándar que otras tablas del área de trabajo. Las aplicaciones clásicas almacenan sus datos por separado y tienen diferentes columnas estándar, como se especifica en este artículo.

> [!NOTE]
> Algunas de las columnas estándar no se mostrarán en la vista de esquema ni en IntelliSense en Log Analytics y no aparecerán en los resultados de la consulta, a menos que especifique explícitamente la columna de la salida.
> 

## <a name="tenantid"></a>TenantId
La columna **TenantId** contiene el identificador del área de trabajo de Log Analytics.

## <a name="timegenerated"></a>TimeGenerated
La columna **TimeGenerated** contiene la fecha y la hora en que el origen de datos creó el registro. Consulte [Tiempo de la ingesta de datos de registro en Azure Monitor](../logs/data-ingestion-time.md) para más detalles.

**TimeGenerated** proporciona una columna común para filtrar o resumir por tiempo. Cuando se selecciona un intervalo de tiempo para una vista o panel en Azure Portal, se utiliza **TimeGenerated** para filtrar los resultados. 

> [!NOTE]
> Las tablas que admiten recursos de Application Insights clásicos usan la columna **timestamp** en lugar de la columna **TimeGenerated**.

### <a name="examples"></a>Ejemplos

La consulta siguiente devuelve el número de eventos de error creados para cada día de la semana anterior.

```Kusto
Event
| where EventLevelName == "Error" 
| where TimeGenerated between(startofweek(ago(7days))..endofweek(ago(7days))) 
| summarize count() by bin(TimeGenerated, 1day) 
| sort by TimeGenerated asc 
```
## <a name="_timereceived"></a>\_TimeReceived
La columna **\_TimeReceived** contiene la fecha y hora en que el punto de ingesta de Azure Monitor recibió el registro en la nube de Azure. Esto puede resultar útil para identificar problemas de latencia entre el origen de datos y la nube. Un ejemplo sería un error de red que genere un retraso con los datos que se envían desde un agente. Consulte [Tiempo de la ingesta de datos de registro en Azure Monitor](../logs/data-ingestion-time.md) para más detalles.

> [!NOTE]
> La columna **\_TimeReceived** se calcula cada vez que se usa. Este proceso consume muchos recursos. Restrinja su uso para filtrar un gran número de registros. El uso de esta función de forma recurrente puede aumentar la duración de la ejecución de la consulta.


En la consulta siguiente se proporciona la latencia promedio por hora para los registros de eventos de un agente. Esto incluye el tiempo del agente a la nube y el tiempo total para que el registro esté disponible en las consultas de registro.

```Kusto
Event
| where TimeGenerated > ago(1d) 
| project TimeGenerated, TimeReceived = _TimeReceived, IngestionTime = ingestion_time() 
| extend AgentLatency = toreal(datetime_diff('Millisecond',TimeReceived,TimeGenerated)) / 1000
| extend TotalLatency = toreal(datetime_diff('Millisecond',IngestionTime,TimeGenerated)) / 1000
| summarize avg(AgentLatency), avg(TotalLatency) by bin(TimeGenerated,1hr)
``` 

## <a name="type"></a>Tipo
La columna **Type** contiene el nombre de la tabla de la que se recuperó el registro, que puede considerarse también como el tipo de registro. Esta columna es útil en las consultas que combinan registros de varias tablas, como las que utilizan el operador `search`, para distinguir entre registros de diferentes tipos. En algunas consultas se puede usar **$table** en lugar de **Type**.

> [!NOTE]
> Las tablas que admiten recursos de Application Insights clásicos usan la columna **itemType** en lugar de la columna **Type**.

### <a name="examples"></a>Ejemplos
La siguiente consulta devuelve el número de registros por tipo recopilados durante la última hora.

```Kusto
search * 
| where TimeGenerated > ago(1h)
| summarize count() by Type

```
## <a name="_itemid"></a>\_ItemId
La columna **\_ItemId** contiene un identificador único para el registro.


## <a name="_resourceid"></a>\_ResourceId
La columna **\_ResourceId** contiene un identificador único para el recurso con el que está asociado el registro. Esto le proporciona una columna estándar para definir el ámbito de la consulta a solo los registros de un recurso determinado, o para unir datos relacionados en varias tablas.

En el caso de los recursos de Azure, el valor de **_ResourceId** es la [URL de id. de recurso de Azure](../../azure-resource-manager/templates/template-functions-resource.md). La columna se limita a los recursos de Azure, incluidos los recursos de [Azure Arc](../../azure-arc/overview.md) o a registros personalizados que indicaron el id. de recurso durante la ingesta.

> [!NOTE]
> Algunos tipos de datos ya tienen campos que contienen el identificador de recurso de Azure o al menos partes de él como el identificador de suscripción. Aunque estos campos se guardan por compatibilidad con versiones anteriores, se recomienda utilizar _ResourceId para realizar la correlación cruzada, ya que será más coherente.

### <a name="examples"></a>Ejemplos
La consulta siguiente combina datos de rendimiento y de eventos para cada equipo. Muestra todos los eventos con el identificador _101_ y un uso del procesador superior al 50 %.

```Kusto
Perf 
| where CounterName == "% User Time" and CounterValue  > 50 and _ResourceId != "" 
| join kind=inner (     
    Event 
    | where EventID == 101 
) on _ResourceId
```

La consulta siguiente combina los registros _AzureActivity_ con los de _SecurityEvent_. Muestra todas las operaciones de actividad con los usuarios que iniciaron sesión en estas máquinas.

```Kusto
AzureActivity 
| where  
    OperationName in ("Restart Virtual Machine", "Create or Update Virtual Machine", "Delete Virtual Machine")  
    and ActivityStatus == "Succeeded"  
| join kind= leftouter (    
   SecurityEvent 
   | where EventID == 4624  
   | summarize LoggedOnAccounts = makeset(Account) by _ResourceId 
) on _ResourceId  
```

La siguiente consulta analiza **_ResourceId** y agrega volúmenes de datos facturados por grupo de recursos de Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by resourceGroup | sort by Bytes nulls last 
```

Use estas consultas `union withsource = tt *` con moderación, ya que la ejecución de exámenes entre tipos de datos es costosa.

Siempre resulta más eficaz usar la columna \_SubscriptionId que extraerla mediante el análisis de la columna \_ResourceId.

## <a name="_subscriptionid"></a>\_SubscriptionId
La columna **\_SubscriptionId** contiene el id. de suscripción del recurso con el que está asociado el registro. Esto le proporciona una columna estándar para definir el ámbito de la consulta a solo los registros de una suscripción determinada, o para comparar suscripciones distintas.

En el caso de los recursos de Azure, el valor de **__SubscriptionId** es la parte de la suscripción de la [URL del id. de recurso de Azure](../../azure-resource-manager/templates/template-functions-resource.md). La columna se limita a los recursos de Azure, incluidos los recursos de [Azure Arc](../../azure-arc/overview.md) o a registros personalizados que indicaron el id. de recurso durante la ingesta.

> [!NOTE]
> Algunos tipos de datos ya tienen campos que contienen el id. de suscripción de Azure. Aunque estos campos se conservan para la compatibilidad con versiones anteriores, se recomienda usar la columna \_SubscriptionId para realizar la correlación cruzada, ya que será más coherente.
### <a name="examples"></a>Ejemplos
La siguiente consulta examina los datos de rendimiento de los equipos de una suscripción específica. 

```Kusto
Perf 
| where TimeGenerated > ago(24h) and CounterName == "memoryAllocatableBytes"
| where _SubscriptionId == "ebb79bc0-aa86-44a7-8111-cabbe0c43993"
| summarize avgMemoryAllocatableBytes = avg(CounterValue) by Computer
```

La siguiente consulta analiza **_ResourceId** y agrega volúmenes de datos facturados por suscripción de Azure.

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _SubscriptionId | sort by Bytes nulls last 
```

Use estas consultas `union withsource = tt *` con moderación, ya que la ejecución de exámenes entre tipos de datos es costosa.


## <a name="_isbillable"></a>\_IsBillable
La columna **\_IsBillable** especifica si los datos ingeridos son facturables. Los datos con **\_IsBillable** igual a `false` se recopilan de forma gratuita y no se facturan en su cuenta de Azure.

### <a name="examples"></a>Ejemplos
Para obtener una lista de equipos que envían los tipos de datos de facturación, use la siguiente consulta:

> [!NOTE]
> Use las consultas con `union withsource = tt *` con moderación, ya que la ejecución de exámenes entre tipos de datos es costosa. 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Dichas consultas pueden ampliarse para devolver el número de equipos por hora que están enviando tipos de datos facturados:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="_billedsize"></a>\_BilledSize
La columna **\_BilledSize** especifica el tamaño en bytes de los datos que se facturarán a su cuenta de Azure si **\_IsBillable** es true.


### <a name="examples"></a>Ejemplos
Para ver el tamaño de los eventos facturables que ingirió cada equipo, use la columna `_BilledSize`, que proporciona el tamaño en bytes:

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last 
```

Para ver el tamaño de los eventos facturables ingeridos por suscripción, use la siguiente consulta:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId | sort by Bytes nulls last 
```

Para ver el tamaño de los eventos facturables ingeridos por grupo de recursos, use la siguiente consulta:

```Kusto
union withsource=table * 
| where _IsBillable == true 
| parse _ResourceId with "/subscriptions/" SubscriptionId "/resourcegroups/" ResourceGroupName "/" *
| summarize Bytes=sum(_BilledSize) by  _SubscriptionId, ResourceGroupName | sort by Bytes nulls last 

```


Para ver el recuento de eventos ingeridos por equipo, use la consulta siguiente:

```Kusto
union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last
```

Para ver el recuento de eventos facturables ingeridos por equipo, use la consulta siguiente: 

```Kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last
```

Para ver el recuento de tipos de datos facturables desde un equipo específico, use la siguiente consulta:

```Kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last 
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre cómo se almacenan los [datos del registro de Azure Monitor](./log-query-overview.md).
- Reciba una lección sobre [escritura de consultas de registro](./get-started-queries.md).
- Reciba una lección sobre [la unión de tablas en consultas de registro](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
