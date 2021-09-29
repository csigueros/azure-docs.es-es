---
title: 'Supervisión y administración de canalizaciones de datos: Azure'
description: Aprenda a usar la aplicación de supervisión y administración para supervisar y administrar factorías de datos y canalizaciones de Azure.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 76acbba66ccbeb18637ce0f181a702f8696148d6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591907"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Supervisión y administración de canalizaciones de Azure Data Factory mediante la aplicación de supervisión y administración
> [!div class="op_single_selector"]
> * [Uso de Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Uso de la aplicación de supervisión y administración](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte el artículo sobre la [supervisión y administración de las canalizaciones de Data Factory](../monitor-visually.md).

En este artículo se describe cómo usar la aplicación de supervisión y administración para supervisar, administrar y depurar las canalizaciones de Data Factory. Para empezar a usar la aplicación, vea el vídeo siguiente:

> [!NOTE]
> La interfaz de usuario que se muestra en el vídeo puede no coincidir exactamente con la que se ve en el portal. Es algo anterior, pero los conceptos siguen siendo los mismos. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Inicio de la aplicación de supervisión y administración
Para iniciar la aplicación de supervisión y administración, haga clic en el icono **Monitoring & Manage** (Supervisión y administración), de la hoja **Factoría de datos** de su factoría de datos.

:::image type="content" source="./media/data-factory-monitor-manage-app/MonitoringAppTile.png" alt-text="Icono de supervisión en la página de inicio de Factoría de datos":::

La aplicación de supervisión y administración debería abrirse en una ventana independiente.  

:::image type="content" source="./media/data-factory-monitor-manage-app/AppLaunched.png" alt-text="Aplicación de supervisión y administración":::

> [!NOTE]
> Si ve que el explorador web está atascado en "Autorizando...", desactive la casilla **Block third party cookies and site data** (Bloquear cookies y datos de sitios de terceros), o déjela activada y cree una excepción para **login.microsoftonline.com** e intente volver a abrir la aplicación.


En la lista Activity Windows (Ventanas de actividad) del panel central, verá una ventana de actividad cada vez que se ejecuta una actividad. Por ejemplo, si la actividad está programada para ejecutarse cada hora durante cinco horas, verá cinco ventanas de actividad asociadas a cinco segmentos de datos. Si no ve las ventanas de actividad en la parte inferior de la lista, haga lo siguiente:
 
- Actualice los filtros de **hora de inicio** y **hora de finalización** en la parte superior para que coincidan con las horas de inicio y finalización de la canalización y, después, haga clic en el botón **Aplicar**.  
- La lista Activity Windows (Ventanas de actividad) no se actualiza automáticamente. Haga clic en el botón **Refresh** (Actualizar) de la barra de herramientas de la lista **Activity Windows** (Ventanas de actividad).  

Si no tiene una aplicación de Data Factory con la que probar estos pasos, realice el tutorial: [Copia de datos de Blob Storage en SQL Database mediante Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Descripción de la aplicación de supervisión y administración
Hay tres pestañas a la izquierda: **Explorador de recursos**, **Vistas de supervisión de** y **Alertas**. La primera pestaña (**Explorador de recursos**) está activada de forma predeterminada.

### <a name="resource-explorer"></a>Explorador de recursos
Verá lo siguiente:

* La **vista de árbol** del Explorador de recursos en el panel izquierdo.
* El **vista de diagrama** en la parte superior del panel central.
* La lista **Activity Windows** (Ventanas de actividad) en la parte inferior del panel central.
* Las pestañas **Properties** (Propiedades), **Activity Window Explorer** (Explorador de ventanas de actividad) y **Script** en el panel derecho.

En el Explorador de recursos, aparecen todos los recursos (canalizaciones, conjuntos de datos, servicios vinculados) de la factoría de datos en una vista de árbol. Al seleccionar un objeto en el Explorador de recursos:

* Se resalta la entidad de Factoría de datos asociada en la vista de diagrama.
* Las [ventanas de actividad asociadas](data-factory-scheduling-and-execution.md) se resaltan en la lista Activity Windows (Ventanas de actividad) de la parte inferior.  
* Las propiedades del objeto seleccionado aparecen en la ventana Propiedades (panel derecho).
* Aparece la definición de JSON del objeto seleccionado, si procede. Por ejemplo: un servicio vinculado, un conjunto de datos o una canalización.

:::image type="content" source="./media/data-factory-monitor-manage-app/ResourceExplorer.png" alt-text="Explorador de recursos":::

Consulte el artículo sobre [programación y ejecución](data-factory-scheduling-and-execution.md) para información conceptual detallada sobre las ventanas de actividad.

### <a name="diagram-view"></a>Vista de diagrama
La vista de diagrama de una factoría de datos es un panel único para supervisar y administrar una factoría de datos y sus recursos. Cuando seleccione una entidad de Factoría de datos (conjunto de datos o canalización) en la vista de diagrama:

* La entidad de la factoría de datos se selecciona en la vista de árbol.
* Las ventanas de actividad asociadas se resaltan en la lista Activity Windows (Ventanas de actividad).
* Las propiedades del objeto seleccionado aparecen en la ventana Propiedades.

Si la canalización está habilitada (es decir, no en estado de pausa), se indicará con una línea verde:

:::image type="content" source="./media/data-factory-monitor-manage-app/PipelineRunning.png" alt-text="Canalización en ejecución":::

Para pausar, reanudar o terminar una canalización, selecciónela en la vista de diagrama y utilice los botones de la barra de comandos.

:::image type="content" source="./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png" alt-text="Pausa/reanudación en la barra de comandos":::
 
En la vista de diagrama hay tres botones en la barra de comandos para la canalización. Puede usar el segundo botón para pausar la canalización. La pausa no termina las actividades que se estén ejecutando, estas continúan hasta su finalización. El tercer botón pausa la canalización y termina las actividades que se están ejecutando. El primer botón reanuda la canalización. Cuando la canalización está en pausa, su color cambia. Por ejemplo, la siguiente imagen muestra una canalización en pausa: 

:::image type="content" source="./media/data-factory-monitor-manage-app/PipelinePaused.png" alt-text="Canalización en pausa":::

Con la tecla Ctrl puede realizar una selección múltiple de dos o más canalizaciones. Puede utilizar los botones de la barra de comandos para pausar y reanudar varias canalizaciones a la vez.

También puede hacer clic con el botón derecho en una canalización y seleccionar las opciones apropiadas para suspender, reanudar o terminar una canalización. 

:::image type="content" source="./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png" alt-text="Menú contextual de la canalización":::

Haga clic en la opción **Abrir canalización** para ver todas las actividades de la canalización. 

:::image type="content" source="./media/data-factory-monitor-manage-app/OpenPipelineMenu.png" alt-text="Menú Abrir canalización":::

En la vista de canalización que se abre, verá todas las actividades de la canalización. En este ejemplo, solamente hay una actividad: la actividad de copia. 

:::image type="content" source="./media/data-factory-monitor-manage-app/OpenedPipeline.png" alt-text="Canalización abierta":::

Para volver a la vista anterior, haga clic en el nombre de la factoría de datos en el menú de la ruta de navegación en la parte superior.

En la vista de canalización, cuando se selecciona un conjunto de datos de salida o cuando se pasa el mouse sobre él, se ve la ventana emergente Activity Windows (Ventanas de actividad) de dicho conjunto de datos.

:::image type="content" source="./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png" alt-text="Ventana emergente Activity Windows (Ventanas de actividad)":::

Puede hacer clic en una ventana de actividad para ver sus detalles en la ventana **Propiedades** del panel derecho.

:::image type="content" source="./media/data-factory-monitor-manage-app/ActivityWindowProperties.png" alt-text="Propiedades de la ventana de actividad":::

En el panel derecho, cambie a la pestaña **Activity Window Explorer** (Explorador de ventanas de actividad) para ver más información.

:::image type="content" source="./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png" alt-text="Captura de pantalla que muestra cómo obtener acceso a la pestaña del explorador de ventanas de actividad.":::

También verá las **variables resueltas** de cada intento de ejecución de una actividad en la sección **Intentos**.

:::image type="content" source="./media/data-factory-monitor-manage-app/ResolvedVariables.PNG" alt-text="Variables resueltas":::

Cambie a la pestaña **Script** para ver la definición del script de JSON del objeto seleccionado.   

:::image type="content" source="./media/data-factory-monitor-manage-app/ScriptTab.png" alt-text="Pestaña de script":::

Puede ver las ventanas de actividad en tres lugares:

* En el elemento emergente Activity Windows (Ventanas de actividad) en la vista de diagrama (panel central).
* En Activity Window Explorer (Explorador de ventanas de actividad) en el panel derecho.
* En la lista Activity Windows (Ventanas de actividad) en el panel inferior.

En el elemento emergente Activity Windows (Ventanas de actividad) y Activity Window Explorer (Explorador de ventanas de actividad) puede desplazarse a la semana anterior o siguiente con las flechas izquierda y derecha.

:::image type="content" source="./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png" alt-text="Flechas izquierda/derecha de Activity Window Explorer (Explorador de ventanas de actividad)":::

En la parte inferior de la vista de diagrama aparecen estos botones: acercar, alejar, ajustar, 100 % y bloquear el diseño. El botón para **bloquear el diseño** mover tablas y canalizaciones por error en la vista de diagrama. Está activado de forma predeterminada. Puede desactivarlo y mover las entidades por el diagrama. Si lo desactiva, puede usar el último botón para colocar automáticamente las tablas y las canalizaciones. También puede acercar o alejar con la rueda del mouse.

:::image type="content" source="./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png" alt-text="Comandos de ampliación de la vista de diagrama":::

### <a name="activity-windows-list"></a>Lista Activity Windows (Ventanas de actividad)
La lista Activity Windows (Ventanas de actividad) de la parte inferior del panel central muestra todas las ventanas de actividad del conjunto de datos seleccionado en el Explorador de recursos o en la vista de diagrama. De forma predeterminada, la lista está en orden descendente, lo que significa que en la parte superior aparece la ventana de actividad más reciente.

:::image type="content" source="./media/data-factory-monitor-manage-app/ActivityWindowsList.png" alt-text="Lista Activity Windows (Ventanas de actividad)":::

Esta lista no se actualiza automáticamente, por lo que debe usar el botón Actualizar de la barra de herramientas para hacerlo manualmente.  

Las ventanas de actividad pueden estar en uno de los siguientes estados:

<table>
<tr>
    <th align="left">Status</th><th align="left">Subestado</th><th align="left">Descripción</th>
</tr>
<tr>
    <td rowspan="8">En espera</td><td>ScheduleTime</td><td>Aún no es momento de ejecutar la ventana de actividad.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Las dependencias de nivel superior no están listas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>No están disponibles los recursos de proceso.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas las instancias de actividad están ocupadas con la ejecución de otras ventanas de actividad.</td>
</tr>
<tr>
<td>ActivityResume</td><td>La actividad está en pausa y no puede ejecutar las ventanas de actividad hasta que se reanude.</td>
</tr>
<tr>
<td>Reintento</td><td>Se está volviendo a intentar la ejecución de la actividad.</td>
</tr>
<tr>
<td>Validación</td><td>Aún no ha iniciado la validación.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>La validación está esperando un reintento.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Validating</td><td>Validación en curso.</td>
</tr>
<td>-</td>
<td>La ventana de actividad se está procesando.</td>
</tr>
<tr>
<td rowspan="4">Con error</td><td>TimedOut</td><td>La ejecución de la actividad tardó más tiempo del permitido por la actividad.</td>
</tr>
<tr>
<td>Canceled</td><td>La ventana de actividad fue cancelada por acción del usuario.</td>
</tr>
<tr>
<td>Validación</td><td>Error de validación.</td>
</tr>
<tr>
<td>-</td><td>No se pudo generar o validar la ventana de actividad.</td>
</tr>
<td>Ready</td><td>-</td><td>La ventana de actividad está lista para su uso.</td>
</tr>
<tr>
<td>Omitido</td><td>-</td><td>La ventana de actividad no se ha procesado.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Una ventana de actividad que existía con un estado distinto, pero que se ha restablecido.</td>
</tr>
</table>


Al hacer clic en una de las ventanas de actividad de la lista, verá sus detalles en **Activity Windows Explorer** (Explorador de ventanas de actividad) o en la ventana **Propiedades** de la derecha.

:::image type="content" source="./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png" alt-text="Captura de pantalla que muestra cómo ver los detalles de una ventana de actividad.":::

### <a name="refresh-activity-windows"></a>Actualizar las ventanas de actividad
Los detalles no se actualizan automáticamente, por lo que debe usar el segundo botón, Actualizar, de la barra de comandos para actualizar manualmente la lista de ventanas de actividad.  

### <a name="properties-window"></a>Ventana Propiedades
La ventana Propiedades está en el panel del extremo derecho de la Aplicación de supervisión y administración.

:::image type="content" source="./media/data-factory-monitor-manage-app/PropertiesWindow.png" alt-text="Ventana Propiedades":::

Muestra las propiedades del elemento seleccionado en el Explorador de recursos (en la vista de árbol), la vista de diagrama o la lista Activity Windows (Ventanas de actividad).

### <a name="activity-window-explorer"></a>Activity Window Explorer
La ventana **Activity Window Explorer** (Explorador de ventanas de actividad) se encuentra en el panel más a la derecha de la aplicación de supervisión y administración. Muestra detalles de la ventana de actividad seleccionada en la ventana emergente Activity Windows (Ventanas de actividad) o en la lista Activity Windows (Ventanas de actividad).

:::image type="content" source="./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png" alt-text="Activity Window Explorer":::

Puede cambiar a una ventana de actividad diferente haciendo clic en ella en la vista de calendario de la parte superior. También puede usar los botones de flecha izquierda/flecha derecha de la parte superior para ver las ventanas de actividad de la semana anterior o siguiente.

Puede usar los botones de la barra de herramientas del panel inferior para volver a ejecutar la ventana de actividad o actualizar los detalles del panel.

### <a name="script"></a>Script
Puede usar la pestaña **Script** para ver la definición de JSON de la entidad de Factoría de datos seleccionada (servicio vinculado, conjunto de datos o canalización).

:::image type="content" source="./media/data-factory-monitor-manage-app/ScriptTab.png" alt-text="Pestaña de script":::

## <a name="use-system-views"></a>Vistas de uso del sistema
La aplicación de supervisión y administración incluye vistas del sistema pregeneradas (**Recent activity windows** [Ventanas de actividad recientes], **Failed activity windows** [Ventanas de actividades con error] y **In-Progress activity windows** [Ventanas de actividad en curso]) que permiten ver las ventanas de actividad recientes, las que contienen errores o las que están en curso de su factoría de datos.

Cambie a la pestaña **Monitoring Views** (Vistas de supervisión) de la izquierda haciendo clic en ella.

:::image type="content" source="./media/data-factory-monitor-manage-app/MonitoringViewsTab.png" alt-text="Pestaña Vistas de supervisión":::

Actualmente, hay tres vistas del sistema compatibles. Seleccione una opción para ver las ventanas de actividad recientes, las ventanas de actividad con error o las ventanas de actividad en curso en la lista Activity Windows (Ventanas de actividad) (en la parte inferior del panel central).

Al seleccionar la opción **Recent activity windows** (Ventanas de actividad reciente), se ven todas las ventanas de actividad reciente en orden descendente de **hora del último intento**.

Puede usar la vista **Failed activity windows** (Ventanas de actividad con error) para ver todas las ventanas de actividad que contengan errores de la lista. Seleccione una ventana de actividad con error de la lista para ver sus detalles en la ventana **Propiedades** o en **Activity Window Explorer** (Explorador de ventanas de actividad). También puede descargar los registros de una ventana de actividad con error.

## <a name="sort-and-filter-activity-windows"></a>Ordenado y filtrado de las ventanas de actividad
Cambie la configuración de **hora de inicio** y **hora de finalización** en la barra de comandos para filtrar las ventanas de actividad. Después de cambiar la hora de inicio y de finalización, haga clic en el botón situado junto a la hora de finalización para actualizar la lista Activity Windows (Ventanas de actividad).

:::image type="content" source="./media/data-factory-monitor-manage-app/StartAndEndTimes.png" alt-text="Horas de inicio y finalización":::

> [!NOTE]
> Actualmente, en la aplicación de supervisión y administración todas las horas están en formato UTC.
>
>

En la **lista de ventanas de actividad**, haga clic en el nombre de una columna (por ejemplo, Estado).

:::image type="content" source="./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png" alt-text="Menú de columna de la lista Activity Windows (Ventanas de actividad)":::

Puede hacer lo siguiente:

* Ordenar en orden ascendente.
* Ordenar en orden descendente.
* Filtrar por uno o más valores (Ready [Listo], Waiting [En espera], etc.).

Cuando especifique un filtro en una columna, verá que el botón de filtro se habilita para esa columna, lo cual indica que los valores de la columna están filtrados.

:::image type="content" source="./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png" alt-text="Filtro de columna de la lista Activity Windows (Ventanas de actividad)":::

Puede usar la misma ventana emergente para borrar filtros. Para borrar todos los filtros de la lista Activity Windows (Ventanas de actividad), haga clic en el botón para borrar el filtro de la barra de comandos.

:::image type="content" source="./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png" alt-text="Borrado de todos los filtros de la lista Activity Windows (Ventanas de actividad)":::

## <a name="perform-batch-actions"></a>Acciones por lotes
### <a name="rerun-selected-activity-windows"></a>Volver a ejecutar las ventanas de actividad seleccionadas
Seleccione una ventana de actividad, haga clic en la flecha hacia abajo del primer botón de la barra de comandos y seleccione **Rerun** (Volver a ejecutar) / **Rerun with upstream in pipeline** (Volver a ejecutar con canal de subida en la canalización). Al seleccionar la opción **Rerun with upstream in pipeline** (Volver a ejecutar con canal de subida en la canalización), también se vuelven a ejecutar todas las ventanas de actividad con canal de subida.
    :::image type="content" source="./media/data-factory-monitor-manage-app/ReRunSlice.png" alt-text="Volver a ejecutar una ventana de actividad":::

También puede seleccionar varias ventanas de actividad en la lista y volver a ejecutarlas al mismo tiempo. Puede filtrar las ventanas de actividad según el estado (por ejemplo, **Failed** [Con error]) y volver a ejecutar las ventanas de actividad con error tras corregir el problema que provocaba el error en las ventanas de actividad. Vea la siguiente sección para obtener más información sobre el filtrado de ventanas de actividad en la lista.  

### <a name="pauseresume-multiple-pipelines"></a>Pausar y reanudar varias canalizaciones
También puede realizar una selección múltiple de dos o más canalizaciones con la tecla Ctrl. Puede utilizar los botones de la barra de comandos (que se resaltan en el rectángulo rojo en la imagen siguiente) para pausarlas o reanudarlas.

:::image type="content" source="./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png" alt-text="Pausa/reanudación en la barra de comandos":::
