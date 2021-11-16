---
title: 'Tutorial: Análisis de métricas para un recurso de Azure'
description: Aprenda a analizar las métricas de un recurso de Azure mediante el Explorador de métricas en Azure Monitor.
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: fc996a635caea212d185b54c5493bf781d098f57
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350116"
---
# <a name="tutorial-analyze-metrics-for-an-azure-resource"></a>Tutorial: Análisis de métricas para un recurso de Azure
Las métricas son valores numéricos que se recopilan automáticamente a intervalos regulares y que describen algún aspecto de un recurso en un momento determinado. Por ejemplo, una métrica podría indicar el uso del procesador de una máquina virtual, el espacio libre en una cuenta de almacenamiento o el tráfico entrante de una red virtual. El explorador de métricas es una característica de Azure Monitor de Azure Portal que permite crear gráficos a partir de valores de métricas, correlacionar visualmente las tendencias e investigar los repuntes o las caídas en los valores de las métricas. Utilice el explorador de métricas para trazar gráficos a partir de métricas creadas por sus recursos de Azure e investigue su estado y uso. 

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Abrir el explorador de métricas para un recurso de Azure.
> * Seleccionar una métrica para trazar en un gráfico.
> * Realizar agregaciones diferentes de valores de métricas
> * Modificar el intervalo de tiempo y la granularidad del gráfico


El siguiente es un vídeo que muestra un escenario más amplio que el procedimiento descrito en este artículo. Si no está familiarizado con las métricas, le recomendamos que lea este artículo primero y que vea después el vídeo para conocer más detalles. 

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4qO59]

## <a name="prerequisites"></a>Prerrequisitos
Para completar este tutorial, necesitará lo siguiente: 

- Un recurso de Azure para supervisar. Puede usar cualquier recurso de la suscripción de Azure que admita métricas. Para determinar si un recurso es compatible con las métricas, vaya a su menú en Azure Portal y busque la opción **Métricas** en la sección **Supervisión** del menú.


## <a name="open-metrics-explorer"></a>Abrir el explorador de métricas 
En la sección **Supervisión** del menú de su recurso, seleccione **Métricas**. El ámbito ya se ha rellenado con el recurso. El ejemplo siguiente es de una cuenta de almacenamiento, pero tendrá un aspecto similar a otros servicios de Azure.

:::image type="content" source="media/tutorial-metrics/metrics-menu.png" lightbox="media/tutorial-metrics/metrics-menu.png" alt-text="Elemento de menú de métricas":::

Seleccione **Espacio de nombres** si el ámbito tiene más de uno. El espacio de nombres es una forma de organizar las métricas para que se puedan encontrar fácilmente. Por ejemplo, las cuentas de almacenamiento tienen espacios de nombres aparte para almacenar las métricas de File service, Table service, Blob service y Queue service. Muchos tipos de recursos solo tienen un espacio de nombres.

Seleccione una métrica de una lista de métricas disponibles para el ámbito y el espacio de nombres seleccionados.

:::image type="content" source="media/tutorial-metrics/metric-picker.png" lightbox="media/tutorial-metrics/metric-picker.png" alt-text="Seleccione el espacio de nombres y la métrica":::

Si lo desea, cambie el valor de **Agregación** de la métrica. Esto define cómo se agregarán los valores de las métricas en la granularidad de tiempo del gráfico. Por ejemplo, si la granularidad de tiempo se establece en 15 minutos y la agregación se establece en Suma, cada punto del gráfico será la suma de todos los valores recopilados en cada segmento de 15 minutos.

:::image type="content" source="media/tutorial-metrics/chart.png" lightbox="media/tutorial-metrics/chart.png" alt-text="La captura de pantalla muestra un gráfico con el título Resumen de entradas de contosoretailweb.":::


Use el botón **Agregar métrica** y repita estos pasos si quiere ver varias métricas trazadas en el mismo gráfico. Seleccione el botón **Nuevo gráfico** para ver varios gráficos en una vista.

## <a name="select-a-time-range-and-granularity"></a>Selección de un intervalo de tiempo y granularidad

El gráfico muestra las últimas 24 horas de los datos de métricas de forma predeterminada. Use el selector de tiempo para cambiar el **intervalo de tiempo** del gráfico o la **granularidad de tiempo** que define el intervalo de tiempo para cada punto de datos. El gráfico utiliza la agregación especificada para calcular todos los valores muestreados a lo largo de la granularidad de tiempo especificada.

:::image type="content" source="media/tutorial-metrics/time-picker.png" lightbox="media/tutorial-metrics/time-picker.png" alt-text="Panel para cambiar el intervalo de tiempo":::

Use la herramienta del **pincel de tiempo** para investigar un área interesante del gráfico, como un repunte o una caída. Coloque el puntero del mouse al principio del área, haga clic y mantenga presionado el botón izquierdo, arrastre al otro lado del área y suelte el botón. El gráfico acercará el intervalo de tiempo. 

:::image type="content" source="media/tutorial-metrics/time-brush.png" lightbox="media/tutorial-metrics/time-brush.png" alt-text="Pincel de tiempo":::

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensión y divisiones
Consulte las siguientes referencias para características avanzadas que permiten realizar análisis adicionales de las métricas e identificar posibles valores atípicos en los datos.

- El [filtrado](../essentials/metrics-charts.md#filters) permite elegir qué valores de dimensión se van a incluir en el gráfico. Por ejemplo, puede que le interese mostrar las solo las solicitudes correctas al representar gráficamente una métrica de *tiempo de respuesta del servidor*. 

- Las [divisiones](../essentials/metrics-charts.md#apply-splitting) controlan si el gráfico va a mostrar líneas independientes por cada valor de una dimensión, o si por el contrario va a agregar los valores en una sola línea. Por ejemplo, puede ver una línea correspondiente a un tiempo de respuesta promedio de todas las instancias de servidor, o ver líneas independientes para cada servidor. 

Vea [ejemplos de gráficos](../essentials/metric-chart-samples.md) que tienen filtrado y divisiones aplicados.

## <a name="advanced-chart-settings"></a>Configuración gráfico avanzada

Se puede personalizar el estilo y el título de un gráfico, así como modificar la configuración de gráfico avanzada. Cuando haya terminado de personalizarlo, puede anclarlo a un panel para guardar el trabajo. También se pueden configurar alertas de métricas. Consulte [Características avanzadas del Explorador de métricas de Azure](../essentials/metrics-charts.md#locking-the-range-of-the-y-axis) para aprender sobre estas y otras características avanzadas del explorador de métricas de Azure Monitor.


## <a name="next-steps"></a>Pasos siguientes
Ahora que ha aprendido a trabajar con métricas en Azure Monitor, aprenda a crear una regla de alertas de métricas para recibir notificaciones cuando un valor de métrica indique un posible problema.

> [!div class="nextstepaction"]
> [Creación de una alerta de métrica en Azure Monitor](../alerts/tutorial-metric-alert.md)

