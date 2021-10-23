---
title: Visualizaciones de árbol en libros de Azure Monitor
description: Más información sobre todas las visualizaciones de árbol en libros de Azure Monitor.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: c2c15eaac298795bfb96466959a77d6840e80936
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131607"
---
# <a name="tree-visualizations"></a>Visualizaciones de árbol

Los libros admiten vistas jerárquicas a través de cuadrículas de árbol. Los árboles permiten expandir algunas filas en el siguiente nivel para obtener una experiencia de exploración en profundidad.

En el ejemplo siguiente se muestran las métricas de mantenimiento del contenedor (tamaño del espacio de trabajo) que se visualizan como una cuadrícula de árbol. Los nodos de nivel superior son nodos de Azure Kubernetes Service (AKS), los del siguiente nivel son pods y los del nivel final son contenedores. Observe que todavía puede dar formato a las columnas como en una cuadrícula (mapa térmico, iconos, vínculo). El origen de datos subyacente en este caso es un área de trabajo de Log Analytics con registros de AKS.

[![Captura de pantalla de la vista de un resumen de iconos](./media/workbooks-tree-visualizations/trees.png)](./media/workbooks-tree-visualizations/trees.png#lightbox)

## <a name="adding-a-tree-grid"></a>Incorporación de una cuadrícula de árbol
1. Para cambiar el libro al modo de edición, haga clic en el elemento de la barra de herramientas _Editar_.
2. Seleccione **Agregar** y *Agregar consulta* para agregar un control de consulta de registro al libro.
3. Seleccione el tipo de consulta **Registro**, el tipo de recurso (por ejemplo, Application Insights) y los recursos de destino.
4. Use el editor de consultas para especificar el KQL para el análisis.
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. Establezca la visualización a **Cuadrícula**
6. Seleccione el botón **Configuración de columna** para abrir el panel de configuración
7. En la sección **Configuración de Árbol/agrupar por** de la parte inferior, establezca:
    * Tipo de árbol: `Parent/Child`
    * Campo de identificador: `Id`
    * Campo de identificador primario: `ParentId`
    * Mostrar el expansor en: `Name`
    * Seleccione la casilla *Expandir el nivel superior del árbol*.
8. En la sección _Columnas_ en la parte superior, establezca:
    * _Identificador_: representador de columnas: `Hidden`
    * _Identificador_: representador de columnas: `Hidden`
    * _Solicitud_: representador de columnas: `Bar`; color: `Blue`; valor mínimo: `0`
9. Haga clic en el botón **Guardar y cerrar** de la parte inferior del panel.

[![Captura de pantalla de la vista de resumen de icono con la consulta y la configuración anteriores.](./media/workbooks-tree-visualizations/tree-settings.png)](./media/workbooks-tree-visualizations/tree-settings.png#lightbox)

## <a name="tree-settings"></a>Opciones del árbol

| Configuración | Explicación |
|:------------- |:-------------|
| `Id Field` | Identificador único de cada fila en la cuadrícula. |
| `Parent Id Field` | Identificador del elemento principal de la fila actual. |
| `Show the expander on` | La columna en la que se va a mostrar el expansor de árbol. Es habitual que las cuadrículas de árbol oculten el identificador y el campo de identificador principal porque no son muy legibles. En su lugar, el ampliador aparece en un campo con un valor más legible, como el nombre de la entidad. |
| `Expand the top level of the tree` | Si está activada, la cuadrícula de árbol se expandirá en el nivel superior. Resulta útil si desea mostrar más información de forma predeterminada. |

## <a name="grouping-in-a-grid"></a>Agrupación en una cuadrícula

La agrupación permite generar vistas jerárquicas similares a las anteriores con unas consultas significativamente más sencillas. Perderá la agregación en los nodos internos del árbol, pero esto es aceptable en algunos escenarios. Utilice *Agrupar por* para generar vistas de árbol cuando el conjunto de resultados subyacente no se pueda transformar en un formato libre adecuado, por ejemplo, datos de alertas, de mantenimiento y de métricas.

## <a name="adding-a-tree-using-grouping"></a>Incorporación de un árbol mediante la agrupación

1. Para cambiar el libro al modo de edición, haga clic en el elemento de la barra de herramientas _Editar_.
2. Seleccione **Agregar** y *Agregar consulta* para agregar un control de consulta de registro al libro.
3. Seleccione el tipo de consulta **Registro**, el tipo de recurso (por ejemplo, Application Insights) y los recursos de destino.
4. Use el editor de consultas para especificar el KQL para el análisis.
    ```kusto
    requests
    | summarize Requests = count() by App = appName, RequestName = name
    | order by Requests desc
    ```
1. Establezca la visualización en *Cuadrícula*.
2. Seleccione el botón **Configuración de columna** para abrir el panel de configuración.
3. En la sección *Configuración de Árbol/agrupar por* de la parte inferior, establezca:
    * Tipo de árbol: `Group By`
    * Agrupar por: `App`
    * Luego por: `None`
    * Seleccione la casilla *Expandir el nivel superior del árbol*.
4. En la sección *Columnas* en la parte superior, establezca:
    * *Aplicación* - representador de columnas: `Hidden`
    * *Solicitud*: representador de columnas: `Bar`; color: `Blue`; valor mínimo: `0`
5. Haga clic en el botón **Guardar y cerrar** de la parte inferior del panel.

[![Captura de pantalla que muestra la creación de una visualización de árbol en los libros](./media/workbooks-tree-visualizations/tree-group-create.png)](./media/workbooks-tree-visualizations/tree-group-create.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a crear un [grafo en libros](workbooks-graph-visualizations.md).
* Más información sobre cómo crear un [icono en los libros](workbooks-tile-visualizations.md).
