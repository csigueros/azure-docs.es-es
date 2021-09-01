---
title: Análisis más profundo de incidentes y evaluación de su impacto
titleSuffix: Azure Cognitive Services
description: Aprenda a sacar provecho de las herramientas de análisis para analizar incidentes con mayor profundidad.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: mbullwin
ms.openlocfilehash: 0aefc4207f064550b41f9341e946cae2f1611961
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342401"
---
# <a name="further-analyze-an-incident-and-evaluate-impact"></a>Análisis más profundo de incidentes y evaluación de su impacto

## <a name="metrics-drill-down-by-dimensions"></a>Exploración en profundidad de métricas por dimensiones

Al ver la información de los incidentes, puede que necesite obtener información más detallada; por ejemplo, para las distintas dimensiones y marcas de tiempo. Si los datos tienen una o varias dimensiones, puede usar la función de exploración en profundidad para obtener una vista más detallada. 

Para usar la función de exploración en profundidad, haga clic en la pestaña **Metric drilling** (Exploración de métricas) de **Incident hub** (Centro de incidentes). 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="Exploración de métricas ":::

La configuración **Dimensions** (Dimensiones) es una lista de dimensiones para un incidente; puede seleccionar otros valores de dimensión disponibles para cada uno. Después, los valores de la dimensión se cambian. La configuración **Timestamp** (Marca de tiempo) permite ver el incidente actual en momentos diferentes.

### <a name="select-drilling-options-and-choose-a-dimension"></a>Selección de opciones de exploración en profundidad y elección de una dimensión

Hay dos tipos de opciones de exploración en profundidad. **Drill down** (Explorar en profundidad) and **Horizontal comparison** (Comparación horizontal).

> [!Note]
> - Para la primera, puede explorar los datos de distintos valores de dimensión, excepto las dimensiones seleccionadas actualmente. 
> - Para la comparación horizontal, puede explorar los datos de distintos valores de dimensión, excepto las dimensiones generales.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="Exploración en profundidad":::

### <a name="value-comparison-for-different-dimension-values"></a>Comparación de valores para diferentes valores de dimensión

La segunda sección de la pestaña Drill down (Exploración en profundidad) es una tabla con comparaciones de distintos valores de dimensión. Incluye el valor, el valor de línea de base, el valor de diferencia, el valor delta y si se trata de una anomalía.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="Comparación horizontal" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>Comparaciones de valores y valores esperados para diferentes valores de dimensión

La tercera sección de la pestaña Drill down (Exploración en profundidad) es un histograma con los valores actuales y los esperados de diferentes valores de dimensión. El histograma se ordena por la diferencia entre el valor y el valor esperado. Puede encontrar fácilmente el valor inesperado con el mayor impacto. Por ejemplo, en la imagen anterior, podemos descubrir que, salvo el valor all-up (general), **US7** es el valor que más contribuye en la anomalía.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="Tabla de exploración en profundidad" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>Visualización de valores sin procesar
La última parte de la pestaña Drill down (Exploración en profundidad) es un gráfico de líneas de los valores sin procesar. Con este gráfico proporcionado, no es necesario ir a la página de la métrica para ver los detalles.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="Gráfico de líneas de exploración en profundidad" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="compare-time-series"></a>Comparación de series temporales

A veces, cuando se detecta una anomalía en una serie temporal específica, resulta útil compararla con varias series en una sola visualización. Haga clic en la pestaña **Compare tools** (Comparar herramientas) y, a continuación, haga clic en el botón azul **+ Add** (+ Agregar). 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="Adición de series para comparar" lightbox="../media/diagnostics/add-series.png":::

Seleccione una serie de la fuente de distribución de datos. Puede elegir la misma granularidad u otra. Seleccione las dimensiones de destino y cargue la tendencia de la serie y, a continuación, haga clic en **Ok** (Aceptar) para compararla con una serie anterior. La serie se colocará en una visualización. Puede seguir agregando más series para la comparación y obtener así más información. Haga clic en el menú desplegable de la parte superior de la pestaña **Compare tools** (Comparar herramientas) para hacer una comparación de los datos de series temporales durante un período desplazado.  

> [!Warning]
> Para realizar una comparación, el análisis de datos de series temporales puede requerir cambios en los puntos de datos, por lo que la granularidad de los datos debe admitirlos. Por ejemplo, si los datos son semanales y usa la comparación **Day over day** (Día a día), no obtendrá ningún resultado. En este ejemplo, se usaría la comparación **Month over month** (Mes a mes).

Después de seleccionar una comparación desplazada, puede elegir si desea comparar los valores de los datos, los valores delta o la diferencia porcentual.

## <a name="view-similar-anomalies-using-time-series-clustering"></a>Visualización de anomalías similares mediante la agrupación en clústeres de series temporales

Al ver un incidente, puede usar la pestaña **Similar time-series-clustering** (Agrupación en clústeres de series temporales) para ver las distintas series asociadas a él. Las series de un grupo se resumen juntas. En la imagen anterior, podemos saber que hay al menos dos grupos de series. Esta característica solo está disponible si se cumplen los siguientes requisitos.

- Las métricas deben tener una o varias dimensiones o valores de dimensión.
- Las series dentro de una métrica deben tener una tendencia similar.

Las dimensiones disponibles se muestran en la parte superior de la pestaña y se puede realizar una selección para especificar las series.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="Grupo de series":::