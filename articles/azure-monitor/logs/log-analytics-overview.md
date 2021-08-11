---
title: Introducción a Log Analytics en Azure Monitor
description: Describe Log Analytics, que es una herramienta de Azure Portal que se usa para editar y ejecutar consultas de registros para analizar los datos de los registros de Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/04/2020
ms.openlocfilehash: 860085cc447b241f622c2be89038bca0014a883e
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110493417"
---
# <a name="overview-of-log-analytics-in-azure-monitor"></a>Introducción a Log Analytics en Azure Monitor
Log Analytics es una herramienta de Azure Portal que se usa para editar y ejecutar consultas de registros con los datos de los registros de Azure Monitor. Puede escribir una consulta sencilla que devuelva un conjunto de registros y, a continuación, utilizar las características de Log Analytics para ordenarlos, filtrarlos y analizarlos. O bien, puede escribir una consulta más avanzada para realizar análisis estadísticos y visualizar los resultados en un gráfico para identificar una tendencia determinada. Tanto si trabaja con los resultados de las consultas de forma interactiva como si los usa con otras características de Azure Monitor, como las alertas de consultas de registros o los libros, Log Analytics es la herramienta que va a usar para escribir y probarlos. 


> [!TIP]
> En este artículo se proporciona una descripción de Log Analytics y de cada una de sus características. Si quiere ir directamente a un tutorial, consulte [Tutorial de Log Analytics](./log-analytics-tutorial.md).



## <a name="starting-log-analytics"></a>Inicio de Log Analytics
Abra Log Analytics desde **Registros** en el menú **Azure Monitor** de Azure Portal. También verá esta opción en el menú para la mayoría de los recursos de Azure. Independientemente del lugar desde el que la inicie, será la misma herramienta de Log Analytics. No obstante, el menú que se usa para iniciar Log Analytics determina los datos que estarán disponibles. Si lo inicia desde el menú de **Azure Monitor** o el menú de las **áreas de trabajo de Log Analytics**, tendrá acceso a todos los registros de un área de trabajo. Si selecciona **Registros** desde otro tipo de recurso, los datos se limitarán a los datos de registro de ese recurso. Consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](./scope.md) para obtener más información.

[![Inicio de Log Analytics](media/log-analytics-overview/start-log-analytics.png)](media/log-analytics-overview/start-log-analytics.png#lightbox)

Al iniciar Log Analytics, lo primero que verá es un cuadro de diálogo con [consultas de ejemplo](../logs/queries.md). Están clasificadas por solución y puede examinar o buscar consultas que coincidan con sus requisitos particulares. Es posible que pueda encontrar una que haga exactamente lo que necesita o bien puede cargar una en el editor y modificarla según sea necesario. Examinar las consultas de ejemplo es realmente una excelente manera de aprender a escribir sus propias consultas. 

Por supuesto, si desea empezar con un script vacío y escribirlo usted mismo, puede cerrar las consultas de ejemplo. Basta con hacer clic en **Consultas** en la parte superior de la pantalla si desea volver a tenerlas.

## <a name="log-analytics-interface"></a>Interfaz de Log Analytics
En la imagen siguiente se identifican los distintos componentes de Log Analytics.

[![Log Analytics](media/log-analytics-overview/log-analytics.png)](media/log-analytics-overview/log-analytics.png#lightbox)

### <a name="1-top-action-bar"></a>1. Barra de acciones superior
Controles para trabajar con la consulta en la ventana de consulta.

| Opción | Descripción |
|:---|:---|
| Ámbito | Especifica el ámbito de los datos utilizados para la consulta. Puede tratarse de todos los datos de un área de trabajo Log Analytics o de los datos de un recurso determinado en varias áreas de trabajo. Consulte el apartado [Ámbito de la consulta](./scope.md). |
| Botón Ejecutar | Haga clic en esta opción para ejecutar la consulta seleccionada en la ventana de consulta. Para ejecutar una consulta, también puede presionar Mayús + Entrar. |
| Selector de hora | Seleccione el intervalo de tiempo para los datos disponibles para la consulta. Esto se invalida si incluye un filtro de tiempo en la consulta. Consulte [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](./scope.md). |
| Botón Guardar | Guarde la consulta en el Explorador de consultas del área de trabajo. |
 botón Copiar | Copie en el Portapapeles un vínculo a la consulta, el texto de la consulta o los resultados de la consulta. |
| Botón Nueva regla de alertas | Cree una nueva pestaña con una consulta vacía. |
| Botón Exportar | Exporte los resultados de la consulta a un archivo CSV o la consulta al formato de lenguaje de fórmulas de Power Query para su uso con Power BI. |
| Botón Anclar al panel | Agregue los resultados de la consulta a un panel de Azure. |
| Botón Dar formato a consulta | Organice el texto seleccionado para facilitar la lectura. |
| Botón Consultas de ejemplo | Abra el cuadro de diálogo de consultas de ejemplo que se muestra cuando se abre por primera vez Log Analytics. |
| Botón Explorador de consultas | Abra el **Explorador de consultas**, que proporciona acceso a las consultas guardadas en el área de trabajo. |


### <a name="2-sidebar"></a>2. Barra lateral
Listas de tablas del área de trabajo, consultas de ejemplo y opciones de filtro para la consulta actual.

| Pestaña | Descripción |
|:---|:---|
| Tablas | Muestra las tablas que forman parte del ámbito seleccionado. Seleccione **Agrupar por** para cambiar la agrupación de las tablas. Mantenga el puntero sobre un nombre de tabla para mostrar un cuadro de diálogo con una descripción de la tabla y las opciones para ver su documentación y obtener una vista previa de sus datos. Expanda una tabla para ver sus columnas. Haga doble clic en el nombre de una tabla o columna para agregarla a la consulta. |
| Consultas | Lista de consultas de ejemplo que se pueden abrir en la ventana de consulta. Esta es la misma lista que se muestra al abrir Log Analytics. Seleccione **Agrupar por** para cambiar la agrupación de las consultas. Haga doble clic en una consulta para agregarla a la ventana de consulta o mantenga el puntero sobre ella para otras opciones. |
| Filter | Crea opciones de filtro basadas en los resultados de una consulta. Después de ejecutar una consulta, se mostrarán las columnas con valores diferentes de los resultados. Seleccione uno o más valores y, a continuación, haga clic en **Aplicar y ejecutar** para agregar un comando **where** a la consulta y ejecutarla de nuevo. |

### <a name="3-query-window"></a>3. Ventana de consulta
La ventana de consulta es donde se edita la consulta. Esto incluye IntelliSense para los comandos de KQL y un código de color para mejorar la legibilidad. Haga clic en **+** en la parte superior de la ventana para abrir otra pestaña.

Una única ventana puede incluir varias consultas. Una consulta no puede incluir líneas en blanco, por lo que puede separar varias consultas en una ventana con una o varias líneas en blanco. La consulta actual es la que tiene el cursor colocado en cualquier parte de ella.

Para ejecutar la consulta actual, haga clic en el botón **Ejecutar** o presione Mayús + Entrar.

### <a name="4-results-window"></a>4. Ventana Resultados
Los resultados de la consulta se muestran en la ventana Resultados. De forma predeterminada, los resultados se muestran como una tabla. Para mostrarlos como un gráfico, seleccione **Gráfico** en la ventana Resultados o agregue un comando **render** a la consulta.

#### <a name="results-view"></a>vista de resultados
Muestra los resultados de la consulta en una tabla organizada por columnas y filas. Haga clic a la izquierda de una fila para expandir sus valores. Haga clic en la lista desplegable **Columnas** para cambiar la lista de columnas. Haga clic en el nombre de una columna para ordenar los resultados. Haga clic en el embudo situado junto a un nombre de columna para filtrar los resultados. Ejecute de nuevo la consulta para borrar los filtros y restablecer la ordenación.

Seleccione **Agrupar columnas** para mostrar la barra de agrupación sobre los resultados de la consulta. Agrupe los resultados por cualquier columna arrastrándolos a la barra. Agregue columnas adicionales para crear grupos anidados en los resultados. 

#### <a name="chart-view"></a>Vista de diagrama
Muestra los resultados como uno de los varios tipos de gráficos disponibles. Puede especificar el tipo de gráfico en un comando **render** en la consulta o seleccionarlo en la lista desplegable **Tipo de visualización**.

| Opción | Descripción |
|:---|:---|
| **Tipo de visualización** | Tipo de gráfico que se va a mostrar. |
| **Eje X** | Columna de los resultados que se va a usar para el eje X. 
| **Eje Y** | Columna de los resultados que se va a usar para el eje Y. Normalmente será una columna numérica. |
| **Dividir por** | Columna de los resultados que define la serie del gráfico. Se crea una serie para cada valor de la columna. |
| **Agregación** | Tipo de agregación que se va a realizar en los valores numéricos del eje Y. |

## <a name="relationship-to-azure-data-explorer"></a>Relación con Azure Data Explorer
Si ya está familiarizado con la interfaz de usuario web de Azure Data Explorer, Log Analytics debe resultarle familiar. Se debe a que se basa en Azure Data Explorer y usa el mismo lenguaje de consulta Kusto (KQL). Log Analytics agrega características específicas a Azure Monitor, como el filtrado por intervalo de tiempo y la capacidad de crear una regla de alertas a partir de una consulta. Ambas herramientas incluyen un explorador que le permite examinar la estructura de las tablas disponibles, pero la interfaz de usuario web de Azure Data Explorer trabaja principalmente con tablas de bases de datos de Azure Data Explorer mientras que Log Analytics trabaja con tablas de un área de trabajo de Log Analytics. 

## <a name="next-steps"></a>Pasos siguientes
- Realice un [tutorial sobre el uso de Log Analytics en Azure Portal](./log-analytics-tutorial.md).
- Realice un [tutorial sobre cómo escribir consultas](./get-started-queries.md).
