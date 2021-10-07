---
title: Azure Data Explorer Insights | Microsoft Docs
description: En este artículo se describe cómo usar Azure Data Explorer Insights
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: b522e510968b97d80917bc6fa4123d33e4480cbf
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662217"
---
# <a name="azure-data-explorer-insights"></a>Azure Data Explorer Insights

Azure Data Explorer Insights proporciona una supervisión completa de los clústeres, ya que ofrece una vista unificada de su rendimiento, operaciones, uso y errores.

Ofrece lo siguiente:

-    **Perspectiva a gran escala**. Muestra una instantánea de las métricas principales de los clústeres, con el fin de realizar un seguimiento sencillo del rendimiento de las consultas, la ingesta y las operaciones de exportación.
-   **Análisis en profundidad**. Puede analizar en profundidad un clúster determinado de Azure Data Explorer para poder realizar análisis detallados.
-    **Personalización**. Esta opción le permite cambiar las métricas que quiere ver y modificar, o establecer umbrales en consonancia con sus límites y guardar sus propios libros personalizados. Los gráficos de un libro se pueden anclar a los paneles de Azure.

Este artículo le ayuda a saber cómo incorporar y usar Azure Data Explorer Insights.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Vista desde Azure Monitor (perspectiva a gran escala)

Desde Azure Monitor, puede ver las métricas de rendimiento principales del clúster. Estas métricas incluyen información sobre las consultas, la ingesta y las operaciones de exportación de varios clústeres de la suscripción. Asimismo, pueden ayudarle a identificar problemas de rendimiento.

Para ver el rendimiento de los clústeres en todas las suscripciones, realice los pasos siguientes:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

2. A continuación, seleccione **Supervisar** en el panel izquierdo. En **Centro de conclusiones**, seleccione **Clústeres de Azure Data Explorer**.

![Captura de pantalla de las selecciones para ver el rendimiento de los clústeres de Azure Data Explorer.](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>Pestaña Información general

En la pestaña **Información general** de la suscripción seleccionada, la tabla muestra las métricas interactivas de los clústeres de Azure Data Explorer agrupadas dentro de la suscripción. Puede filtrar los resultados en función de las opciones que seleccione en las siguientes listas desplegables:

* **Suscripciones**: solo se enumeran las suscripciones que tienen clústeres de Azure Data Explorer.

* **Clústeres de Azure Data Explorer**: de forma predeterminada, solo se preseleccionan hasta cinco clústeres. Si selecciona todos los clústeres o varios de ellos en el selector de ámbitos, se devolverán como máximo 200 clústeres.

* **Intervalo de tiempo**: de forma predeterminada, la tabla muestra las últimas 24 horas de información en función de las selecciones correspondientes realizadas.

El icono de contador, que se encuentra bajo la lista desplegable, acumula el número total de clústeres de Azure Data Explorer de las suscripciones seleccionadas y refleja cuántas están seleccionadas. Hay codificaciones de color condicionales para las columnas: **Mantener activo**, **CPU**, **Uso de ingesta** y **Uso de caché**. Las celdas con codificación naranja tienen valores que no son sostenibles para el clúster. 

Para comprender mejor lo que representa cada una de estas métricas, se recomienda leer la documentación sobre las [métricas de Azure Data Explorer](/azure/data-explorer/using-metrics#cluster-metrics).

### <a name="query-performance-tab"></a>Pestaña Rendimiento de las consultas

En la pestaña **Rendimiento de consultas** se muestra la duración de las consultas, el número total de consultas simultáneas y el número total de consultas limitadas.

![Captura de pantalla de la pestaña Rendimiento de las consultas.](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>Pestaña Ingestion Performance (Rendimiento de ingesta)

En esta pestaña **Rendimiento de la ingesta** se muestra la latencia de la ingesta, los resultados de ingesta correctos, los resultados de ingesta con errores, el volumen de ingesta y los eventos procesados para los centros de IoT o de eventos.

[![Captura de pantalla de la pestaña Rendimiento de la ingesta.](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>Pestaña Streaming Ingest Performance (Rendimiento de ingesta de streaming)

La pestaña **Rendimiento de la ingesta de streaming** proporciona información sobre la velocidad de datos media, la duración media y la tasa de solicitudes.

### <a name="export-performance-tab"></a>Pestaña Export Performance (Rendimiento de exportación)

La pestaña **Rendimiento de exportación** proporciona información sobre los registros exportados, la demora, el recuento pendiente y el porcentaje de uso de las operaciones de exportación continua.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Vista de un recurso de clúster de Azure Data Explorer (análisis en profundidad)

Para acceder a Azure Data Explorer Insights directamente desde un clúster de Azure Data Explorer:

1. En Azure Portal, seleccione **Clústeres de Azure Data Explorer**.

2. En la lista, elija un clúster de Azure Data Explorer. En la sección Supervisión, seleccione **Conclusiones**.

También se puede acceder a estas vistas seleccionando el nombre del recurso de un clúster de Azure Data Explorer desde la vista de conclusiones de Azure Monitor.

> [!NOTE]
> Azure Data Explorer Insights combina los registros y las métricas para proporcionar una solución de supervisión global. La inclusión de visualizaciones basadas en registros requiere que los usuarios [habiliten el registro de diagnóstico de su clúster de Azure Data Explorer y lo envíen a un área de trabajo de Log Analytics](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs). Los registros de diagnóstico que deben habilitarse son **Comando**, **Consulta**, **SucceededIngestion**, **FailedIngestion**, **IngestionBatching**, **TableDetails** y **TableUsageStatistics**. (Tenga en cuenta que habilitar los registros **succeededIngestion** puede ser costoso. Por ello, habilítelos solo si necesita supervisar las ingestas correctas).

![Captura de pantalla del botón para configurar registros para la supervisión.](./media/data-explorer/enable-logs.png)

### <a name="overview-tab"></a>Pestaña Información general

La pestaña **Información general** muestra:

- Iconos de métricas que resaltan la disponibilidad y el estado general del clúster para realizar una evaluación rápida del estado.

- Un resumen del estado activo de las [recomendaciones de Azure Advisor](/azure/data-explorer/azure-advisor) y del [estado de los recursos](/azure/data-explorer/monitor-with-resource-health).

- Gráficos que muestran los consumidores principales de CPU y memoria y el número de usuarios únicos a lo largo del tiempo.

[![Captura de pantalla de la vista de un recurso de clúster de Azure Data Explorer.](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

### <a name="key-metrics-tab"></a>Pestaña de Métricas clave

La pestaña de **Métricas clave** muestra una vista unificada de algunas de las métricas del clúster. Se agrupan en métricas generales, métricas relacionadas con consultas, métricas relacionadas con la ingesta y métricas relacionadas con la ingesta de streaming.

[![Captura de pantalla de gráficos en la pestaña Métricas clave.](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

### <a name="usage-tab"></a>Pestaña Uso

La pestaña **Uso** permite a los usuarios profundizar en el rendimiento de los comandos y las consultas del clúster. En esta pestaña, puede:
 
- Ver qué grupos de cargas de trabajo, usuarios y aplicaciones envían la mayoría de las consultas o consumen la mayor parte de la CPU y la memoria. A continuación, puede comprender qué cargas de trabajo envían las consultas más pesadas para que el clúster las procese.
- Identificar los grupos de cargas de trabajo, usuarios y aplicaciones principales por consultas con error.
- Identificar los cambios recientes en el número de consultas, en comparación con la media diaria histórica (en los últimos 16 días), por grupo de cargas de trabajo, usuario y aplicación.
- Identificar las tendencias y los picos en el número de consultas y el consumo de memoria y CPU por grupo de cargas de trabajo, usuario, aplicación y tipo de comando.

[![Captura de pantalla de la vista de operaciones con gráficos de anillos relacionados con comandos y consultas.](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![Captura de pantalla de la vista de operaciones con gráficos de líneas relacionados con las consultas y la memoria.](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

### <a name="tables-tab"></a>Pestaña Tablas

En la pestaña **Tablas** se muestran las propiedades históricas y más recientes de las tablas del clúster. Puede ver qué tablas consumen más espacio. Asimismo, también puede realizar un seguimiento del historial de crecimiento por tamaño de tabla, los datos de acceso frecuente y el número de filas a lo largo del tiempo.

### <a name="cache-tab"></a>Pestaña Caché

La pestaña **Caché** permite a los usuarios analizar los patrones de la ventana de recuperación de las consultas reales y compararlos con la directiva de caché configurada (para cada tabla). Puede identificar las tablas que usan la mayoría de las consultas y aquellas que no se consultan nunca, y adaptar la directiva de caché en consecuencia. 

Puede obtener recomendaciones de directivas de caché en tablas específicas en Azure Advisor. Actualmente, las recomendaciones de caché solo están disponibles en el [panel principal de Azure Advisor](/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations). Se basan en la ventana de búsqueda de consultas reales de los últimos 30 días y en una directiva de caché sin optimizar para, al menos, el 95 % de las consultas. 

Las recomendaciones de reducción de caché en Azure Advisor están disponibles para los clústeres que están "limitados por datos". Esto significa que el clúster tiene poca CPU y un uso de ingesta bajo, pero debido a la alta capacidad de datos, el clúster no se puede reducir horizontalmente ni verticalmente.

[![Captura de pantalla de los detalles de la caché.](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

### <a name="cluster-boundaries-tab"></a>Pestaña Límites del clúster

La pestaña **Límites del clúster** muestra los límites del clúster en función de su uso. En esta pestaña puede inspeccionar el uso de la CPU, la ingesta y la caché. Los valores de estas métricas pueden ser **Baja**, **Media** o **Alta**. Estas métricas y valores son importantes a la hora de decidir el número óptimo de SKU e instancias para el clúster. Asimismo, se tienen en cuenta en las recomendaciones de SKU o tamaño de Azure Advisor. 

En esta pestaña puede seleccionar el icono de una métrica y profundizar para conocer su tendencia y cómo se decide su puntuación. También puede ver la recomendación de tamaño y SKU de Azure Advisor del clúster. Por ejemplo, en la siguiente imagen, puede ver que todas las métricas se puntúan como **Baja**. El clúster recibe una recomendación de costos que le permitirá reducirlos horizontalmente o verticalmente así y ahorrar dinero.

> [!div class="mx-imgBorder"]
> [![Captura de pantalla de los límites del clúster.](./media/data-explorer/cluster-boundaries.png)](./media/data-explorer/cluster-boundaries.png#lightbox)

## <a name="pin-to-an-azure-dashboard"></a>Anclar a un panel de Azure

Cualquiera de las secciones de métricas (de la perspectiva "a gran escala") se puede anclar a un panel de Azure; para ello, seleccione el icono con forma de chincheta que se encuentra en la parte superior derecha de la sección.

![Captura de pantalla del icono con forma de chincheta seleccionado.](./media/data-explorer/pin.png)

## <a name="customize-azure-data-explorer-insights"></a>Personalización de Azure Data Explorer Insights

Puede editar el libro para personalizarlo en función de sus necesidades de análisis de datos:
* Definir el ámbito del libro para seleccionar siempre una suscripción o unos clústeres de Azure Data Explorer determinados.
* Cambiar las métricas de la cuadrícula.
* Cambiar los umbrales o la representación o codificación del color.

Puede iniciar las personalizaciones; para ello, seleccione el botón **Personalizar** de la barra de herramientas superior.

![Captura de pantalla del botón Personalizar.](./media/data-explorer/customize.png)

Las personalizaciones se guardan en un libro personalizado para evitar sobrescribir la configuración predeterminada del libro publicado. Los libros se guardan en un recurso compartido, bien en la sección **Mis informes** o en la sección **Informes compartidos** accesibles a cualquiera con acceso al grupo de recursos. Después de guardar el libro personalizado, vaya a la galería de libros para abrirlo.

![Captura de pantalla de la galería de libros.](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>Solución de problemas

Para obtener instrucciones generales sobre la solución de problemas, consulte el artículo dedicado a la [Solución de problemas de conclusiones basadas en libros](troubleshoot-workbooks.md).

Las secciones siguientes le ayudarán a diagnosticar y solucionar algunos de los problemas comunes que pueden surgir al usar Azure Data Explorer Insights.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>¿Por qué veo todas mis suscripciones en el selector de suscripciones?

Azure Data Explorer Insights solo muestra las suscripciones que contienen clústeres de Azure Data Explorer que se han seleccionado del filtro de suscripción indicado. Seleccione un filtro de suscripción en **Directorio y suscripción** en Azure Portal.

![Captura de pantalla de la selección de un filtro de suscripción.](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-dont-i-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-section"></a>¿Por qué no veo ningún dato del clúster de Azure Data Explorer en las secciones Uso, Tablas o Caché?

Para ver los datos basados en registros, debe [habilitar los registros de diagnóstico](/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) para cada uno de los clústeres de Azure Data Explorer que quiera supervisar. Esto puede realizarse en la configuración de diagnóstico de cada clúster. Asimismo, tendrá que enviar los datos a un área de trabajo de Log Analytics. Los registros de diagnóstico que se deben habilitar son: **Command**, **Query**, **TableDetails** y **TableUsageStatistics**.

### <a name="ive-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Ya he habilitado los registros del clúster de Azure Data Explorer. ¿Por qué todavía no puedo ver mis datos en las secciones Comandos y Consultas?

Actualmente, los registros de diagnóstico no funcionan con carácter retroactivo. Los datos comenzarán a aparecer después de que se hayan realizado acciones en Azure Data Explorer. Por lo tanto, pueden tardar algún tiempo (desde horas hasta un día), en función de la actividad que presente el clúster de Azure Data Explorer.

## <a name="next-steps"></a>Pasos siguientes

Conozca los escenarios para los que están concebidos los libros, cómo crear informes y personalizar los ya existentes y otros muchos temas en el artículo [Creación de informes interactivos con libros de Azure Monitor](../visualize/workbooks-overview.md).
