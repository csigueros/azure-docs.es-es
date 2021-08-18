---
title: Supervisión de Time Series Insights | Microsoft Docs
description: Supervise la disponibilidad, el rendimiento y el funcionamiento de Time Series Insights.
author: tedvilutis
ms.author: tvilutis
manager: cnovak
ms.reviewer: orspodek
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: caf47ca56114ce559288ea1d115b2c09b9959324
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369372"
---
# <a name="monitoring-time-series-insights"></a>Supervisión de Time Series Insights

Si tiene aplicaciones y procesos empresariales críticos que dependen de recursos de Azure, querrá supervisar esos recursos para su disponibilidad, rendimiento y funcionamiento. En este artículo se describen los datos de supervisión generados por Time Series Insights y cómo puede usar las características de Azure Monitor para analizar y alertar sobre estos datos.

## <a name="monitor-overview"></a>Información general de supervisión

La página **información general** de Azure Portal de cada entorno de Time Series Insights incluye una sucinta vista del uso de los recursos, como el número de mensajes recibidos y el número de bytes almacenados. Esta información es útil, pero solo una pequeña cantidad de datos de supervisión está disponible en este panel. Algunos de estos datos se recopilan automáticamente y están disponibles para su análisis en cuanto se crea el recurso. Puede habilitar tipos adicionales de recopilación de datos con cierta configuración adicional.

## <a name="what-is-azure-monitor"></a>¿Qué es Azure Monitor?

Time Series Insights crea datos de supervisión mediante [Azure Monitor](../azure-monitor/overview.md), un servicio de supervisión de pila completo de Azure que proporciona un conjunto completo de características para supervisar los recursos de Azure, además de los recursos locales y en otras nubes.

Comience leyendo el artículo [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), en el que se describen los conceptos siguientes:

- ¿Qué es Azure Monitor?
- Costos asociados con la supervisión
- Datos de supervisión recopilados en Azure
- Configuración de la recolección de datos
- Herramientas estándar en Azure para analizar datos de supervisión y alertar sobre ellos

Las secciones siguientes complementan este artículo mediante la descripción de los datos específicos que se recopilan de Azure Time Series Insights. En estas secciones también se proporcionan ejemplos para configurar la recopilación y el análisis de datos con Azure Tools.

> [!TIP]
> Para comprender los costos asociados a Azure Monitor, consulte [Uso y costos estimados](../azure-monitor//usage-estimated-costs.md). Para comprender el tiempo necesario para que los datos aparezcan en Azure Monitor, consulte [Tiempo de ingesta de datos de registro](../azure-monitor/logs/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-time-series-insights"></a>Supervisión de datos de Azure Time Series Insights

Azure Time Series Insights recopila los mismos tipos de datos de supervisión que otros recursos de Azure que se describen en [Supervisión de datos de recursos de Azure](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data).

Consulte [Referencia de datos de supervisión de Azure Time Series Insights](how-to-monitor-tsi-reference.md) para obtener una referencia detallada de los registros y las métricas que se pueden recopilar.

## <a name="collection-and-routing"></a>Recopilación y enrutamiento

Las métricas de la plataforma se recopilan y almacenan de forma automática, pero se pueden enrutar a otras ubicaciones mediante una configuración de diagnóstico.

Los registros de recursos no se recopilan ni almacenan hasta que se crea una configuración de diagnóstico y se enrutan a una o varias ubicaciones.
Consulte [Creación de una configuración de diagnóstico para recopilar registros de plataforma y métricas en Azure](../azure-monitor/essentials/diagnostic-settings.md) para ver el proceso detallado para crear una configuración de diagnóstico mediante el Azure Portal, la CLI o PowerShell. Cuando se crea una configuración de diagnóstico, se especifican las categorías de registros que se van a recopilar.

Se pueden recopilar registros de las siguientes categorías de Azure Time Series Insights:

   | Category | Descripción |
   |---|---|
   | Entrada  | La categoría Entrada realiza un seguimiento de los errores que se producen en la canalización de entrada. Esta categoría incluye los errores que se producen al recibir eventos (como errores de conexión a un origen de eventos) y al procesamiento eventos (como errores al analizar una carga de eventos). |

## <a name="analyzing-metrics"></a>Análisis de métricas

Puede analizar las métricas de Azure Time Series Insights con métricas de otros servicios de Azure mediante la opción Métricas del menú de Azure Monitor. Consulte [Introducción al explorador de métricas de Azure](../azure-monitor/essentials/metrics-getting-started.md) para más información sobre esta herramienta.

Para obtener una lista de las métricas de la plataforma recopiladas, consulte [Supervisión de las referencia de datos de Azure Time Series Insights](how-to-monitor-tsi-reference.md#metrics).

En este ejemplo se muestra el número de bytes recibidos de todos los orígenes de eventos en el entorno de Azure Time Series Insights.

**Bytes de entrada recibidos** [![Bytes recibidos de la entrada de la serie temporal de Azure](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox)

En el ejemplo se muestra el número de bytes procesados correctamente y disponibles para la consulta en el entorno de Azure Time Series Insights.

**Bytes de entrada almacenados** [![Bytes almacenados de la entrada de la serie temporal de Azure](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>Análisis de datos
Puede acceder a los registros de los recursos como blob en una cuenta de almacenamiento, como datos de eventos o a través de consultas de Log Analytics.

Los datos de los registros de Azure Monitor se almacenan en tablas; cada tabla tiene su propio conjunto de propiedades únicas.

Todos los registros de recursos de Azure Monitor tienen los mismos campos seguidos de campos específicos del servicio. El esquema común se describe en [Esquema de registros de recursos de Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Para ver una lista de los tipos de registros de recursos recopilados para Azure Time Series Insights, consulte [Referencia de datos de supervisión de Azure Time Series Insights](how-to-monitor-tsi-reference.md#resource-logs).

Azure Time Series Insights almacena los datos en las siguientes tablas.

| Tabla | Descripción |
|:---|:---|
| TSIIngress | La tabla que almacena los datos de la categoría Entrada. La categoría Entrada realiza un seguimiento de los errores que se producen en la canalización de entrada. Esta categoría incluye los errores que se producen al recibir eventos (como errores de conexión a un origen de eventos) y al procesamiento eventos (como errores al analizar una carga de eventos).

Para enrutar los datos a registros de Azure Monitor, debe crear una configuración de diagnóstico para enviar los registros de recursos o las métricas de plataforma a un área de trabajo de Log Analytics. Para obtener más información, vea [Recopilación y enrutamiento](../iot-hub/monitor-iot-hub.md#collection-and-routing).

## <a name="sample-queries"></a>Consultas de ejemplo

A continuación se muestran las consultas que puede usar para ayudarle a supervisar el entorno de Azure Time Series Insights:

+ Obtener detalles de los errores de la conexión del origen de eventos en los últimos cinco días:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Obtener detalles de los mensajes no válidos recibidos en los últimos cinco días:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Alertas

Las alertas de Azure Monitor le informan de forma proactiva cuando se detectan condiciones importantes en los datos que se supervisan. Permiten identificar y solucionar las incidencias en el sistema antes de que los clientes puedan verlos. Puede establecer alertas en [métricas](../azure-monitor/alerts/alerts-metric-overview.md), [registros](../azure-monitor/alerts/alerts-unified-log.md) y el [registro de actividad](../azure-monitor/alerts/activity-log-alerts.md). Cada tipo de alerta tiene sus ventajas y desventajas.

Al crear una regla de alerta basada en métricas de plataforma, recuerde que para las métricas de plataforma de Azure Time Series Insights que se recopilan en unidades de recuento, es posible que algunas agregaciones no estén disponibles o se puedan usar.

## <a name="next-steps"></a>Pasos siguientes

* Consulte [Referencia de datos de supervisión de Azure Time Series Insights](how-to-monitor-tsi-reference.md) para obtener una referencia de los registros y las métricas creados por Azure Time Series Insights.
* Para más información sobre la supervisión de recursos de Azure, consulte [Supervisión de recursos de Azure con Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).