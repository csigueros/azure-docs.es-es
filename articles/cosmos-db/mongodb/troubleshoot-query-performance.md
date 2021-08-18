---
title: Solución de problemas de consultas al usar la API para MongoDB de Azure Cosmos DB
description: Obtenga información sobre cómo identificar, diagnosticar y solucionar problemas de consultas de la API para MongoDB de Azure Cosmos DB.
ms.service: cosmos-db
ms.topic: troubleshooting
ms.subservice: cosmosdb-mongo
ms.date: 03/02/2021
author: gahl-levy
ms.author: gahllevy
ms.reviewer: sngun
ms.openlocfilehash: 30e9623a63f0342f6800ffbb6b3534989d02ec02
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780310"
---
# <a name="troubleshoot-query-issues-when-using-the-azure-cosmos-db-api-for-mongodb"></a>Solución de problemas de consultas al usar la API para MongoDB de Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

Este artículo le guía por un enfoque recomendado general para la solución de problemas con las consultas en Azure Cosmos DB. Aunque no debe tener en cuenta los pasos descritos en este artículo como una defensa completa contra posibles problemas de consulta, hemos incluido aquí las sugerencias de rendimiento más comunes. Use este artículo como punto de partida para la solución de problemas con las consultas lentas o costosas en la API para MongoDB de Azure Cosmos DB. Si usa la API del núcleo de Azure Cosmos DB Core (SQL), consulte la [guía de solución de problemas de consultas de la API de SQL](troubleshoot-query-performance.md).

Las categorías de las optimizaciones de consulta en Azure Cosmos DB son las siguientes:

- Optimizaciones que reducen el cargo por unidades de solicitud (RU) de la consulta
- Optimizaciones que solo reducen la latencia

Al reducir el cargo por RU de una consulta, normalmente se reducirá la latencia.

En este artículo se ofrecen ejemplos que se pueden volver a crear mediante el conjunto de datos [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

> [!NOTE] 
> En este artículo se da por supuesto que está usando la cuenta de API de Azure Cosmos DB para MongoDB con la versión 3.6 y versiones posteriores. Algunas consultas que funcionan de manera deficiente en la versión 3.2 han mejorado significativamente en la versión 3.6. Para actualizar a la versión 3.6, presente una [solicitud de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="use-explain-command-to-get-metrics"></a>Uso del comando $explain para obtener métricas

Al optimizar una consulta en Azure Cosmos DB, el primer paso es siempre [obtener el cargo por RU](find-request-unit-charge-mongodb.md) de la consulta. Como directriz aproximada, debe explorar las maneras de reducir el cargo por RU para consultas con cargos mayores que 50 RU. 

Además de obtener el cargo por RU, debe usar el comando `$explain` para obtener las métricas de uso de las consultas y los índices. Este es un ejemplo en el que se ejecuta una consulta y se usa el comando `$explain` para mostrar las métricas de uso de las consultas y los índices:

**Comando $explain:**

```
db.coll.find({foodGroup: "Baby Foods"}).explain({"executionStatistics": true })
```

**Salida:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 905.2888,
            "timeExclusiveMS" : 905.2888,
            "in" : 362,
            "out" : 362,
            "details" : {
                "database" : "db-test",
                "collection" : "collection-test",
                "query" : {
                    "foodGroup" : {
                        "$eq" : "Baby Foods"
                    }
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "e68e6bdd-5e89-4ec5-b053-3dbbc2428140",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 788.5867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 362,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 362,
                    "outputDocumentSizeBytes" : 2553535,
                    "indexHitRatio" : 0.0016802042237178,
                    "totalQueryExecutionTimeMS" : 777.72,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.19,
                        "logicalPlanBuildTimeMS" : 0.14,
                        "physicalPlanBuildTimeMS" : 0.09,
                        "queryOptimizationTimeMS" : 0.03
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 687.22,
                    "vmExecutionTimeMS" : 774.09,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 37.45,
                        "systemFunctionExecutionTimeMS" : 10.82,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 49.42
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

La salida del comando `$explain` es larga y contiene información detallada acerca de la ejecución de la consulta. Sin embargo, en general, hay algunas secciones en las que se debe centrar al optimizar el rendimiento de las consultas:

| Métrica | Descripción | 
| ------ | ----------- |
| `timeInclusiveMS` | Latencia de consulta de back-end. |
| `pathsIndexed` | Muestra los índices usados por la consulta. | 
| `pathsNotIndexed` | Muestra los índices que la consulta podría haber usado, si están disponibles. | 
| `shardInformation` | Resumen del rendimiento de las consultas de una [partición física](../partitioning-overview.md#physical-partitions) determinada. | 
| `retrievedDocumentCount` | Número de documentos cargados por el motor de consultas. | 
| `outputDocumentCount` | Número de documentos devueltos en los resultados de la consulta. | 
| `estimatedDelayFromRateLimitingInMilliseconds` | Latencia de consulta adicional estimada debido a la limitación de velocidad. | 

Después de obtener las métricas de consulta, compare el `retrievedDocumentCount` con el de `outputDocumentCount` de la consulta. Use esta comparación para identificar las secciones pertinentes a las que se hará referencia en este artículo. `retrievedDocumentCount` es el número de documentos que el motor de consulta necesita cargar. El `outputDocumentCount` es el número de documentos necesarios para los resultados de la consulta. Si `retrievedDocumentCount` es significativamente mayor que `outputDocumentCount`, al menos una parte de la consulta no pudo usar el índice y tuvo que realizar un examen.

Consulte las secciones siguientes para entender las optimizaciones de consulta pertinentes para su escenario.

### <a name="querys-ru-charge-is-too-high"></a>El cargo por RU de la consulta es demasiado elevado

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>El recuento de documentos recuperados es significativamente mayor que el de documentos de salida

- [Incluya los índices necesarios.](#include-necessary-indexes)

- [Obtenga información de qué operaciones de agregación usan el índice.](#understand-which-aggregation-operations-use-the-index)

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Recuento de documentos recuperados aproximadamente igual al de documentos de salida

- [Minimice las consultas con particiones cruzadas.](#minimize-cross-partition-queries)

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>El cargo por RU de la consulta es aceptable, pero la latencia sigue siendo demasiado elevada

- [Mejora de la proximidad](#improve-proximity)

- [Aumento del rendimiento aprovisionado](#increase-provisioned-throughput)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas en las que el recuento de documentos recuperados supera el de documentos de salida

 El `retrievedDocumentCount` es el número de documentos que el motor de consulta necesitaba cargar. El `outputDocumentCount` es el número de documentos que devuelve la consulta. Si el `retrievedDocumentCount` es significativamente mayor que el de `outputDocumentCount`, habrá al menos una parte de la consulta que no haya podido usar el índice y haya tenido que realizar un examen.

A continuación se muestra un ejemplo de consulta de examen que el índice no sirvió completamente:

**Comando $explain:**

```
db.coll.find(
  {
    $and : [
            { "foodGroup" : "Cereal Grains and Pasta"}, 
            { "description" : "Oat bran, cooked"}
        ]
  }
).explain({"executionStatistics": true })
```

**Salida:**

```json
{
    "stages" : [ 
        {
            "stage" : "$query",
            "timeInclusiveMS" : 436.5716,
            "timeExclusiveMS" : 436.5716,
            "in" : 1,
            "out" : 1,
            "details" : {
                "database" : "db-test",
                "collection" : "indexing-test",
                "query" : {
                    "$and" : [ 
                        {
                            "foodGroup" : {
                                "$eq" : "Cereal Grains and Pasta"
                            }
                        }, 
                        {
                            "description" : {
                                "$eq" : "Oat bran, cooked"
                            }
                        }
                    ]
                },
                "pathsIndexed" : [],
                "pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ],
                "shardInformation" : [ 
                    {
                        "activityId" : "13a5977e-a10a-4329-b68e-87e4f0081cac",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 435.4867,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1,
                        "retrievedDocumentCount" : 8618
                    }
                ],
                "queryMetrics" : {
                    "retrievedDocumentCount" : 8618,
                    "retrievedDocumentSizeBytes" : 104963042,
                    "outputDocumentCount" : 1,
                    "outputDocumentSizeBytes" : 6064,
                    "indexHitRatio" : 0.0,
                    "totalQueryExecutionTimeMS" : 433.64,
                    "queryPreparationTimes" : {
                        "queryCompilationTimeMS" : 0.12,
                        "logicalPlanBuildTimeMS" : 0.09,
                        "physicalPlanBuildTimeMS" : 0.1,
                        "queryOptimizationTimeMS" : 0.02
                    },
                    "indexLookupTimeMS" : 0,
                    "documentLoadTimeMS" : 387.44,
                    "vmExecutionTimeMS" : 432.93,
                    "runtimeExecutionTimes" : {
                        "queryEngineExecutionTimeMS" : 45.36,
                        "systemFunctionExecutionTimeMS" : 16.86,
                        "userDefinedFunctionExecutionTimeMS" : 0
                    },
                    "documentWriteTimeMS" : 0.13
                }
            }
        }
    ],
    "estimatedDelayFromRateLimitingInMilliseconds" : 0.0,
    "continuation" : {
        "hasMore" : false
    },
    "ok" : 1.0
}
```

`retrievedDocumentCount` (8618) es significativamente mayor que `outputDocumentCount` (1), lo que implica que esta consulta requirió un examen del documento. 

### <a name="include-necessary-indexes"></a>Inclusión de los índices necesarios

Debe comprobar la matriz `pathsNotIndexed` y agregar estos índices. En este ejemplo, las rutas de acceso `foodGroup` y `description` deben indexarse.

```json
"pathsNotIndexed" : [ 
                    "foodGroup", 
                    "description"
                ]
```

Los procedimientos recomendados de indexación en la API para MongoDB de Azure Cosmos DB son diferentes de MongoDB. En la API para MongoDB de Azure Cosmos DB, los índices compuestos solo se usan en consultas que necesitan ordenar de forma eficaz por varias propiedades. Si tiene consultas con filtros en varias propiedades, debe crear índices de campo único para cada una de estas propiedades. Los predicados de consulta pueden usar varios índices de campo único.

Los [índices de caracteres comodín](mongodb-indexing.md#wildcard-indexes) pueden simplificar la indexación. A diferencia de MongoDB, los índices de caracteres comodín pueden admitir varios campos en predicados de consulta. No habrá ninguna diferencia en el rendimiento de las consultas si se usa un solo índice de caracteres comodín en lugar de crear un índice independiente para cada propiedad. Agregar un índice de caracteres comodín para todas las propiedades es la forma más fácil de optimizar todas las consultas.

Puede agregar nuevos índices en cualquier momento, sin que ello afecte a la disponibilidad de la escritura o la lectura. Puede [realizar el seguimiento del progreso de transformación del índice](../how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-aggregation-operations-use-the-index"></a>Obtención de información sobre qué operaciones de agregación usan el índice

En la mayoría de los casos, las operaciones de agregación en la API para MongoDB de Azure Cosmos DB usarán índices parcialmente. Normalmente, el motor de consultas aplicará primero los filtros de igualdad y de intervalo, y usará índices. Después de aplicar estos filtros, el motor de consultas puede evaluar los filtros adicionales y recurrir a la carga de los documentos restantes para calcular el agregado, si es necesario. 

Este es un ejemplo:

```
db.coll.aggregate( [
   { $match: { foodGroup: 'Fruits and Fruit Juices' } },
   {
     $group: {
        _id: "$foodGroup",
        total: { $max: "$version" }
     }
   }
] )
```

En este caso, los índices pueden optimizar la fase `$match`. La adición de un índice para `foodGroup` mejorará significativamente el rendimiento de las consultas. Al igual que en MongoDB, debe colocar `$match` tan pronto como sea posible en la canalización de agregación para maximizar el uso de los índices.

En la API para MongoDB de Azure Cosmos DB, los índices no se usan para la agregación real, que en este caso es `$max`. La adición de un índice en `version` no mejorará el rendimiento de la consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas en las que el recuento de documentos recuperados es igual al de documentos de salida

Si el `retrievedDocumentCount` es aproximadamente igual al de los `outputDocumentCount`, significa que el motor de consulta no tuvo que examinar muchos documentos innecesarios.

### <a name="minimize-cross-partition-queries"></a>Minimización de las consultas con particiones cruzadas

Azure Cosmos DB usa [particiones](../partitioning-overview.md) para escalar contenedores individuales a medida que aumentan las necesidades de almacenamiento de datos y las unidades de solicitud. Cada partición física tiene un índice independiente. Si la consulta tiene un filtro de igualdad que coincide con la clave de partición del contenedor, solo tendrá que comprobar el índice de la partición pertinente. Esta optimización reduce el número total de RU que requiere la consulta. [Obtenga más información sobre las diferencias entre las consultas en particiones y las consultas entre particiones](../how-to-query-container.md).

Si tiene un gran número de RU aprovisionadas (más de 30 000) o una gran cantidad de datos almacenados (más de aproximadamente 100 GB), es probable que tenga un contenedor lo suficientemente grande como para ver una reducción significativa en los cargos de las RU de consulta. 

Puede comprobar la matriz `shardInformation` para comprender las métricas de consulta de cada partición física individual. El número de valores de `shardKeyRangeId` únicos es el número de particiones físicas en las que la consulta debía ejecutarse. En este ejemplo, la consulta se ejecutó en cuatro particiones físicas. Es importante comprender que la ejecución es completamente independiente del uso de índices. Es decir, las consultas entre particiones pueden seguir usando índices.

```json
  "shardInformation" : [ 
                    {
                        "activityId" : "42f670a8-a201-4c58-8023-363ac18d9e18",
                        "shardKeyRangeId" : "5",
                        "durationMS" : 24.3859,
                        "preemptions" : 1,
                        "outputDocumentCount" : 463,
                        "retrievedDocumentCount" : 463
                    }, 
                    {
                        "activityId" : "a8bf762a-37b9-4c07-8ed4-ae49961373c0",
                        "shardKeyRangeId" : "2",
                        "durationMS" : 35.8328,
                        "preemptions" : 1,
                        "outputDocumentCount" : 905,
                        "retrievedDocumentCount" : 905
                    }, 
                    {
                        "activityId" : "3754e36b-4258-49a6-8d4d-010555628395",
                        "shardKeyRangeId" : "1",
                        "durationMS" : 67.3969,
                        "preemptions" : 1,
                        "outputDocumentCount" : 1479,
                        "retrievedDocumentCount" : 1479
                    }, 
                    {
                        "activityId" : "a69a44ee-db97-4fe9-b489-3791f3d52878",
                        "shardKeyRangeId" : "0",
                        "durationMS" : 185.1523,
                        "preemptions" : 1,
                        "outputDocumentCount" : 867,
                        "retrievedDocumentCount" : 867
                    }
                ]
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimizaciones que reducen la latencia de las consultas

En muchos casos, el cargo por RU podría ser aceptable cuando la latencia de consulta sigue siendo demasiado alta. En las secciones siguientes se ofrece información general con sugerencias para reducir la latencia de las consultas. Si ejecuta la misma consulta varias veces en el mismo conjunto de datos, normalmente tendrá el mismo cargo por RU cada vez. Sin embargo, la latencia de las consultas podría variar entre una ejecución y otra.

### <a name="improve-proximity"></a>Mejora de la proximidad

Las consultas que se ejecutan desde una región distinta de aquella de la cuenta de Azure Cosmos DB tendrán una latencia mayor que si se ejecutaran desde la misma región. Por ejemplo, si ejecutara código en el equipo de escritorio, debería esperar que la latencia fuera decenas o cientos (o más) de milisegundos más que si la consulta proviniera de una máquina virtual dentro de la misma región de Azure que Azure Cosmos DB. Es fácil [distribuir datos globalmente en Azure Cosmos DB](tutorial-global-distribution-mongodb.md) para asegurarse de que puede acercar los datos a la aplicación.

### <a name="increase-provisioned-throughput"></a>Aumento de la capacidad de proceso aprovisionada

En Azure Cosmos DB, el rendimiento aprovisionado se mide en unidades de solicitud (RU). Supongamos que tiene una consulta que consume 5 RU de rendimiento. Por ejemplo, si aprovisiona 1000 RU, podrá ejecutar esa consulta 200 veces por segundo. Si intentó ejecutar la consulta cuando no había suficiente rendimiento disponible, Azure Cosmos DB limitará la velocidad de las solicitudes. La API para MongoDB de Azure Cosmos DB volverá a intentar automáticamente esta consulta tras una breve espera. Las solicitudes limitadas tardan más, por lo que aumentar el rendimiento aprovisionado puede mejorar la latencia de las consultas.

El valor `estimatedDelayFromRateLimitingInMilliseconds` da la sensación de posibles ventajas de latencia si aumenta el rendimiento.

## <a name="next-steps"></a>Pasos siguientes

* [Solución de problemas del rendimiento de las consultas (API de SQL)](troubleshoot-query-performance.md)
* [Administración de la indexación en la API de Azure Cosmos DB para MongoDB](mongodb-indexing.md)
