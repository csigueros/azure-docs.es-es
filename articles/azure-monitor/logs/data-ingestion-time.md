---
title: Tiempo de la ingesta de datos de registro en Azure Monitor | Microsoft Docs
description: Explica los distintos factores que afectan a la latencia en la recopilación de datos de registro en Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 85aff2a31d2ac7221f78439a142dbe7517ab042a
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398903"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Tiempo de la ingesta de datos de registro en Azure Monitor
Azure Monitor es un servicio de datos a gran escala que atiende a miles de clientes que envían terabytes de datos cada mes a un ritmo creciente. Con frecuencia se plantean preguntas sobre el tiempo necesario para que los datos de registro estén disponibles una vez que se han recopilado. En este artículo se explican los distintos factores que afectan a esta latencia.

## <a name="typical-latency"></a>Latencia típica
La latencia hace referencia al momento en el que los datos se crean en el sistema supervisado y el momento en el que están disponibles para su análisis en Azure Monitor. La latencia típica para la ingesta de datos de registro es de **entre 20 y 3 minutos**. Sin embargo, la latencia específica para cualquier dato determinado varía en función de una serie de factores que se explican más adelante.


## <a name="factors-affecting-latency"></a>Factores que afectan a la latencia
El tiempo total de ingesta para un determinado conjunto de datos puede dividirse en las siguientes áreas de alto nivel. 

- **Tiempo del agente:** es el tiempo para detectar un evento, recopilarlo y enviarlo al punto de ingesta de registros de Azure Monitor como una entrada de registro. En la mayoría de los casos, este proceso se controla mediante un agente. La red puede aumentar también la latencia.
- **Tiempo de canalización:** el tiempo que tarda la canalización de ingesta en procesar la entrada del registro. Esto incluye el análisis de las propiedades del evento y la incorporación potencial de información calculada.
- **Tiempo de indexación:** el tiempo dedicado a la ingesta de una entrada de registro en el almacén de macrodatos de Azure Monitor.

A continuación se describen los detalles sobre las diferentes latencias que se presentan en este proceso.

### <a name="agent-collection-latency"></a>Latencia de la recopilación de agente

**El tiempo varía**

Los agentes y las soluciones de administración utilizan diferentes estrategias para recopilar datos de una máquina virtual, lo que puede afectar a la latencia. A continuación se incluyen algunos ejemplos:

| Tipo de datos  | Frecuencia de recopilación  | Notas |
|:--------------|:----------------------|:------|
| Eventos de Windows, eventos de syslog y métricas de rendimiento | Se recopilan inmediatamente.| | 
| Contadores de rendimiento de Linux | Se sondean a intervalos de 30 segundos.| |
| Registros de IIS y registros personalizados | Se recopilan cuando cambia su marca de tiempo. | Para los registros de IIS, esto viene determinado por la [programación de sustitución configurada en IIS](../agents/data-sources-iis-logs.md). |
| Solución de Active Directory Replication | Se evalúa cada cinco días. | El agente recopila estos registros solo cuando se completa la evaluación.|
| Solución de Active Directory Assessment | Se evalúa la infraestructura de Active Directory semanalmente. | El agente recopila estos registros solo cuando se completa la evaluación.|

### <a name="agent-upload-frequency"></a>Frecuencia de carga del agente

**Menos de 1 minuto**

Para asegurarse de que el agente de Log Analytics es ligero, el agente almacena en búfer los registros y los carga periódicamente en Azure Monitor. La frecuencia de carga varía entre 30 segundos y 2 minutos, según el tipo de datos. La mayoría de los datos se carga en menos de 1 minuto. 

### <a name="network"></a>Red

**Varía**

Las condiciones de red pueden afectar negativamente a la latencia de estos datos para acceder al punto de ingesta de Azure Monitor.

### <a name="azure-metrics-resource-logs-activity-log"></a>Métricas de Azure, registros de recursos, registro de actividad

**De 30 segundos hasta 15 minutos**

Los datos de Azure tardan más tiempo en estar disponibles en el punto de ingesta de Log Analytics para su procesamiento:

- Las **métricas de la plataforma Azure** están disponibles en menos de un minuto en la base de datos de métricas, pero tardan tres minutos más en exportarse al punto de ingesta de registros de Azure Monitor.
- Normalmente, los **registros de recursos** tardan entre 30 y 90 segundos, según el servicio de Azure. Algunos servicios de Azure (específicamente, Azure SQL Database y Azure Virtual Network) notifican actualmente sus registros a intervalos de 5 minutos. Se está trabajando para mejorarlos aún más. Vea [esta consulta](#checking-ingestion-time) para examinar la latencia en su entorno
- Los datos del **registro de actividad** se ingieren en 30 segundos cuando se usa la configuración de diagnóstico de nivel de suscripción recomendada para enviarlos a los registros de Azure Monitor. Sin embargo, pueden tardar entre 10 y 15 minutos si en su lugar se usa la integración heredada.  

### <a name="management-solutions-collection"></a>Recopilación de las soluciones de administración

**Varía**

Algunas soluciones no recopilan los datos de un agente y pueden usar un método de recopilación que introduce latencia adicional. Algunas soluciones recopilan los datos a intervalos regulares sin intentar la recopilación casi en tiempo real. A continuación se incluyen algunos ejemplos específicos:

- La solución Microsoft 365 sondea los registros de actividad mediante la API de Actividad de administración, que actualmente no proporciona garantías de latencia casi en tiempo real.
- Los datos de las soluciones de Windows Analytics (Update Compliance por ejemplo) se recopilan con una frecuencia diaria por la solución.

Consulte la [documentación de cada solución](/azure/azure-monitor/insights/solutions) para determinar su frecuencia de recopilación.

### <a name="pipeline-process-time"></a>Tiempo del proceso de canalización

**De 30 a 60 segundos**

Una vez disponibles en el punto de ingesta, tardarán entre 30 y 60 minutos más en estar disponibles para su consulta.

Una vez que las entradas de registro se han ingerido en la canalización de Azure Monitor (como se indica en la propiedad [_TimeReceived](./log-standard-columns.md#_timereceived)), se escriben en un almacenamiento temporal para garantizar el aislamiento de inquilinos y para asegurarse de que no se pierden datos. Normalmente, este proceso agrega de 5 a 15 segundos. Algunas soluciones de administración implementan algoritmos más pesados para agregar datos y obtener perspectivas a medida que los datos van entrando. Por ejemplo, Network Performance Monitor agrega los datos de entrada en intervalos de 3 minutos, agregando así una latencia de 3 minutos. Otro proceso que agrega latencia es el proceso que controla los registros personalizados. En algunos casos, este proceso puede agregar algunos minutos de latencia a los registros recopilados de los archivos por el agente.

### <a name="new-custom-data-types-provisioning"></a>Aprovisionamiento de nuevos tipos de datos personalizados

Cuando se crea un nuevo tipo de datos personalizados desde un [registro personalizado](../agents/data-sources-custom-logs.md) o la [API del recopilador de datos](../logs/data-collector-api.md), el sistema crea un contenedor de almacenamiento dedicado. Se trata de una sobrecarga de un solo uso que se produce solo con la primera aparición de este tipo de datos.

### <a name="surge-protection"></a>Protección para los picos de datos

**Normalmente menos de 1 minuto, pero puede ser más** 

La máxima prioridad de Azure Monitor es asegurarse de que no se pierda ningún dato de cliente, por ello el sistema tiene protección integrada para los picos en los que los datos aumentan. Esto incluye los búferes para asegurarse de que incluso con una carga enorme, el sistema seguirá funcionando. Bajo una carga normal, estos controles agregan menos de un minuto, pero en condiciones extremas y con errores pueden agregar un tiempo significativo mientras se aseguran de que los datos no se pierdan.

### <a name="indexing-time"></a>Tiempo de indexación

**5 minutos como máximo**

Hay un equilibrio integrado para cada plataforma de macrodatos entre, por un lado, proporcionar análisis y funcionalidades de búsqueda avanzada y por otro proporcionar acceso inmediato a los datos. Azure Monitor le permite ejecutar consultas muy eficaces en miles de millones de registros y obtener resultados en cuestión de segundos. Esto es posible porque la infraestructura transforma los datos de forma drástica durante su ingesta y los almacena en estructuras compactas únicas. El sistema almacena en búfer los datos hasta que haya disponibles los suficientes para crear estas estructuras. Esto se tiene que completar antes de que aparezca la entrada de registro en los resultados de búsqueda.

Actualmente, este proceso tarda aproximadamente 5 minutos cuando hay bajo volumen de datos, y menos tiempo con una frecuencia de datos más alta. Esto parece contradictorio, pero este proceso permite la optimización de latencia para cargas de trabajo de producción de gran volumen.

## <a name="checking-ingestion-time"></a>Comprobación del tiempo de ingesta de datos
El tiempo de ingesta puede variar para diferentes recursos en diferentes circunstancias. Puede usar consultas de registro para identificar el comportamiento específico de su entorno. En la tabla siguiente se especifica cómo se pueden determinar los distintos tiempos de un registro a medida que se crea y se envía a Azure Monitor.

| Paso | Propiedad o función | Comentarios |
|:---|:---|:---|
| Registro creado en el origen de datos | [TimeGenerated](./log-standard-columns.md#timegenerated) <br>Si el origen de datos no establece este valor, se establecerá en el mismo tiempo que _TimeReceived. |
| Registro recibido por el punto de conexión de ingesta de Azure Monitor | [_TimeReceived](./log-standard-columns.md#_timereceived) | Este campo no está optimizado para el procesamiento masivo y no debe usarse para filtrar grandes conjuntos de datos. |
| Registro almacenado en el área de trabajo y disponible para las consultas. | [ingestion_time()](/azure/kusto/query/ingestiontimefunction) | Se recomienda usar ingestion_time () si hay necesidad de filtrar solo los registros ingeridos en un período de tiempo determinado. En tal caso, se recomienda agregar también el filtro TimeGenerated con un rango mayor. |

### <a name="ingestion-latency-delays"></a>Retrasos de latencia de la ingesta
Puede medir la latencia de un registro específico si compara el resultado de la función [ingestion_time()](/azure/kusto/query/ingestiontimefunction) con la propiedad _TimeGenerated_. Estos datos se pueden usar con diversas agregaciones para conocer el funcionamiento de la latencia de ingesta. Examine algunos percentiles del tiempo de ingesta para obtener información de grandes cantidades de datos. 

Por ejemplo, la consulta siguiente muestra los equipos que tenían el mayor tiempo de ingesta durante las ocho horas anteriores: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Las comprobaciones de percentiles anteriores son adecuadas para buscar tendencias generales en la latencia. Para identificar un pico a corto plazo en la latencia, puede resultar más eficaz usar el máximo (`max()`).

Si quiere profundizar en el tiempo de ingesta de un equipo específico durante un período de tiempo, use la siguiente consulta, que también visualiza los datos del día anterior en un gráfico: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Use la siguiente consulta para mostrar la hora de ingesta del equipo por el país o región en el que está ubicado basándose en su dirección IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Los diferentes tipos de datos que se originan desde el agente pueden tener diferentes horas de latencia de ingestión, por lo que las consultas anteriores se pueden usar con otros tipos. Use la siguiente consulta para examinar la hora de la ingesta de diversos servicios de Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Recursos que dejan de responder 
En algunos casos, un recurso pudo detener el envío de datos. Para saber si un recurso está enviando datos o no, examine su registro más reciente que puede identificarse por el campo _TimeGenerated_ estándar.  

Use la tabla _Latido_ para comprobar la disponibilidad de una máquina virtual, puesto que un agente envía un latido una vez por minuto. Use la siguiente consulta para mostrar una lista de los equipos activos que no han comunicado latidos recientemente: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Pasos siguientes
* Lea el [Acuerdo de Nivel de Servicio (SLA)](https://azure.microsoft.com/support/legal/sla/monitor/v1_3/) para Azure Monitor.
