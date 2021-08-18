---
title: Creación de particiones en la API de Gremlin de Azure Cosmos DB
description: Aprenda a usar Graph con particiones en Azure Cosmos DB. En este artículo también se describen los requisitos y procedimientos recomendados para un grafo con particiones.
author: manishmsfte
ms.author: mansha
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 4f7b6a3ce2963128f028f073310efef50ef96a42
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780808"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Uso de Graph con particiones en Azure Cosmos DB
[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Una de las características clave de la API de Gremlin de Azure Cosmos DB es la posibilidad de tratar grafos a gran escala con escalado horizontal. Los contenedores pueden escalarse independientemente en términos de almacenamiento y rendimiento. Puede crear contenedores en Azure Cosmos DB que pueden escalarse automáticamente para almacenar los datos de un grafo. Los datos se equilibrarán automáticamente según la **clave de partición** especificada.

La creación de particiones se realiza internamente si se espera que el contenedor almacene más de 20 GB de tamaño o se quieren asignar más de 10 000 unidades de solicitud por segundo (RU). Las particiones de datos se crean automáticamente en función de la clave de partición que se especifica. La clave de partición es necesaria si se crean contenedores de gráficos desde Azure Portal o desde las versiones 3.x o posteriores de los controladores Gremlin. La clave de partición no es necesaria si usa las versiones 2.x o anteriores de los controladores de Gremlin.

Se aplican los mismos principios generales del [mecanismo para crear particiones de Azure Cosmos DB](../partitioning-overview.md) con algunas optimizaciones específicas de Grafo que se describen a continuación.

:::image type="content" source="./media/graph-partitioning/graph-partitioning.png" alt-text="Creación de particiones de Graph." border="false":::

## <a name="graph-partitioning-mechanism"></a>Mecanismo de creación de particiones de Graph

Las instrucciones siguientes describen cómo funciona la estrategia de creación de particiones de Azure Cosmos DB:

- **Los vértices y los bordes se almacenan como documentos JSON**.

- Los **vértices requieren una clave de partición**. Esta clave determinará en qué partición se almacenará el vértice a través de un algoritmo hash. El nombre de la propiedad de clave de partición se define al crear otro contenedor y tiene el siguiente formato: `/partitioning-key-name`.

- Los **bordes se almacenarán con sus vértices de origen**. En otras palabras, para cada vértice, su clave de partición define dónde se almacena junto con sus bordes salientes. Esta optimización se hace para evitar consultas entre particiones cuando se usa la cardinalidad `out()` en las consultas de grafo.

- **Los bordes contienen referencias a los vértices a los que apuntan**. Todos los bordes se almacenan con las claves de partición y los identificadores de los vértices a los que apuntan. Este cálculo hace que todas las consultas de dirección `out()` sean siempre una consulta con particiones con ámbito y no una consulta ciega entre particiones.

- Las **consultas de grafo tienen que especificar una clave de partición**. Para aprovechar al máximo la partición horizontal en Azure Cosmos DB, la clave de partición debe especificarse al seleccionar un solo vértice, siempre que sea posible. Estas son las consultas para seleccionar uno o varios vértices en un grafo con particiones:

    - `/id` y `/label` no se admiten como claves de partición para un contenedor en la API de Gremlin.


    - Selección de un vértice por el identificador, **use el paso `.has()` para especificar la propiedad de clave de partición**:

        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```

    - Selección de un vértice, **especifique una tupla con valor de clave de partición e identificador**:

        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```

    - Especificación de una **matriz de tuplas de identificadores y valores de clave de partición**:

        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```

    - Selección de un conjunto de vértices con sus identificadores y **especificación de una lista de valores de clave de partición**:

        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Mediante la **estrategia de partición** al inicio de una consulta y la especificación de una partición para el ámbito del resto de la consulta de Gremlin:

        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Procedimientos recomendados con un grafo con particiones

Para garantizar la escalabilidad y el rendimiento al usar grafos con particiones con contenedores ilimitados, siga estas directrices:

- **Especifique siempre el valor de la clave de partición al consultar un vértice**. Obtener un vértice de una partición conocida es una forma de conseguir rendimiento. Todas las operaciones de adyacencia subsiguientes se limitarán siempre a una partición, porque los bordes contienen los identificadores de referencia y las claves de partición de sus vértices de destino.

- **Use la dirección saliente al consultar los bordes siempre que sea posible**. Tal y como se mencionó anteriormente, los bordes se almacenan con sus vértices de origen en la dirección saliente. Por lo que se minimizan las posibilidades de recurrir a las consultas entre particiones cuando los datos y las consultas están diseñados con este patrón en mente. Por el contrario, la consulta `in()` siempre será una consulta de distribución ramificada costosa.

- **Elija una clave de partición que distribuya los datos uniformemente por las particiones**. Esta decisión depende mucho el modelo de datos de la solución. Más información sobre la creación de una clave de partición apropiada en [Partición y escalado en Azure Cosmos DB](../partitioning-overview.md).

- **Optimice las consultas para obtener los datos dentro de los límites de una partición**. Una estrategia de partición óptima se alinea con los patrones de consulta. Las consultas que obtienen datos de una sola partición proporcionan el mejor rendimiento.

## <a name="next-steps"></a>Pasos siguientes

Ahora puede pasar a la lectura los artículos siguientes:

* Información acerca de la [Partición y escalado en Azure Cosmos DB](../partitioning-overview.md).
* Información acerca de la [compatibilidad de Gremlin en la API de Gremlin](gremlin-support.md).
* Información acerca de la [introducción a la API de Gremlin](graph-introduction.md).
