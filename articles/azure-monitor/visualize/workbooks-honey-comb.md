---
title: Visualizaciones de panales de libro de Azure Monitor
description: Obtenga información sobre las visualizaciones de panales de libro de Azure Monitor.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 51941e9b8ef7165e3d5756d8c4fa9ed3520bc460
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132367"
---
# <a name="honey-comb-visualizations"></a>Visualizaciones de panales

Los panales permiten vistas de alta densidad de métricas o categorías que, de manera opcional, se pueden agrupar como clústeres. Son útiles para identificar visualmente las zonas activas y profundizar más.

En la imagen siguiente se muestra el uso de CPU de las máquinas virtuales en dos suscripciones. Cada celda representa una máquina virtual y el color o la etiqueta representan el uso promedio de la CPU (los rojos son equipos de acceso frecuente). Las máquinas virtuales están agrupadas por suscripción.

[![Captura de pantalla que muestra el uso de CPU de las máquinas virtuales en dos suscripciones](.\media\workbooks-honey-comb\cpu-example.png)](.\media\workbooks-honey-comb\cpu-example.png#lightbox)

## <a name="adding-a-honey-comb"></a>Adición de un panal

1. Para cambiar el libro al modo de edición, haga clic en el elemento de la barra de herramientas Editar.
2. Use **Agregar** de la parte inferior y, a continuación, *Agregar consulta* para agregar un control de consulta de registro al libro.
3. Seleccione "Registros" como *Origen de datos*, "Log Analytics" como *Tipo de recurso* y, para *Recurso*, apunte a un área de trabajo que tenga el registro de rendimiento de la máquina virtual.
4. Use el editor de consultas para especificar el KQL para el análisis.

```kusto
    Perf
| where CounterName == 'Available MBytes'
| summarize CounterValue = avg(CounterValue) by Computer, _ResourceId
| extend ResourceGroup = extract(@'/subscriptions/.+/resourcegroups/(.+)/providers/microsoft.compute/virtualmachines/.+', 1, _ResourceId)
| extend ResourceGroup = iff(ResourceGroup == '', 'On-premise computers', ResourceGroup), Id = strcat(_ResourceId, '::', Computer)
```

5. Ejecute una consulta.
6. Establezca la *visualización* en "Gráfico".
7. Seleccione **Configuración del gráfico**.
    1. En *Campos de diseño*, en la parte inferior, establezca:
        1. Tipo de gráfico: **Clústeres de Hive**.
        2. Id. de nodo: `Id`.
        3. Agrupar por: `None`.
        4. Tamaño del nodo: 100.
        5. Margen entre hexágonos: `5`.
        6. Tipo de coloreado: **Mapa térmico**.
        7. Campo de color del nodo: `CouterValue`.
        8. Paleta de colores: `Red to Green`.
        9. Valor mínimo: `100`.
        10. Valor máximo: `2000`.
    2. En *Configuración de formato del nodo*, en la parte superior, establezca:
        1. Contenido en la parte superior:
            1. Usar columna: `Computer`.
            2. Representador de columnas: `Text`.
        9. Contenido en el centro:
            1. Usar columna: `CounterValue`.
            2. Representador de columnas: `Big Number`.
            3. Paleta de colores: `None`.
            4. Active la casilla *Formato de número personalizado*.
            5. Unidades: `Megabytes`.
            6. Máximo de dígitos fraccionarios: `1`.
8. Seleccione el botón **Guardar y cerrar** de la parte inferior del panel.

[![Captura de pantalla del control de consulta, la configuración del gráfico y el panal con la consulta y la configuración anteriores](.\media\workbooks-honey-comb\available-memory.png)](.\media\workbooks-honey-comb\available-memory.png#lightbox)

## <a name="honey-comb-layout-settings"></a>Configuración del diseño del panal

| Configuración | Explicación |
|:------------- |:-------------|
| `Node Id` | Selecciona una columna que proporciona el identificador único de los nodos. El valor de la columna puede ser una cadena o un número. |
| `Group By Field` | Seleccione la columna en la que se van a agrupar los nodos. |
| `Node Size` | Establece el tamaño de las celdas hexagonales. Úselo con la propiedad `Margin between hexagons` para personalizar la apariencia del gráfico de panal. |
| `Margin between hexagons` | Establece la separación entre las celdas hexagonales. Úselo con la propiedad `Node size` para personalizar la apariencia del gráfico de panal. |
| `Coloring type` | Selecciona el esquema que se va a usar para colorear el nodo. |
| `Node Color Field` | Selecciona una columna que proporciona la métrica en la que se basarán las áreas de nodo. |

## <a name="node-coloring-types"></a>Tipos de coloreado de nodo

| Tipo de coloreado | Explicación |
|:------------- |:-------------|
| `None` | Todos los nodos tienen el mismo color. |
| `Categorical` | A los nodos se les asignan colores basados en el valor o la categoría de una columna del conjunto de resultados. En el ejemplo anterior, el coloreado se basa en la columna _Tipo_ del conjunto de resultados. Las paletas admitidas son `Default`, `Pastel` y `Cool tone`.  |
| `Heatmap` | En este tipo, las celdas se colorean en función de una columna de métrica y una paleta de colores. Esto proporciona una manera sencilla de resaltar las métricas en las celdas. |
| `Thresholds` | En este tipo, los colores de las celdas se establecen mediante reglas de umbral (por ejemplo, _CPU > 90 % => rojo, 60 % > CPU > 90 % => amarillo, CPU < 60 % => verde_) |
| `Field Based` | En este tipo, una columna proporciona valores RGB específicos que se usarán para el nodo. Ofrece la máxima flexibilidad, pero normalmente requiere un trabajo mayor para habilitarlo.  |
      
## <a name="node-format-settings"></a>Configuración de formato del nodo

Los autores de panales pueden especificar el contenido que va a las distintas partes de un nodo: superior, izquierda, centro, derecha e inferior. Los autores pueden utilizar cualquier soporte de libros de representadores (texto, numero grande, minigráficos, icono, etc.).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo crear un [representador de la barra compuesta en los libros](workbooks-composite-bar.md).
- Obtenga información sobre cómo [importar datos de registro de Azure Monitor en Power BI](./powerbi.md).