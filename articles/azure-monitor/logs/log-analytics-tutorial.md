---
title: Tutorial de Log Analytics
description: Aprenda en este tutorial a usar las características de Log Analytics en Azure Monitor para crear y ejecutar una consulta de registro, y analizar sus resultados en Azure Portal.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/28/2021
ms.openlocfilehash: bf19056cbde9c719c8d8665d2fc9a954e7a943b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729343"
---
# <a name="log-analytics-tutorial"></a>Tutorial de Log Analytics
Log Analytics es una herramienta de Azure Portal que se usa para editar y ejecutar consultas de registros en los datos que se recopilan en los registros de Azure Monitor, y analizar los resultados de forma interactiva. Puede usar consultas de Log Analytics para recuperar registros que coincidan con determinados criterios, identificar tendencias, analizar patrones y proporcionar una gran variedad de conclusiones sobre los datos. 

Este tutorial le guía por la interfaz de Log Analytics, le permite comenzar con algunas consultas básicas y le muestra cómo puede trabajar con los resultados. Aprenderá lo siguiente:

> [!div class="checklist"]
> * Comprender el esquema de datos del registro
> * Escribir y ejecutar consultas sencillas, y modificar el intervalo de tiempo de las consultas
> * Filtrar, ordenar y agrupar los resultados de las consultas
> * Ver, modificar y compartir los objetos visuales de los resultados de las consultas
> * Cargar, exportar y copiar consultas y resultados

> [!IMPORTANT]
> En este tutorial, usará las características de Log Analytics para crear una consulta y usar otra consulta de ejemplo. Cuando esté listo para obtener información sobre la sintaxis de las consultas e iniciar la edición directa de la propia consulta, lea el [tutorial del lenguaje de consulta de Kusto](/azure/data-explorer/kusto/query/tutorial?pivots=azuremonitor). Este tutorial le guiará por consultas de ejemplo que podrá editar y ejecutar en Log Analytics. Usa varias de las características que aprenderá en este tutorial.


## <a name="prerequisites"></a>Requisitos previos
En este tutorial se usa el [entorno de demostración de Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), que incluye una gran cantidad de datos de ejemplo que admiten las consultas de ejemplo. También puede usar su propia suscripción de Azure, pero es posible que no tenga datos en las mismas tablas.

## <a name="open-log-analytics"></a>Apertura de Log Analytics
Abra el [entorno de demostración de Log Analytics](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade) o seleccione **Registros** en el menú de Azure Monitor de su suscripción. En este paso se establecerá el ámbito inicial en un área de trabajo de Log Analytics, lo que significa que la consulta hará una selección entre todos los datos de esa área de trabajo. Si selecciona **Registros** en el menú de un recurso de Azure, el ámbito se establecerá solo en los registros de ese recurso. Para obtener más información sobre el ámbito, consulte [Ámbito de la consulta de registro](./scope.md).

Puede ver el ámbito en la esquina superior izquierda de la pantalla. Si usa su propio entorno, verá una opción para seleccionar un ámbito diferente. Esta opción no está disponible en el entorno de demostración.

:::image type="content" source="media/log-analytics-tutorial/scope.png" alt-text="Captura de pantalla que muestra el ámbito de Log Analytics para la demostración" lightbox="media/log-analytics-tutorial/scope.png":::.

## <a name="view-table-information"></a>Visualización de la información de la tabla
El lado izquierdo de la pantalla incluye la pestaña **Tablas**, donde puede inspeccionar las tablas disponibles en el ámbito actual. Estas tablas se agrupan por **Solución** de forma predeterminada, pero puede cambiar su agrupación o filtrarlas. 

Expanda la solución **Administración del registro** y localice la tabla **AppRequests**. Puede expandir la tabla para ver su esquema o mantener el puntero sobre su nombre para que se muestre información adicional sobre ella. 

:::image type="content" source="media/log-analytics-tutorial/table-details.png" alt-text="Captura de pantalla que muestra la vista de tablas." lightbox="media/log-analytics-tutorial/table-details.png":::

Seleccione el vínculo situado debajo de **Vínculos útiles** para ir a la referencia de tablas en la que se documentan todas las tablas y sus columnas. Seleccione **Vista previa de los datos** para echar un vistazo a algunos registros recientes de la tabla. Esta vista previa puede resultar útil para asegurarse de que se trata de los datos que espera antes de ejecutar una consulta en ellos.

:::image type="content" source="media/log-analytics-tutorial/sample-data.png" alt-text="Captura de pantalla que muestra datos de ejemplo." lightbox="media/log-analytics-tutorial/sample-data.png":::

## <a name="write-a-query"></a>Escriba una consulta.
Ahora escribirá una consulta mediante la tabla **AppRequests**. Haga doble clic en su nombre para agregarlo a la ventana de consulta. También puede escribir directamente en la ventana. También puede incluso obtener IntelliSense que le ayudará a completar los nombres de las tablas del ámbito actual y los comandos del lenguaje de consulta Kusto (KQL).

Esta es la consulta más sencilla que se puede escribir. Simplemente devuelve todos los registros de una tabla. Para ejecutarla, seleccione el botón **Ejecutar** o Mayús + ENTRAR con el cursor situado en cualquier parte del texto de la consulta.

:::image type="content" source="media/log-analytics-tutorial/query-results.png" alt-text="Captura de pantalla que muestra los resultados de la consulta." lightbox="media/log-analytics-tutorial/query-results.png":::

Puede ver que se han obtenido unos resultados. El número de registros devueltos por la consulta aparece en la esquina inferior derecha. 

## <a name="filter-query-results"></a>Filtrar los resultados de la consulta

Vamos a agregar un filtro a la consulta para reducir el número de registros que se devuelven. En el panel izquierdo, seleccione la pestaña **Filtrar**. En esta pestaña se muestran las columnas de los resultados de la consulta que puede usar para filtrar los resultados. Los valores superiores de esas columnas se muestran con el número de registros que tienen ese valor. Seleccione **200** en **ResultCode** y, después, **Aplicar y ejecutar**. 

:::image type="content" source="media/log-analytics-tutorial/query-pane.png" alt-text="Captura de pantalla que muestra el panel de consultas." lightbox="media/log-analytics-tutorial/query-pane.png":::

Se agrega una instrucción **where** a la consulta con el valor que seleccionó. Los resultados ahora incluyen solo los registros con ese valor para que pueda ver que el número de registros se reduce.

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-01.png" alt-text="Captura de pantalla que muestra los resultados de la consulta filtrados." lightbox="media/log-analytics-tutorial/query-results-filter-01.png":::


### <a name="time-range"></a>Intervalo de horas
Todas las tablas de un área de trabajo de Log Analytics tienen una columna denominada **TimeGenerated** que es la hora a la que se creó el registro. Todas las consultas tienen un intervalo de tiempo que limita los resultados a los registros que tienen un valor **TimeGenerated** que se encuentra dentro de ese intervalo. Puede definir el intervalo de tiempo en la consulta o mediante el selector de la parte superior de la pantalla.

De forma predeterminada, la consulta devolverá los registros de las últimas 24 horas. Debería ver un mensaje en el que se indica que no se ven todos los resultados. Esto se debe a que Log Analytics puede devolver un máximo de 30 000 registros y la consulta ha devuelto un número superior. Seleccione la lista desplegable **Intervalo de tiempo** y cambie el valor a **12 horas**. Seleccione **Ejecutar** de nuevo para que se devuelvan los resultados. 

:::image type="content" source="media/log-analytics-tutorial/query-results-max.png" alt-text="Captura de pantalla que muestra el intervalo de tiempo." lightbox="media/log-analytics-tutorial/query-results-max.png":::


### <a name="multiple-query-conditions"></a>Varias condiciones de consulta
Vamos a reducir aún más los resultados agregando otra condición de filtro. Una consulta puede incluir cualquier número de filtros para conseguir exactamente el conjunto de registros que desee. Seleccione **Get Home/Index** (Obtener inicio/índice) en **Name** (Nombre) y, a continuación, **Apply & Run** (Aplicar y ejecutar). 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter-02.png" alt-text="Captura de pantalla que muestra los resultados de la consulta con varios filtros." lightbox="media/log-analytics-tutorial/query-results-filter-02.png":::


## <a name="analyze-results"></a>Analizar resultados
Además de ayudarle a escribir y ejecutar consultas, Log Analytics proporciona características para trabajar con los resultados. Comience expandiendo un registro para ver los valores de todas sus columnas.

:::image type="content" source="media/log-analytics-tutorial/expand-record.png" alt-text="Captura de pantalla que muestra la expansión de un registro." lightbox="media/log-analytics-tutorial/expand-record.png":::

Seleccione el nombre de cualquier columna para ordenar los resultados por esa columna. Seleccione el icono de filtro situado junto a ella para proporcionar una condición de filtro. Esto es similar a agregar una condición de filtro a la propia consulta, salvo que este filtro se borra si se vuelve a ejecutar la consulta. Use este método si desea analizar rápidamente un conjunto de registros como parte del análisis interactivo.

Por ejemplo, establezca un filtro en la columna **DurationMs** para limitar los registros a los que hayan tardado más de **100** milisegundos. 

:::image type="content" source="media/log-analytics-tutorial/query-results-filter.png" alt-text="Captura de pantalla que muestra un filtro de resultados de consulta." lightbox="media/log-analytics-tutorial/query-results-filter.png":::

En lugar de filtrar los resultados, puede agrupar los registros por una columna determinada. Borre el filtro que acaba de crear y, a continuación, active el control **Agrupar columnas**. 

:::image type="content" source="media/log-analytics-tutorial/query-results-group-columns.png" alt-text="Captura de pantalla que muestra la activación de la agrupación de columnas." lightbox="media/log-analytics-tutorial/query-results-group-columns.png":::

Ahora, arrastre la columna **Url** a la fila de agrupación. Los resultados se organizan ahora por esa columna y puede contraer cada grupo para ayudarle con el análisis.

:::image type="content" source="media/log-analytics-tutorial/query-results-grouped.png" alt-text="Captura de pantalla que muestra los resultados de la consulta agrupados." lightbox="media/log-analytics-tutorial/query-results-grouped.png":::

## <a name="work-with-charts"></a>Uso de gráficos
Echemos un vistazo a una consulta que usa datos numéricos que se pueden ver en un gráfico. En lugar de crear una consulta, vamos a seleccionar una consulta de ejemplo.

En el panel izquierdo, seleccione **Consultas**. Este panel incluye consultas de ejemplo que se pueden agregar a la ventana de consultas. Si usa su propia área de trabajo, debería tener una variedad de consultas en varias categorías. Si usa el entorno de demostración, es posible que solo vea una única categoría de **Áreas de trabajo de Log Analytics**. Expanda esa categoría para ver las consultas que incluye.

Seleccione la consulta denominada **Tasa de errores de función** en la categoría **Aplicaciones**. Este paso agrega la consulta a la ventana de consultas. Observe que la nueva consulta está separada de la otra por una línea en blanco. Una consulta en KQL finaliza cuando encuentra una línea en blanco, por lo que estas se consideran consultas independientes. 

:::image type="content" source="media/log-analytics-tutorial/example-query.png" alt-text="Captura de pantalla que muestra una nueva consulta." lightbox="media/log-analytics-tutorial/example-query.png":::

La consulta actual es aquella en la que está situado el cursor. Puede ver que la primera consulta está resaltada, lo que indica que es la consulta actual. Haga clic en cualquier lugar de la nueva consulta para seleccionarla y, a continuación, seleccione el botón **Ejecutar** para ejecutarla.

:::image type="content" source="media/log-analytics-tutorial/example-query-output-table.png" alt-text="Captura de pantalla que muestra la tabla de resultados de la consulta." lightbox="media/log-analytics-tutorial/example-query-output-table.png":::

Para ver los resultados en un gráfico, seleccione **Gráfico** en el panel de resultados.  Observe que hay varias opciones para trabajar con el gráfico, como cambiar a otro tipo.

:::image type="content" source="media/log-analytics-tutorial/example-query-output-chart.png" alt-text="Captura de pantalla que muestra el gráfico de resultados de la consulta." lightbox="media/log-analytics-tutorial/example-query-output-chart.png":::


## <a name="next-steps"></a>Pasos siguientes

Ahora que sabe cómo usar Log Analytics, complete el tutorial sobre el uso de consultas de registro:
> [!div class="nextstepaction"]
> [Escritura de consultas de registros de Azure Monitor](get-started-queries.md)
