---
title: Use el perfil de ejecución para evaluar consultas en la API Gremlin de Azure Cosmos DB
description: Aprenda a solucionar problemas y mejorar sus consultas de Gremlin con el paso de perfil de ejecución.
services: cosmos-db
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 03/27/2019
author: manishmsfte
ms.author: mansha
ms.openlocfilehash: 2678fe832286e3d193ee923c0e6cdb1133dbbc29
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113354428"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>Uso del paso de perfil de ejecución para evaluar las consultas de Gremlin
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

En este artículo se proporciona información general sobre cómo usar el paso de perfil de ejecución para las bases de datos de grafos de Gremlin API de Azure Cosmos DB. Este paso proporciona información importante de solución de problemas y optimizaciones de consultas, y es compatible con cualquier consulta de Gremlin que se puede ejecutar en una cuenta de Gremlin API de Cosmos DB.

Para usar este paso, solo tiene que anexar la llamada de función `executionProfile()` al final de la consulta de Gremlin. **La consulta de Gremlin se ejecutará** y el resultado de la operación devolverá un objeto de respuesta JSON con el perfil de ejecución de consulta.

Por ejemplo:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Después de llamar al paso `executionProfile()`, la respuesta será un objeto JSON que incluye el paso de Gremlin ejecutado, el tiempo total que tardó y una matriz de los operadores de tiempo de ejecución de Cosmos DB resultado de la instrucción.

> [!NOTE]
> Esta implementación del perfil de ejecución no está definida en la especificación de Apache Tinkerpop. Es específica de la implementación de Gremlin API de Azure Cosmos DB.


## <a name="response-example"></a>Ejemplo de respuesta

Este es un ejemplo con anotaciones de la salida que se devuelve:

> [!NOTE]
> En este ejemplo se han anotado comentarios que explican la estructura general de la respuesta. Una respuesta executionProfile real no contiene ningún comentario.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> El paso executionProfile ejecutará la consulta de Gremlin. Aquí se incluyen los pasos `addV` o `addE`, que darán como resultado la creación y confirmación de los cambios especificados en la consulta. En consecuencia, también se cobrarán las unidades de solicitud generadas por la consulta de Gremlin.

## <a name="execution-profile-response-objects"></a>Objetos de respuesta de perfil de ejecución

La respuesta de una función executionProfile() producirá una jerarquía de objetos JSON con la estructura siguiente:
  - **Objeto de operaciones Gremlin**: representa la operación completa de Gremlin que se ejecutó. Contiene las siguientes propiedades:
    - `gremlin`: la instrucción de Gremlin explícita que se ejecutó.
    - `totalTime`: el tiempo, en milisegundos, empleado en la ejecución del paso. 
    - `metrics`: una matriz que contiene cada uno de los operadores de tiempo de ejecución de Cosmos DB que se ejecutaron para satisfacer la consulta. Esta lista se clasifica en orden de ejecución.
    
  - **Operadores del entorno de ejecución de Cosmos DB**: representa cada uno de los componentes de toda la operación de Gremlin. Esta lista se clasifica en orden de ejecución. Cada objeto contiene las siguientes propiedades:
    - `name`: nombre de la operación. Este es el tipo de paso que se evaluó y ejecutó. Lea más en la tabla siguiente.
    - `time`: cantidad de tiempo, en milisegundos, que tardó un operador determinado.
    - `annotations`: contiene información adicional específica del operador que se ejecutó.
    - `annotations.percentTime`: porcentaje del tiempo total que se tardó en ejecutar el operador específico.
    - `counts`: número de objetos que se devolvieron desde la capa de almacenamiento de este operador. Este valor está contenido en el valor escalar `counts.resultCount`.
    - `storeOps`: representa una operación de almacenamiento que puede abarcar una o varias particiones.
    - `storeOps.fanoutFactor`: representa el número de particiones a las que tuvo acceso esta operación de almacenamiento específica.
    - `storeOps.count`: representa el número de resultados que devolvió esta operación de almacenamiento.
    - `storeOps.size` :representa el tamaño en bytes del resultado de una operación de almacenamiento determinada.

Operador de tiempo de ejecución de Gremlin de Cosmos DB|Descripción
---|---
`GetVertices`| Este paso obtiene un conjunto declarado de objetos de la capa de persistencia. 
`GetEdges`| Este paso obtiene los bordes adyacentes a un conjunto de vértices. Este paso puede dar lugar a una o varias operaciones de almacenamiento.
`GetNeighborVertices`| Este paso obtiene los vértices que están conectados a un conjunto de bordes. Los bordes contienen las claves de partición y los identificadores tanto de los vértices de origen como de destino.
`Coalesce`| Este paso explica la evaluación de dos operaciones cada vez que se ejecuta el paso `coalesce()` de Gremlin.
`CartesianProductOperator`| Este paso calcula un producto cartesiano entre dos conjuntos de datos. Normalmente se ejecuta cada vez que se usan los predicados `to()` o `from()`.
`ConstantSourceOperator`| Este paso calcula una expresión para producir como resultado un valor constante.
`ProjectOperator`| Este paso se prepara y serializa una respuesta con el resultado de operaciones anteriores.
`ProjectAggregation`| Este paso prepara y serializa una respuesta para una operación de agregado.

> [!NOTE]
> Esta lista se seguirá actualizando a medida que se agreguen nuevos operadores.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Ejemplos de cómo analizar una respuesta de perfil de ejecución

Los siguientes son ejemplos de optimizaciones comunes que se pueden observar con la respuesta del perfil de ejecución:
  - Consulta de distribución ramificada ciega.
  - Consulta sin filtrar.

### <a name="blind-fan-out-query-patterns"></a>Patrones de consulta de distribución ramificada ciega

Suponga la siguiente respuesta del perfil de ejecución desde un **grafo con particiones**:

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Se pueden extraer de ella las siguientes conclusiones:
- La consulta es una búsqueda única de identificador, ya que la instrucción de Gremlin sigue el patrón `g.V('id')`.
- Por la métrica `time`, la latencia de esta consulta parece ser alta, ya que [una sola operación de lectura de puntos tarda más de 10 ms](./introduction.md#guaranteed-speed-at-any-scale).
- Si observamos el objeto `storeOps`, podemos ver que `fanoutFactor` es `5`, lo que significa que esta operación tuvo acceso a [5 particiones](./partitioning-overview.md).

La conclusión de este análisis es que podemos determinar que la primera consulta accede a particiones de las necesarias. Para solucionar esto, se puede especificar la clave de creación de particiones como un predicado. El resultado será una menor latencia y un costo más reducido por consulta. Más información sobre la [creación de particiones de grafos](graph-partitioning.md). Una consulta más óptima sería `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Patrones de consulta sin filtrar

Compare las siguientes dos respuestas de perfil de ejecución. Por motivos de simplicidad, en estos ejemplos se usa un único grafo con particiones.

Esta primera consulta recupera todos los vértices con la etiqueta `tweet` y, luego, obtiene sus vértices más próximos:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Observe el perfil de la misma consulta, pero ahora con un filtro adicional, `has('lang', 'en')`, antes de explorar los vértices adyacentes:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Estas dos consultas alcanzaron el mismo resultado; sin embargo, la primera de ellas requiere más unidades de solicitud dado que necesita recorrer en iteración un conjunto de datos inicial mayor antes de consultar los elementos adyacentes. Podemos ver indicadores de este comportamiento al comparar los siguientes parámetros de ambas respuestas:
- El valor `metrics[0].time` es mayor en la primera respuesta, lo que indica que este paso tardó más tiempo en resolverse.
- El valor `metrics[0].counts.resultsCount` es más alto también en la primera respuesta, lo que indica que el conjunto de datos de trabajo inicial era mayor.

## <a name="next-steps"></a>Pasos siguientes
* Aprenda sobre las [características de Gremlin admitidas](gremlin-support.md) en Azure Cosmos DB. 
* Aprenda sobre [Gremlin API en Azure Cosmos DB](graph-introduction.md).