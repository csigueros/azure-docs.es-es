---
title: 'Azure Communication Services: Log Analytics (versión preliminar)'
titleSuffix: An Azure Communication Services concept document
description: Acerca del uso de Log Analytics con los registros de resumen de llamadas y diagnóstico de llamadas
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 05ae40fc974228bf93641be3786294aab59c243a
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138582"
---
# <a name="log-analytics-for-communications-services-preview"></a>Log Analytics para Communications Services (versión preliminar)

## <a name="overview-and-access"></a>Introducción y acceso

Para poder aprovechar las ventajas de usar [Log Analytics](/azure/azure-monitor/logs/log-analytics-overview) con los registros de Communications Services, primero debe seguir los pasos descritos en [Habilitación del registro en Configuración de diagnóstico](enable-logging.md). Cuando haya habilitado los registros y un [área de trabajo de Log Analytics](/azure/azure-monitor/logs/design-logs-deployment), tendrá acceso a muchos [paquetes de consulta predeterminados](/azure/azure-monitor/logs/query-packs#default-query-pack) útiles que le ayudarán a visualizar y comprender rápidamente los datos disponibles en los registros, que se describen a continuación. Mediante Log Analytics, también obtiene acceso a más información de Communication Services a través de Libros de Azure Monitor (consulte [Información de Communications Services](insights.md)), puede crear sus propias consultas y libros y puede [acceder a la API REST](https://dev.loganalytics.io/) en cualquier consulta.

### <a name="access"></a>Access
Para acceder a las consultas, comience en la página del recurso Communications Services y, luego, haga clic en "Registros" en el panel de navegación izquierdo dentro de la sección Supervisión:

:::image type="content" source="media\log-analytics\access-log-analytics.png" alt-text="Navegación por Log Analytics":::

Desde allí, se muestra una pantalla modal que contiene todos los [paquetes de consulta predeterminados](/azure/azure-monitor/logs/query-packs#default-query-pack) disponibles para Communication Services, con la lista de los paquetes de consulta disponibles por los que puede navegar, a la izquierda.

:::image type="content" source="media\log-analytics\log-analytics-modal-resource.png" alt-text="Modal de consulta de Log Analytics" lightbox="media\log-analytics\log-analytics-modal-resource.png":::

Aunque cierre la pantalla modal, podrá navegar a los distintos paquetes de consulta y acceder directamente a los datos en forma de tablas en función del esquema de los registros y las métricas que ha habilitado en la configuración de diagnóstico. Aquí, puede crear sus propias consultas a partir de los datos mediante [KQL (Kusto)](/azure/data-explorer/kusto/query/). Puede encontrar más información sobre el uso, la edición y la creación de consultas en [Consultas de Log Analytics](/azure/azure-monitor/logs/queries).

:::image type="content" source="media\log-analytics\log-analytics-queries-resource.png" alt-text="Consultas de Log Analytics en el recurso" lightbox="media\log-analytics\log-analytics-queries-resource.png":::

:::image type="content" source="media\log-analytics\log-analytics-tables-resource.png" alt-text="Tablas de Log Analytics en el recurso" lightbox="media\log-analytics\log-analytics-tables-resource.png":::

## <a name="default-query-packs-for-call-summary-and-call-diagnostic-logs"></a>Paquetes de consulta predeterminados para los registros de resumen de llamadas y diagnóstico de llamadas
A continuación, se muestran descripciones de cada consulta del [paquete de consulta predeterminado](/azure/azure-monitor/logs/query-packs#default-query-pack) para los [registros de resumen de llamadas y diagnóstico de llamadas](call-logs-azure-monitor.md), junto con ejemplos de código y salidas de ejemplo para cada consulta disponible:
### <a name="call-overview-queries"></a>Consultas de información general de llamadas
#### <a name="number-of-participants-per-call"></a>Número de participantes por llamada

```
// Count number of calls and participants,
// and print average participants per call
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId
| summarize num_participants=count(), num_calls=dcount(CorrelationId)
| extend avg_participants = todecimal(num_participants) / todecimal(num_calls)
```

Salida del ejemplo:

:::image type="content" source="media\log-analytics\call-overview-sample.png" alt-text="Consulta de información general de llamadas":::

#### <a name="number-of-participants-per-group-call"></a>Número de participantes por llamada de grupo

```
// Count number of participants per group call
ACSCallSummary
| where CallType == 'Group'
| distinct CorrelationId, ParticipantId
| summarize num_participants=count() by CorrelationId
| summarize participant_counts=count() by num_participants
| order by num_participants asc 
| render columnchart with  (xcolumn = num_participants, title="Number of participants per group call")
```

Salida del ejemplo:

:::image type="content" source="media\log-analytics\participants-group-sample.png" alt-text="Consulta de participantes por llamada de grupo":::

#### <a name="ratio-of-call-types"></a>Proporción de tipos de llamada

```
// Ratio of call types
ACSCallSummary
| summarize call_types=dcount(CorrelationId) by CallType
| render piechart title="Call Type Ratio"

```

Salida del ejemplo:

:::image type="content" source="media\log-analytics\ratio-call-types-sample.png" alt-text="Consulta de proporción de tipos de llamada":::

#### <a name="call-duration-distribution"></a>Distribución de duración de llamada

```
// Call duration histogram
ACSCallSummary
| distinct CorrelationId, CallDuration
|summarize duration_counts=count() by CallDuration
| order by CallDuration asc
| render columnchart with (xcolumn = CallDuration, title="Call duration histogram")
```

Salida del ejemplo:

:::image type="content" source="media\log-analytics\call-duration-sample.png" alt-text="Consulta de duración de llamada":::

#### <a name="call-duration-percentiles"></a>Percentiles de duración de llamada

```
// Call duration percentiles
ACSCallSummary
| distinct CorrelationId, CallDuration
| summarize avg(CallDuration), percentiles(CallDuration, 50, 90, 99)
```

Salida del ejemplo:

:::image type="content" source="media\log-analytics\call-duration-percentile-example.png" alt-text="Consulta de percentil de duración de llamada":::

### <a name="endpoint-information-queries"></a>Consultas de información del punto de conexión

#### <a name="number-of-endpoints-per-call"></a>Número de puntos de conexión por llamada

```
// Count number of calls and endpoints,
// and print average endpoints per call
ACSCallSummary
| distinct CorrelationId, EndpointId
| summarize num_endpoints=count(), num_calls=dcount(CorrelationId)
| extend avg_endpoints = todecimal(num_endpoints) / todecimal(num_calls)
```

Salida del ejemplo:

:::image type="content" source="media\log-analytics\endpoints-per-call.png" alt-text="Consulta de puntos de conexión por llamada":::

#### <a name="ratio-of-sdk-versions"></a>Proporción de versiones del SDK

```
// Ratio of SDK Versions
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, SdkVersion
| summarize sdk_counts=count() by SdkVersion
| order by SdkVersion asc
| render piechart title="SDK Version Ratio"
```

Salida del ejemplo:

:::image type="content" source="media\log-analytics\sdk-ratio-sample.png" alt-text="Gráfico circular que muestra la proporción de versiones del SDK."::: :::image type="content" source="media\log-analytics\sdk-ratio-table-sample.png" alt-text="Tabla que muestra las versiones del SDK":::

#### <a name="ratio-of-os-versions-simplified-os-name"></a>Proporción de versiones del sistema operativo (nombre simplificado del sistema operativo)

```
// Ratio of OS Versions (simplified OS name)
ACSCallSummary
| distinct CorrelationId, ParticipantId, EndpointId, OsVersion
| extend simple_os = case(  indexof(OsVersion, "Android") != -1, tostring(split(OsVersion, ";")[0]),
                            indexof(OsVersion, "Darwin") != -1, tostring(split(OsVersion, ":")[0]),
                            indexof(OsVersion, "Windows") != -1, tostring(split(OsVersion, ".")[0]),
                            OsVersion
                        )
| summarize os_counts=count() by simple_os
| order by simple_os asc
| render piechart title="OS Version Ratio"
```

Salida del ejemplo:

:::image type="content" source="media\log-analytics\os-version-graph.png" alt-text="Gráfico circular que muestra las proporciones de sistemas operativos":::
:::image type="content" source="media\log-analytics\os-version-table.png" alt-text="Tabla que muestra las versiones del sistema operativo":::

### <a name="media-stream-queries"></a>Consultas de secuencias multimedia
#### <a name="streams-per-call"></a>Secuencias por llamada

```
// Count number of calls and streams,
// and print average streams per call
ACSCallDiagnostics
| summarize num_streams=count(), num_calls=dcount(CorrelationId)
| extend avg_streams = todecimal(num_streams) / todecimal(num_calls)
```
Salida del ejemplo:

:::image type="content" source="media\log-analytics\streams-call-output.png" alt-text="Consulta de secuencias por llamada":::

#### <a name="streams-per-call-histogram"></a>Histograma de secuencias por llamada

```
// Distribution of streams per call
ACSCallDiagnostics
| summarize streams_per_call=count() by CorrelationId
| summarize stream_counts=count() by streams_per_call
| order by streams_per_call asc
| render columnchart title="Streams per call histogram"
```

:::image type="content" source="media\log-analytics\streams-call-histogram.png" alt-text="Histograma de secuencias por llamada":::

#### <a name="ratio-of-media-types"></a>Proporción de tipos de elementos multimedia

```
// Ratio of media types by call
ACSCallDiagnostics
| summarize media_types=count() by MediaType
| render piechart title="Media Type Ratio"
```

:::image type="content" source="media\log-analytics\media-type-ratio.png" alt-text="Gráfico circular que muestra las proporciones de tipos de elementos multimedia":::

### <a name="quality-metrics-queries"></a>Consultas de métricas de calidad

#### <a name="average-telemetry-values"></a>Promedio de valores de telemetría

```
// Average telemetry values over all streams
ACSCallDiagnostics
| summarize Avg_JitterAvg=avg(JitterAvg),
            Avg_JitterMax=avg(JitterMax),
            Avg_RoundTripTimeAvg=avg(RoundTripTimeAvg),
            Avg_RoundTripTimeMax=avg(RoundTripTimeMax),
            Avg_PacketLossRateAvg=avg(PacketLossRateAvg),
            Avg_PacketLossRateMax=avg(PacketLossRateMax)
```

:::image type="content" source="media\log-analytics\avg-telemetry-values.png" alt-text="Promedio de valores de telemetría":::

#### <a name="jitteravg-histogram"></a>Histograma JitterAvg

```
// Jitter Average Histogram
ACSCallDiagnostics
| where isnotnull(JitterAvg)
| summarize JitterAvg_counts=count() by JitterAvg
| order by JitterAvg asc
| render columnchart with (xcolumn = JitterAvg, title="JitterAvg histogram")
```

:::image type="content" source="media\log-analytics\jitteravg-histogram.png" alt-text="Histograma de valores medios de vibración":::

#### <a name="jittermax-histogram"></a>Histograma JitterMax

```
// Jitter Max Histogram
ACSCallDiagnostics
| where isnotnull(JitterMax)
|summarize JitterMax_counts=count() by JitterMax
| order by JitterMax asc
| render columnchart with (xcolumn = JitterMax, title="JitterMax histogram")
```

:::image type="content" source="media\log-analytics\jittermax-histogram.png" alt-text="Histograma de valores máximos de vibración":::

#### <a name="packetlossrateavg-histogram"></a>Histograma PacketLossRateAvg
```
// PacketLossRate Average Histogram
ACSCallDiagnostics
| where isnotnull(PacketLossRateAvg)
|summarize PacketLossRateAvg_counts=count() by bin(PacketLossRateAvg, 0.01)
| order by PacketLossRateAvg asc
| render columnchart with (xcolumn = PacketLossRateAvg, title="PacketLossRateAvg histogram")
```

:::image type="content" source="media\log-analytics\packetloss-avg-histogram.png" alt-text="Histograma de valores medios de pérdida de paquetes":::

#### <a name="packetlossratemax-histogram"></a>Histograma PacketLossRateMax
```
// PacketLossRate Max Histogram
ACSCallDiagnostics
| where isnotnull(PacketLossRateMax)
|summarize PacketLossRateMax_counts=count() by bin(PacketLossRateMax, 0.01)
| order by PacketLossRateMax asc
| render columnchart with (xcolumn = PacketLossRateMax, title="PacketLossRateMax histogram")
```

:::image type="content" source="media\log-analytics\packetloss-max-histogram.png" alt-text="Histograma de valores máximos de pérdida de paquetes":::

#### <a name="roundtriptimeavg-histogram"></a>Histograma RoundTripTimeAvg
```
// RoundTripTime Average Histogram
ACSCallDiagnostics
| where isnotnull(RoundTripTimeAvg)
|summarize RoundTripTimeAvg_counts=count() by RoundTripTimeAvg
| order by RoundTripTimeAvg asc
| render columnchart with (xcolumn = RoundTripTimeAvg, title="RoundTripTimeAvg histogram")
```

:::image type="content" source="media\log-analytics\rtt-avg-histogram.png" alt-text="Histograma de valores medios de RTT":::

#### <a name="roundtriptimemax-histogram"></a>Histograma RoundTripTimeMax
```
// RoundTripTime Max Histogram
ACSCallDiagnostics
| where isnotnull(RoundTripTimeMax)
|summarize RoundTripTimeMax_counts=count() by RoundTripTimeMax
| order by RoundTripTimeMax asc
| render columnchart with (xcolumn = RoundTripTimeMax, title="RoundTripTimeMax histogram")
```

:::image type="content" source="media\log-analytics\rtt-max-histogram.png" alt-text="Histograma de valores máximos de RTT":::

#### <a name="poor-jitter-quality"></a>Baja calidad de vibración
```
// Get proportion of calls with poor quality jitter
// (defined as jitter being higher than 30ms)
ACSCallDiagnostics
| extend JitterQuality = iff(JitterAvg > 30, "Poor", "Good")
| summarize count() by JitterQuality
| render piechart title="Jitter Quality"
```

:::image type="content" source="media\log-analytics\jitter-quality.png" alt-text="Calidad de vibración":::


#### <a name="packetlossrate-quality"></a>Calidad PacketLossRate
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\packet-loss-quality.png" alt-text="Calidad de la velocidad de pérdida de paquetes":::

#### <a name="roundtriptime-quality"></a>Calidad RoundTripTime
```
// Get proportion of calls with poor quality packet loss
// rate (defined as packet loss being higher than 10%)
ACSCallDiagnostics
| extend PacketLossRateQuality = iff(PacketLossRateAvg > 0.1, "Poor", "Good")
| summarize count() by PacketLossRateQuality
| render piechart title="Packet Loss Rate Quality"
```

:::image type="content" source="media\log-analytics\rtt-quality.png" alt-text="Calidad de RTT":::

### <a name="parameterizable-queries"></a>Consultas parametrizables

#### <a name="daily-calls-in-the-last-week"></a>Llamadas diarias en la última semana
```
// Histogram of daily calls over the last week
ACSCallSummary
| where CallStartTime > now() - 7d
| distinct CorrelationId, CallStartTime
| extend hour  = floor(CallStartTime, 1d)
| summarize event_count=count() by day
| sort by day asc
| render columnchart title="Number of calls in last week"
```

:::image type="content" source="media\log-analytics\calls-per-week.png" alt-text="Llamadas diarias en la última semana":::

#### <a name="calls-per-hour-in-last-day"></a>Llamadas por hora en el último día
```
// Histogram of calls per hour in the last day
ACSCallSummary
| where CallStartTime > now() - 1d
| distinct CorrelationId, CallStartTime
| extend hour = floor(CallStartTime, 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render columnchart title="Number of calls per hour in last day"
```

:::image type="content" source="media\log-analytics\calls-per-hour.png" alt-text="Llamadas por hora en el último día":::