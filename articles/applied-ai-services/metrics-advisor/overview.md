---
title: ¿Qué es el servicio Azure Metrics Advisor?
titleSuffix: Azure Applied AI Services
description: ¿Qué es Metrics Advisor?
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: overview
ms.date: 07/06/2021
ms.author: mbullwin
ms.openlocfilehash: 295c166ec80eba638798dd452e27de945e41e9a6
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342521"
---
# <a name="what-is-azure-metrics-advisor"></a>¿Qué es Azure Metrics Advisor? 

Metrics Advisor forma parte de [Azure Applied AI Services](../../applied-ai-services/what-are-applied-ai-services.md), que usa inteligencia artificial para realizar la supervisión de datos y la detección de anomalías en los datos de series temporales. El servicio automatiza el proceso de aplicación de modelos a los datos y proporciona un conjunto de API y un área de trabajo basada en web para la ingesta de datos, la detección de anomalías y los diagnósticos, sin necesidad de conocer el aprendizaje automático. Los desarrolladores pueden crear aplicaciones de AIOps, mantenimiento de predictivo y supervisión empresarial basadas en el servicio. Use Metrics Advisor para:

* Analizar los datos multidimensionales de varios orígenes de datos
* Identificar y correlacionar las anomalías
* Configurar y ajustar el modelo de detección de anomalías usado en los datos
* Diagnosticar las anomalías y ayudar con el análisis de la causa principal

:::image type="content" source="media/metrics-advisor-overview.png" alt-text="Introducción a Metrics Advisor":::

Esta documentación contiene los siguientes tipos de artículos:
* Los [inicios rápidos](./Quickstarts/web-portal.md) son instrucciones paso a paso que permiten realizar llamadas al servicio y obtener los resultados en un breve período de tiempo. 
* Las [guías paso a paso](./how-tos/onboard-your-data.md) contienen instrucciones para usar el servicio de maneras más específicas o personalizadas.
* Los [artículos conceptuales](glossary.md) proporcionan explicaciones detalladas de la funcionalidad y las características del servicio.

## <a name="connect-to-a-variety-of-data-sources"></a>Conexión a diferentes orígenes de datos

Metrics Advisor puede conectarse e [ingerir datos de métricas multidimensionales](how-tos/onboard-your-data.md) de muchos almacenes de datos, entre los que se incluyen lo siguientes: SQL Server, Azure Blob Storage, MongoDB y otros.

## <a name="easy-to-use-and-customizable-anomaly-detection"></a>Detección de anomalías fácil de usar y personalizable

* Metrics Advisor selecciona automáticamente el mejor modelo para los datos, sin necesidad de realizar ningún aprendizaje automático.
* Supervise automáticamente cada serie temporal dentro de las [métricas multidimensionales](glossary.md#multi-dimensional-metric).
* Use la [optimización de parámetros](how-tos/configure-metrics.md) y los [comentarios interactivos](how-tos/anomaly-feedback.md) para personalizar el modelo que se aplica a los datos y los futuros resultados de la detección de anomalías.

## <a name="real-time-notification-through-multiple-channels"></a>Notificación en tiempo real en varios canales

Cada vez que se detecten anomalías, Metrics Advisor puede [enviar notificaciones en tiempo real](how-tos/alerts.md) mediante varios canales con enlaces, como los de correo electrónico, Web, de Teams y de Azure DevOps. La configuración flexible de alertas le permite personalizar cuándo y dónde enviar una notificación.

## <a name="smart-diagnostic-insights-by-analyzing-anomalies"></a>Información de diagnóstico inteligente mediante el análisis de anomalías

### <a name="analyze-root-cause-into-specific-dimension"></a>Análisis de la causa principal en una dimensión específica 

Metrics Advisor combina las anomalías detectadas en la misma métrica multidimensional en un árbol de diagnóstico para ayudarle a analizar la causa principal en una dimensión específica. También hay conclusiones analizadas automatizadas disponibles mediante el análisis de la mayor contribución de cada dimensión. 

### <a name="cross-metrics-analysis-using-metrics-graph"></a>Análisis entre métricas mediante el gráfico Métricas

Un [gráfico Métricas](./how-tos/metrics-graph.md) indica la relación entre las métricas. El análisis entre métricas se puede habilitar para ayudarle a detectar el estado anómalo entre todas las métricas relacionadas en una vista holística. Y, finalmente, le permite buscar la causa principal final.

Consulte [cómo diagnosticar un incidente](./how-tos/diagnose-an-incident.md) para obtener más información.

## <a name="typical-workflow"></a>Flujo de trabajo típico

El flujo de trabajo es sencillo: después de incorporar los datos, puede ajustar la detección de anomalías y crear configuraciones para que se adapten a su escenario.

1. [Cree un recurso de Azure](https://go.microsoft.com/fwlink/?linkid=2142156) para Metrics Advisor. 
2. Cree su primer monitor con el portal web.
    1. [Incorporación de los datos](./how-tos/onboard-your-data.md)
    2. [Ajuste de la configuración de detección de anomalías](./how-tos/configure-metrics.md)
    3. [Suscripción a anomalías para su notificación](./how-tos/alerts.md)
    4. [Visualización de la Información de diagnóstico](./how-tos/diagnose-an-incident.md)
3. Use la API de REST para personalizar la instancia.

## <a name="video"></a>Vídeo
* [Introducción a Metrics Advisor](https://www.youtube.com/watch?v=0Y26cJqZMIM)
* [Novedades de Cognitive Services](https://www.youtube.com/watch?v=7tCLJHdBZgM)

## <a name="data-retention--limitation"></a>Limitación y retención de datos: 

Metrics Advisor mantendrá como máximo **10 000** intervalos de tiempo ([¿qué es un intervalo?](tutorials/write-a-valid-query.md#what-is-an-interval)) contando hacia delante desde la marca de tiempo actual, con independencia de que haya datos disponibles o no. Se eliminarán los datos fuera del intervalo.  Asignación de retención de datos para el recuento de días para una granularidad de métricas diferente: 

| Granularity(min) |    Retention(day) |
|------------------| ------------------|
|  1 | 6,94 |
|  5 | 34,72|
| 15 | 104,1|
| 60(=hourly) | 416,67 |
| 1440(=daily)|10000,00|

También hay limitaciones adicionales; vea las [Preguntas más frecuentes](faq.yml#what-are-the-data-retention-and-limitations-of-metrics-advisor-) para obtener más información. 

## <a name="next-steps"></a>Pasos siguientes

* Explore una guía de inicio rápido: [Supervisión de la primera métrica en la web](quickstarts/web-portal.md).
* Explore una guía de inicio rápido: [Uso de las API de REST para personalizar su solución](./quickstarts/rest-api-and-client-library.md).