---
title: Gráfico de métricas de Metrics Advisor
titleSuffix: Azure Cognitive Services
description: En este artículo se explica cómo configurar Metrics Graph y visualizar las anomalías relacionadas en los datos.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: 7842cdd6d5336e90346d666d8c2d31d20c19caa0
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342026"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>Procedimiento: Creación de un gráfico de métricas para analizar las métricas relacionadas

Cada serie temporal de Metrics Advisor se supervisa por separado en función de un modelo que aprende de datos históricos para predecir tendencias futuras. Se detectarán anomalías si algún punto de datos se sale del patrón histórico. En algunos casos, sin embargo, varias métricas pueden estar relacionadas entre sí y las anomalías se deben analizar en varias métricas. El **grafo de métricas** es la herramienta que le ayudará con esto. 

Por ejemplo, si tiene varias métricas que supervisan su negocio desde distintas perspectivas, se aplicará la detección de anomalías a cada una de ellas. Sin embargo, en un caso empresarial real, las anomalías detectadas en varias métricas pueden tener una relación entre sí, por lo que la detección de esas relaciones y el análisis de la causa principal serían útiles a la hora de abordar problemas reales. El grafo de métricas ayuda a correlacionar automáticamente las anomalías detectadas en las métricas relacionadas para acelerar el proceso de solución de problemas. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>Selección de una métrica para colocar el primer nodo en el gráfico

Haga clic en la pestaña **Grafo de métricas** de la barra de navegación. El primer paso para la creación de un grafo de métricas consiste en colocar un nodo en el grafo. Seleccione una fuente de distribución de datos y una métrica en la parte superior de la página. Aparecerá un mensaje en la barra inferior. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="Selección de la métrica":::

## <a name="add-a-noderelation-on-existing-node"></a>Adición de un nodo o una relación a un nodo

A continuación, debe agregar otro nodo y especificar una relación con uno o varios nodos. Seleccione un nodo y haga clic con el botón derecho en él. Aparecerá un menú contextual con varias opciones. 

Seleccione **Agregar relación** para elegir otra métrica y especificar el tipo de relación entre los dos nodos. También puede aplicar filtros de dimensión específicos. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="Adición de un nodo y una relación":::

Después de repetir los pasos anteriores, tendrá un gráfico de métricas que describe las relaciones entre todas las métricas relacionadas.

Hay otras acciones que puede realizar en el grafo: 
1.  Eliminar un nodo
2.  Ir a métricas
3.  Ir al Centro de incidentes
4.  Expanda
5.  Eliminar relación

## <a name="legend-of-metrics-graph"></a>Leyenda del grafo de métricas

Cada nodo del grafo representa una métrica. Hay cuatro tipos de nodos en el grafo de métricas:

-  **Nodo verde**: el nodo que representa la gravedad del incidente de la métrica actual es bajo.
- **Nodo naranja**: el nodo que representa la gravedad del incidente de la métrica actual es medio.
- **Nodo rojo**: el nodo que representa la gravedad del incidente de la métrica actual es alto.
- **Nodo azul**: nodo que no tiene gravedad de la anomalía.


## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>Visualización del estado de anomalías de las métricas relacionadas en Incident hub (Centro de incidentes)

Cuando se crea el gráfico de métricas, siempre que se detecta una anomalía en las métricas del gráfico, podrá ver los estados de anomalías relacionados y ver un resumen del incidente. 

Haga clic en un incidente dentro del gráfico y desplácese hacia abajo hasta **cross metrics analysis** (análisis entre métricas), debajo de la información de diagnóstico.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="Visualización de anomalías y métricas relacionadas":::

## <a name="next-steps"></a>Pasos siguientes

- [Ajuste de la detección de anomalías mediante comentarios](anomaly-feedback.md)
- [Diagnóstico de incidentes](diagnose-an-incident.md)
- [Configuración de métricas y ajuste de la configuración de la detección](configure-metrics.md)