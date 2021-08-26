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
ms.date: 09/02/2020
ms.openlocfilehash: bf274ce49a2b83fca49bc039c1ba3c7980531979
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638186"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Administración del grafo de flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Los flujos de datos de asignación se crean mediante una superficie de diseño conocida como el grafo de flujo de datos. En el grafo, la lógica de transformación se crea de izquierda a derecha y los flujos de datos adicionales se agregan de arriba a abajo. Para agregar una nueva transformación, seleccione el signo más situado en la parte inferior derecha de una transformación existente.

![Lienzo](media/data-flow/canvas-2.png)

A medida que los flujos de datos se hagan más complejos, use los mecanismos siguientes para navegar y administrar eficazmente el grafo de flujo de datos. 

## <a name="moving-transformations"></a>Traslado de transformaciones

En los flujos de datos de asignación, un conjunto de lógica de transformación conectada se conoce como **flujo**. El campo **Incoming stream** (Flujo entrante) determina qué flujo de datos está alimentando la transformación actual. Cada transformación tiene uno o dos flujos entrantes según su función y representa un flujo de salida. El esquema de salida de los flujos entrantes determina los metadatos de columna a los que puede hacer referencia la transformación actual.

![Traslado de nodo](media/data-flow/move-nodes.png "trasladar nodo")

A diferencia del lienzo de canalización, las transformaciones de flujo de datos no se editan con un modelo de arrastrar y colocar. Para cambiar el flujo entrante o "trasladar" una transformación, elija otro valor en la lista desplegable **Incoming stream** (Flujo entrante). Al hacer esto, todas las transformaciones descendentes se trasladarán a lo largo de la transformación editada. El gráfico se actualizará automáticamente para mostrar el nuevo flujo lógico. Si cambia el flujo entrante a una transformación que ya tenga una transformación descendente, se creará una nueva rama o flujo de datos paralelo. Obtenga más información sobre [nuevas ramas en un flujo de datos de asignación](data-flow-new-branch.md).

## <a name="hide-graph-and-show-graph"></a>Ocultar gráfico y mostrar gráfico

Al editar la transformación, puede expandir el panel de configuración para ocupar todo el lienzo, ocultando el gráfico. Haga clic en el botón de contenido adicional orientado hacia arriba situado en el lado derecho del lienzo.

![Ocultar gráfico](media/data-flow/hide-graph.png "ocultar grafo")

Cuando el grafo está oculto, puede desplazarse por las transformaciones dentro de un flujo haciendo clic en **Siguiente** o **Anterior**. Haga clic en el botón de contenido adicional orientado hacia abajo para mostrar el grafo.

![Mostrar grafo](media/data-flow/show-graph.png "mostrar grafo")

## <a name="searching-for-transformations"></a>Búsqueda de transformaciones

Para encontrar rápidamente una transformación en el gráfico, haga clic en el icono **Búsqueda** situado sobre la configuración de zoom.

![Captura de pantalla que muestra el botón Buscar](media/data-flow/search-1.png "Buscar gráfico").

Puede buscar por nombre de transformación o descripción para encontrar una transformación.

![Captura de pantalla en la que se muestra el cuadro de texto de búsqueda](media/data-flow/search-2.png "Buscar gráfico").

## <a name="hide-reference-nodes"></a>Ocultación de nodos de referencia

Si el flujo de datos tiene cualquier transformación de combinación, búsqueda, existencia o unión, el flujo de datos muestra nodos de referencia a todos los flujos entrantes. Si desea minimizar la cantidad de espacio vertical utilizado, puede minimizar los nodos de referencia. Para ello, haga clic con el botón derecho en el lienzo y seleccione **Hide reference nodes** (Ocultar nodos de referencia).

![Ocultar nodos de referencia](media/data-flow/hide-reference-nodes.png "Ocultación de nodos de referencia")

## <a name="next-steps"></a>Pasos siguientes

Después de completar la lógica de flujo de datos, active el [modo de depuración](concepts-data-flow-debug-mode.md) y pruébelo en una vista previa de los datos.
