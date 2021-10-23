---
title: Visualizaciones de grafos de libro de Azure Monitor
description: Más información sobre todas las visualizaciones de grafos de libro de Azure Monitor.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/04/2020
ms.openlocfilehash: 45902cbac3948787832238bbeb89090265464ab8
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129232"
---
# <a name="graph-visualizations"></a>Visualizaciones de grafos

Los libros admiten la visualización de gráficos arbitrarios basados en datos de registros para mostrar las relaciones entre las entidades de supervisión.

En el grafo siguiente se muestran los datos que fluyen dentro y fuera de un equipo a través de varios puertos hacia equipos externos y desde ellos. Está coloreado por tipo (equipo frente a puerto frente a dirección IP externa) y los tamaños de borde se corresponden con la cantidad de datos que fluyen dentro. Los datos subyacentes provienen de las conexiones de las máquinas virtuales de destino de consultas de KQL.

[![Captura de pantalla de la vista de un resumen de iconos](./media/workbooks-graph-visualizations/graph.png)](./media/workbooks-graph-visualizations/graph.png#lightbox)

## <a name="adding-a-graph"></a>Incorporación de un grafo
1. Para cambiar el libro al modo de edición, haga clic en el elemento de la barra de herramientas _Editar_.
2. Use el vínculo **Agregar consulta** para agregar un control de consulta de registro al libro.
3. Seleccione el tipo de consulta **Registro**, el tipo de recurso (por ejemplo, Application Insights) y los recursos de destino.
4. Use el editor de consultas para especificar el KQL para el análisis.

    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```

5. Establezca la visualización a **Gráfico**
6. Seleccione el botón **Configuración del grafo** para abrir el panel de configuración
7. En _Campos de diseño_, en la parte inferior, establezca:
    * Id. de nodo: `Id`
    * Id. de origen: `SourceId`
    * Id. de destino: `TargetId`
    * Etiqueta del borde: `None`
    * Tamaño del borde: `Calls`
    * Tamaño del nodo: `None`
    * Tipo de coloreado: `Categorical`
    * Campo de color del nodo: `Kind`
    * Paleta de colores: `Pastel`
8. En _Configuración de formato del nodo_, en la parte superior, establezca:
    * _Contenido principal_: columna de uso: `Name`; representador de columnas: `Text`
    * _Contenido del centro_: columna de uso: `Calls`; representador de columnas: `Big Number`; paleta de colores: `None`
    * _Contenido de la parte inferior_: columna de uso: `Kind`; representador de columnas: `Text`
9. Haga clic en el botón _Guardar y cerrar_ de la parte inferior del panel.

[![Captura de pantalla de la vista de resumen de icono con la consulta y la configuración anteriores.](./media/workbooks-graph-visualizations/graph-settings.png)](./media/workbooks-graph-visualizations/graph-settings.png#lightbox)

## <a name="graph-settings"></a>Configuración del grafo

| Configuración         | Explicación                                                                                                        |
|:----------------|:-------------------------------------------------------------------------------------------------------------------|
| `Node Id`       | Selecciona una columna que proporciona el identificador único de los nodos del grafo. El valor de la columna puede ser una cadena o un número. |
| `Source Id`     | Selecciona una columna que proporciona los identificadores de los nodos de origen para los bordes del grafo. Los valores deben asignarse a un valor de la columna _Id. de nodo_. |
| `Target Id`     | Selecciona una columna que proporciona los identificadores de los nodos de destino para los bordes del grafo. Los valores deben asignarse a un valor de la columna _Id. de nodo_. |
| `Edge Label`    | Selecciona una columna que proporciona etiquetas de los bordes del grafo.                                                            |
| `Edge Size`     | Selecciona una columna que proporciona la métrica en la que se basarán los anchos de borde.                                |
| `Node Size`     | Selecciona una columna que proporciona la métrica en la que se basarán las áreas de nodo.                                 |
| `Coloring Type` | Se usa para elegir el esquema de colores del nodo.                                                                            |

## <a name="node-coloring-types"></a>Tipos de coloreado de nodo

| Tipo de coloreado | Explicación |
|:------------- |:------------|
| `None`        | Todos los nodos tienen el mismo color. |
| `Categorical` | A los nodos se les asignan colores basados en el valor o la categoría de una columna del conjunto de resultados. En el ejemplo anterior, el coloreado se basa en la columna _Tipo_ del conjunto de resultados. Las paletas admitidas son `Default`, `Pastel` y `Cool tone`.  |
| `Field Based` | En este tipo, una columna proporciona valores RGB específicos que se usarán para el nodo. Ofrece la máxima flexibilidad, pero normalmente requiere un trabajo mayor para habilitarlo.  |

## <a name="node-format-settings"></a>Configuración de formato del nodo

Los autores de grafos pueden especificar el contenido que va a las distintas partes de un nodo: superior, izquierda, centro, derecha e inferior. Los grafos pueden utilizar cualquier soporte de libros de representadores (texto, numero grande, minigráficos, icono, etc.).

## <a name="field-based-node-coloring"></a>Coloreado del nodo basado en campos

1. Para cambiar el libro al modo de edición, haga clic en el elemento de la barra de herramientas _Editar_.
2. Use el vínculo **Agregar consulta** para agregar un control de consulta de registro al libro.
3. Seleccione el tipo de consulta **Registro**, el tipo de recurso (por ejemplo, Application Insights) y los recursos de destino.
4. Use el editor de consultas para especificar el KQL para el análisis.

     ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Color = 'FD7F23'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Color = 'B3DE8E')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Color = 'C9B3D5');
    nodes
    | union (links)
    ```
5. Establezca la visualización a *Gráfico*
6. Seleccione el botón **Configuración del grafo** para abrir el panel de configuración.
7. En *Campos de diseño*, en la parte inferior, establezca:
    * Id. de nodo:`Id`
    * Id. de origen: `SourceId`
    * Id. de destino: `TargetId`
    * Etiqueta del borde: `None`
    * Tamaño del borde: `Calls`
    * Tamaño del nodo: `Node`
    * Tipo de coloreado: `Field Based`
    * Campo de color del nodo: `Color`
8. En *Configuración de formato del nodo* en la parte superior, escriba lo siguiente.
    * En *Contenido en la parte superior*, establezca:
        * Usar columna: `Name`.
        * Representador de columnas: `Text`.
    * En *Contenido en el centro*, establezca:
        * Usar columna: `Calls`
        * Representador de columnas: `Big Number`
        * Paleta de colores: `None`
    * En *Contenido en la parte inferior*, establezca:
        * Usar columna: `Kind`
        * Representador de columnas: `Text`.
9. Haga clic en el botón **Guardar y cerrar** de la parte inferior del panel.

[![Captura de pantalla que muestra la creación de una visualización de grafo con el coloreado del nodo base de campo.](./media/workbooks-graph-visualizations/graph-field-based.png)](./media/workbooks-graph-visualizations/graph-field-based.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

* Los grafos también admiten el representador de la barra compuesta. Para más información, visite la [documentación de la barra compuesta](workbooks-composite-bar.md).
* Más información sobre los [orígenes de datos](workbooks-data-sources.md) que puede usar en los libros.
