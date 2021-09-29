---
title: Administración del grafo de flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Cómo administrar y editar de forma eficaz el grafo de flujo de datos de asignación
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: da9b3148506e437f7bf5d90fc0fbe44e0a2d27f0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812101"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Administración del grafo de flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Los flujos de datos de asignación se crean mediante una superficie de diseño conocida como el grafo de flujo de datos. En el grafo, la lógica de transformación se crea de izquierda a derecha y los flujos de datos adicionales se agregan de arriba a abajo. Para agregar una nueva transformación, seleccione el signo más situado en la parte inferior derecha de una transformación existente.

:::image type="content" source="media/data-flow/canvas-2.png" alt-text="Lienzo":::

A medida que los flujos de datos se hagan más complejos, use los mecanismos siguientes para navegar y administrar eficazmente el grafo de flujo de datos. 

## <a name="moving-transformations"></a>Traslado de transformaciones

En los flujos de datos de asignación, un conjunto de lógica de transformación conectada se conoce como **flujo**. El campo **Incoming stream** (Flujo entrante) determina qué flujo de datos está alimentando la transformación actual. Cada transformación tiene uno o dos flujos entrantes según su función y representa un flujo de salida. El esquema de salida de los flujos entrantes determina los metadatos de columna a los que puede hacer referencia la transformación actual.

:::image type="content" source="media/data-flow/move-nodes.png" alt-text="Traslado de nodo":::

A diferencia del lienzo de canalización, las transformaciones de flujo de datos no se editan con un modelo de arrastrar y colocar. Para cambiar el flujo entrante o "trasladar" una transformación, elija otro valor en la lista desplegable **Incoming stream** (Flujo entrante). Al hacer esto, todas las transformaciones descendentes se trasladarán a lo largo de la transformación editada. El gráfico se actualizará automáticamente para mostrar el nuevo flujo lógico. Si cambia el flujo entrante a una transformación que ya tenga una transformación descendente, se creará una nueva rama o flujo de datos paralelo. Obtenga más información sobre [nuevas ramas en un flujo de datos de asignación](data-flow-new-branch.md).

## <a name="hide-graph-and-show-graph"></a>Ocultar gráfico y mostrar gráfico

Al editar la transformación, puede expandir el panel de configuración para ocupar todo el lienzo, ocultando el gráfico. Haga clic en el botón de contenido adicional orientado hacia arriba situado en el lado derecho del lienzo.

:::image type="content" source="media/data-flow/hide-graph.png" alt-text="Ocultar gráfico":::

Cuando el grafo está oculto, puede desplazarse por las transformaciones dentro de un flujo haciendo clic en **Siguiente** o **Anterior**. Haga clic en el botón de contenido adicional orientado hacia abajo para mostrar el grafo.

:::image type="content" source="media/data-flow/show-graph.png" alt-text="Mostrar grafo":::

## <a name="searching-for-transformations"></a>Búsqueda de transformaciones

Para encontrar rápidamente una transformación en el gráfico, haga clic en el icono **Búsqueda** situado sobre la configuración de zoom.

:::image type="content" source="media/data-flow/search-1.png" alt-text="Captura de pantalla que muestra el botón Buscar":::.

Puede buscar por nombre de transformación o descripción para encontrar una transformación.

:::image type="content" source="media/data-flow/search-2.png" alt-text="Captura de pantalla en la que se muestra el cuadro de texto de búsqueda":::.

## <a name="hide-reference-nodes"></a>Ocultación de nodos de referencia

Si el flujo de datos tiene cualquier transformación de combinación, búsqueda, existencia o unión, el flujo de datos muestra nodos de referencia a todos los flujos entrantes. Si desea minimizar la cantidad de espacio vertical utilizado, puede minimizar los nodos de referencia. Para ello, haga clic con el botón derecho en el lienzo y seleccione **Hide reference nodes** (Ocultar nodos de referencia).

:::image type="content" source="media/data-flow/hide-reference-nodes.png" alt-text="Ocultar nodos de referencia":::

## <a name="next-steps"></a>Pasos siguientes

Después de completar la lógica de flujo de datos, active el [modo de depuración](concepts-data-flow-debug-mode.md) y pruébelo en una vista previa de los datos.
