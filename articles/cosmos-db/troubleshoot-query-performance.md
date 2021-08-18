---
title: Solución de problemas de consulta al usar Azure Cosmos DB
description: Aprenda a identificar, diagnosticar y solucionar problemas de consultas SQL de Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 02/16/2021
ms.author: tisande
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: bbd915a912f69c2e3fbaf4cb493ba8bfe90f1552
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730136"
---
# <a name="troubleshoot-query-issues-when-using-azure-cosmos-db"></a>Solución de problemas de consulta al usar Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artículo le guía por un enfoque recomendado general para la solución de problemas con las consultas en Azure Cosmos DB. Aunque no debe tener en cuenta los pasos descritos en este artículo como una defensa completa contra posibles problemas de consulta, hemos incluido aquí las sugerencias de rendimiento más comunes. Use este artículo como punto de partida para la solución de problemas con las consultas lentas o costosas en Core (SQL) API de Azure Cosmos DB. También puede usar los [registros de diagnóstico](cosmosdb-monitor-resource-logs.md) para identificar las consultas lentas o que reducen considerablemente el rendimiento. Si usa la API de Azure Cosmos DB para MongoDB, debe usar la [guía de solución de problemas de la API de Azure Cosmos DB para MongoDB](mongodb/troubleshoot-query-performance.md).

Las categorías de las optimizaciones de consulta en Azure Cosmos DB son las siguientes:

- Optimizaciones que reducen el cargo por unidades de solicitud (RU) de la consulta
- Optimizaciones que solo reducen la latencia

Al reducir el cargo por RU de una consulta, normalmente se reducirá la latencia.

En este artículo se ofrecen ejemplos que se pueden volver a crear mediante el conjunto de datos [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json).

## <a name="common-sdk-issues"></a>Problemas comunes del SDK

Antes de leer esta guía, es útil tener en cuenta los problemas comunes del SDK que no están relacionados con el motor de consultas.

- Siga estas [sugerencias para mejorar el rendimiento del SDK](performance-tips.md).
    - [Guía de solución de problemas del SDK de .NET](troubleshoot-dot-net-sdk.md)
    - [Guía de solución de problemas del SDK de Java](troubleshoot-java-sdk-v4-sql.md)
- El SDK permite definir un valor `MaxItemCount` para las consultas, pero no puede especificar un número mínimo de elementos.
    - El código debe controlar cualquier tamaño de página entre 0 y `MaxItemCount`.
- A veces, las consultas pueden tener páginas vacías incluso cuando hay resultados en una página futura. Estas pueden ser algunas de las razones:
    - El SDK podría estar realizando varias llamadas de red.
    - La consulta podría estar tardando mucho tiempo en recuperar los documentos.
- Todas las consultas tienen un token de continuación que permitirá que continúe la consulta. Asegúrese de purgar la consulta completamente. Más información sobre [Administración de varias páginas de resultados](sql-query-pagination.md#handling-multiple-pages-of-results)

## <a name="get-query-metrics"></a>Obtención de las métricas de consulta

Al optimizar una consulta en Azure Cosmos DB, el primer paso es siempre [obtener las métricas](profile-sql-api-query.md) de la consulta. Estas métricas también están disponibles en Azure Portal. Una vez que ejecute la consulta en el Explorador de datos, las métricas de consulta estarán visibles junto a la pestaña **Resultados**:

:::image type="content" source="./media/troubleshoot-query-performance/obtain-query-metrics.png" alt-text="Obtención de las métricas de consulta" lightbox="./media/troubleshoot-query-performance/obtain-query-metrics.png":::

Después de obtener las métricas de consulta, compare el **recuento de documentos recuperados** con el de **documentos de salida** de la consulta. Use esta comparación para identificar las secciones pertinentes a las que se hará referencia en este artículo.

El **recuento de documentos recuperado** es el número de documentos que el motor de consulta necesitaba cargar. El **recuento de documentos de salida** es el número de documentos necesarios para los resultados de la consulta. Si el **recuento de documentos recuperados** es significativamente mayor que el de **documentos de salida**, habrá al menos una parte de la consulta que no haya podido usar el índice y haya tenido que realizar un examen.

Consulte las secciones siguientes para entender las optimizaciones de consulta pertinentes para su escenario.

### <a name="querys-ru-charge-is-too-high"></a>El cargo por RU de la consulta es demasiado elevado

#### <a name="retrieved-document-count-is-significantly-higher-than-output-document-count"></a>El recuento de documentos recuperados es significativamente mayor que el de documentos de salida

- [Inclusión de las rutas de acceso necesarias en la directiva de indexación](#include-necessary-paths-in-the-indexing-policy)

- [Conocimiento de las funciones del sistema que usan el índice](#understand-which-system-functions-use-the-index)

- [Mejora de la ejecución de funciones del sistema de cadena.](#improve-string-system-function-execution)

- [Conocimiento de las consultas agregadas que usan el índice](#understand-which-aggregate-queries-use-the-index)

- [Optimice las consultas que tienen un filtro y una cláusula ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

- [Optimización de las expresiones JOIN mediante una subconsulta](#optimize-join-expressions-by-using-a-subquery)

<br>

#### <a name="retrieved-document-count-is-approximately-equal-to-output-document-count"></a>Recuento de documentos recuperados aproximadamente igual al de documentos de salida

- [Minimice las consultas con particiones cruzadas.](#minimize-cross-partition-queries)

- [Optimización de las consultas que tienen filtros en varias propiedades](#optimize-queries-that-have-filters-on-multiple-properties)

- [Optimice las consultas que tienen un filtro y una cláusula ORDER BY.](#optimize-queries-that-have-both-a-filter-and-an-order-by-clause)

<br>

### <a name="querys-ru-charge-is-acceptable-but-latency-is-still-too-high"></a>El cargo por RU de la consulta es aceptable, pero la latencia sigue siendo demasiado elevada

- [Mejora de la proximidad](#improve-proximity)

- [Aumento del rendimiento aprovisionado](#increase-provisioned-throughput)

- [Aumento de MaxConcurrency](#increase-maxconcurrency)

- [Aumento de MaxBufferedItemCount](#increase-maxbuffereditemcount)

## <a name="queries-where-retrieved-document-count-exceeds-output-document-count"></a>Consultas en las que el recuento de documentos recuperados supera el de documentos de salida

 El **recuento de documentos recuperado** es el número de documentos que el motor de consulta necesitaba cargar. El **recuento de documentos de salida** es el número de documentos que devuelve la consulta. Si el **recuento de documentos recuperados** es significativamente mayor que el de **documentos de salida**, habrá al menos una parte de la consulta que no haya podido usar el índice y haya tenido que realizar un examen.

A continuación se muestra un ejemplo de consulta de examen que el índice no sirvió completamente:

Consulta:

 ```sql
SELECT VALUE c.description
FROM c
WHERE UPPER(c.description) = "BABYFOOD, DESSERT, FRUIT DESSERT, WITHOUT ASCORBIC ACID, JUNIOR"
 ```

Métricas de la consulta:

```
Retrieved Document Count                 :          60,951
Retrieved Document Size                  :     399,998,938 bytes
Output Document Count                    :               7
Output Document Size                     :             510 bytes
Index Utilization                        :            0.00 %
Total Query Execution Time               :        4,500.34 milliseconds
  Query Preparation Times
    Query Compilation Time               :            0.09 milliseconds
    Logical Plan Build Time              :            0.05 milliseconds
    Physical Plan Build Time             :            0.04 milliseconds
    Query Optimization Time              :            0.01 milliseconds
  Index Lookup Time                      :            0.01 milliseconds
  Document Load Time                     :        4,177.66 milliseconds
  Runtime Execution Times
    Query Engine Times                   :          322.16 milliseconds
    System Function Execution Time       :           85.74 milliseconds
    User-defined Function Execution Time :            0.00 milliseconds
  Document Write Time                    :            0.01 milliseconds
Client Side Metrics
  Retry Count                            :               0
  Request Charge                         :        4,059.95 RUs
```

El **recuento de documentos recuperados** (60 951) es significativamente mayor que el de **documentos de salida** (7), lo que implica que esta consulta generó un examen de documentos. En este caso, la función del sistema [UPPER()](sql-query-upper.md) no usa un índice.

### <a name="include-necessary-paths-in-the-indexing-policy"></a>Inclusión de las rutas de acceso necesarias en la directiva de indexación

La directiva de indexación debe cubrir cualquier propiedad incluida en las cláusulas `WHERE`, `ORDER BY`, `JOIN` y la mayoría de las funciones del sistema. Las rutas de acceso deseadas especificadas en la directiva de indexación deben coincidir con las propiedades de los documentos JSON.

> [!NOTE]
> Las propiedades de la directiva de indexación de Azure Cosmos DB distinguen mayúsculas de minúsculas

Si ejecuta la siguiente consulta simple en el conjunto de datos [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json), observará un cargo por RU mucho menor cuando la propiedad de la cláusula `WHERE` esté indexada:

#### <a name="original"></a>Original

Consulta:

```sql
SELECT *
FROM c
WHERE c.description = "Malabar spinach, cooked"
```

Directiva de indexación:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/description/*"
        }
    ]
}
```

**Cargo por RU:** 409,51 RU

#### <a name="optimized"></a>Optimizado

Directiva de indexación actualizada:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

**Cargo por RU:** 2,98 RU

Puede agregar propiedades a la directiva de indexación en cualquier momento, sin que ello afecte a la disponibilidad de la escritura o la lectura. Puede [realizar el seguimiento del progreso de transformación del índice](./how-to-manage-indexing-policy.md#dotnet-sdk).

### <a name="understand-which-system-functions-use-the-index"></a>Conocimiento de las funciones del sistema que usan el índice

La mayoría de las funciones del sistema usan índices. Esta es una lista de funciones de cadena comunes que usan índices:

- StartsWith
- Contains
- RegexMatch
- Left
- Subcadena: pero solo si la primera instancia de num_expr es 0

A continuación se indican algunas funciones del sistema comunes que no usa el índice y que debe cargar cada documento cuando se usan en una cláusula `WHERE`:

| **Función del sistema**                     | **Ideas de optimización**             |
| --------------------------------------- |------------------------------------------------------------ |
| Mayúsculas y minúsculas                         | En lugar de usar la función del sistema para normalizar los datos al realizar comparaciones, puede normalizar el uso de mayúsculas y minúsculas durante la inserción. Una consulta como ```SELECT * FROM c WHERE UPPER(c.name) = 'BOB'``` se convierte en ```SELECT * FROM c WHERE c.name = 'BOB'```. |
| GetCurrentDateTime/GetCurrentTimestamp/GetCurrentTicks | Calcule la hora actual antes de la ejecución de la consulta y use ese valor de cadena en la cláusula `WHERE`. |
| Funciones matemáticas (sin agregados) | Si necesita calcular frecuentemente un valor en la consulta, considere la posibilidad de almacenarlo como propiedad en el documento JSON. |

Estas funciones del sistema pueden usar índices, excepto cuando se usan en consultas con agregados:

| **Función del sistema**                     | **Ideas de optimización**             |
| --------------------------------------- |------------------------------------------------------------ |
| Funciones del sistema espacial                        | Almacenamiento del resultado de la consulta en una vista materializada en tiempo real |

Cuando se usan en la cláusula `SELECT`, las funciones del sistema ineficaces no afectarán al modo en que las consultas pueden usar índices.

### <a name="improve-string-system-function-execution"></a>Mejora de la ejecución de funciones del sistema de cadena

En algunas funciones del sistema que usan índices, se puede mejorar la ejecución de consultas agregando una cláusula `ORDER BY` a la consulta. 

Más concretamente, las funciones del sistema cuya carga de RU aumenta al mismo tiempo que la cardinalidad de la propiedad pueden beneficiarse de tener `ORDER BY` en la consulta. Estas consultas realizan una exploración del índice, por lo el hecho de que los resultados de la consulta estén ordenados puede hacer que la consulta sea más eficaz.

Esta optimización puede mejorar la ejecución de las siguientes funciones del sistema:

- StartsWith (donde case-insensitive = true)
- StringEquals (donde case-insensitive = true)
- Contains
- RegexMatch
- EndsWith

Por ejemplo, considere la siguiente consulta con `CONTAINS`. `CONTAINS` usará índices, pero a veces, incluso después de agregar el índice pertinente, es posible que siga apareciendo un cargo de RU muy alto al ejecutar la siguiente consulta.

Consulta original:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
```

La ejecución de la consulta se puede mejorar agregando `ORDER BY`:

```sql
SELECT *
FROM c
WHERE CONTAINS(c.town, "Sea")
ORDER BY c.town
```

La misma optimización puede ayudar en consultas con filtros adicionales. En este caso, es mejor agregar también propiedades con filtros de igualdad a la cláusula `ORDER BY`.

Consulta original:

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
```

La ejecución de la consulta se puede mejorar agregando `ORDER BY` y [un índice compuesto](index-policy.md#composite-indexes) para (c.name, c.town):

```sql
SELECT *
FROM c
WHERE c.name = "Samer" AND CONTAINS(c.town, "Sea")
ORDER BY c.name, c.town
```

### <a name="understand-which-aggregate-queries-use-the-index"></a>Conocimiento de las consultas agregadas que usan el índice

En la mayoría de los casos, las funciones del sistema agregado en Azure Cosmos DB utilizarán el índice. Sin embargo, en función de los filtros o las cláusulas adicionales de una consulta de funciones agregadas, es posible que el motor de consultas tenga que cargar un número elevado de documentos. Normalmente, el motor de consultas aplicará primero los filtros de igualdad y de intervalo. Después de aplicar estos filtros, el motor de consultas puede evaluar los filtros adicionales y recurrir a la carga de los documentos restantes para calcular el agregado, si es necesario.

Por ejemplo, dadas estas dos consultas de ejemplo, la consulta con un filtro de igualdad y otro de función del sistema `CONTAINS` generalmente será más eficaz que una consulta con solo un filtro de función del sistema `CONTAINS`. Esto se debe a que el filtro de igualdad se aplica primero y usa el índice antes de que se deban cargar los documentos para el filtro de `CONTAINS` más costoso.

Consulta solo con el filtro `CONTAINS`: mayor carga de unidad de solicitud:

```sql
SELECT COUNT(1)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

Consulta con filtro de igualdad y filtro `CONTAINS`: menor carga de unidad de solicitud:

```sql
SELECT AVG(c._ts)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats" AND CONTAINS(c.description, "spinach")
```

A continuación, se muestran ejemplos adicionales de consultas de funciones agregadas que no usarán por completo el índice:

#### <a name="queries-with-system-functions-that-dont-use-the-index"></a>Consultas con funciones del sistema que no usan el índice

Debe consultar la [página de la función del sistema](sql-query-system-functions.md) correspondiente para ver si usa el índice.

```sql
SELECT MAX(c._ts)
FROM c
WHERE CONTAINS(c.description, "spinach")
```

#### <a name="aggregate-queries-with-user-defined-functionsudfs"></a>Consultas de funciones agregadas con funciones definidas por el usuario

```sql
SELECT AVG(c._ts)
FROM c
WHERE udf.MyUDF("Sausages and Luncheon Meats")
```

#### <a name="queries-with-group-by"></a>Consultas con GROUP BY

La carga de RU de las consultas con `GROUP BY` aumentará a medida que aumente la cardinalidad de las propiedades de la cláusula `GROUP BY`. En la consulta siguiente, por ejemplo, la carga de RU de la consulta aumentará a medida que lo haga el número de descripciones únicas.

La carga de RU de una función de agregado con una cláusula `GROUP BY` será mayor que la de una función de agregado por sí misma. En este ejemplo, el motor de consultas debe cargar todos los documentos que coincidan con el filtro `c.foodGroup = "Sausages and Luncheon Meats"`, por lo que se espera que la carga de unidad de solicitud sea alta.

```sql
SELECT COUNT(1)
FROM c
WHERE c.foodGroup = "Sausages and Luncheon Meats"
GROUP BY c.description
```

Si tiene previsto ejecutar las mismas consultas de funciones agregadas con frecuencia, puede resultar más eficaz crear una vista materializada en tiempo real con la [fuente de cambios de Azure Cosmos DB](change-feed.md), en lugar de ejecutar consultas individuales.

### <a name="optimize-queries-that-have-both-a-filter-and-an-order-by-clause"></a>Optimización de las consultas que tienen un filtro y una cláusula ORDER BY

Aunque normalmente las consultas con filtro y una cláusula `ORDER BY` utilizan un índice de intervalo, serán más eficientes si se pueden servir desde un índice compuesto. Además de modificar la directiva de indexación, debe agregar todas las propiedades del índice compuesto a la cláusula `ORDER BY`. Este cambio en la consulta garantizará que utiliza el índice compuesto.  Puede observar el impacto mediante la ejecución de una consulta en el conjunto de datos [nutrition](https://github.com/CosmosDB/labs/blob/master/dotnet/setup/NutritionData.json):

#### <a name="original"></a>Original

Consulta:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c._ts ASC
```

Directiva de indexación:

```json
{

        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[]
}
```

**Cargo por RU:** 44,28 RU

#### <a name="optimized"></a>Optimizado

Consulta actualizada (incluye ambas propiedades en la cláusula `ORDER BY`):

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies"
ORDER BY c.foodGroup, c._ts ASC
```

Directiva de indexación actualizada:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
        },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
    }

```

**Cargo por RU:** 8,86 RU

### <a name="optimize-join-expressions-by-using-a-subquery"></a>Optimización de las expresiones JOIN mediante una subconsulta

Las subconsultas multivalor pueden optimizar las expresiones `JOIN` mediante la inserción de predicados después de cada expresión select-many, en lugar de hacerlo después de todas las combinaciones cruzadas en la cláusula `WHERE`.

Considere esta consulta:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0
AND n.nutritionValue < 10) AND s.amount > 1
```

**Cargo por RU:** 167,62 RU

Para esta consulta, el índice coincidirá con cualquier documento que tenga una etiqueta con el nombre `infant formula`, `nutritionValue` mayor que 0 y `amount` mayor que 1. La expresión `JOIN` aquí generará el producto cruzado de todos los elementos de las matrices tags, nutrients y servings para cada documento coincidente antes de aplicar cualquier filtro. Luego, la cláusula `WHERE` aplicará el predicado de filtro en cada tupla `<c, t, n, s>`.

Por ejemplo, si un documento coincidente tenía 10 elementos en cada una de las tres matrices, se expandirá a 1 × 10 × 10 × 10 (es decir, 1000) tuplas. Usar subconsultas aquí puede ayudar a filtrar los elementos de matrices combinadas antes de unirse a la siguiente expresión.

Esta consulta es equivalente a la anterior, pero usa subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

**Cargo por RU:** 22,17 RU

Se supone que solo un elemento de la matriz tags coincide con el filtro, y hay cinco elementos tanto para la matriz de nutrients como la de servings. Las expresiones `JOIN` luego se expandirán a 1 × 1 × 5 × 5 = 25 elementos, en lugar de los 1000 elementos de la primera consulta.

## <a name="queries-where-retrieved-document-count-is-equal-to-output-document-count"></a>Consultas en las que el recuento de documentos recuperados es igual que el de documentos de salida

Si el **recuento de los documentos recuperados** es aproximadamente igual al de los **documentos de salida**, significa que el motor de consulta no tuvo que examinar muchos documentos innecesarios. Para muchas consultas, como las que usan la palabra clave `TOP`, el **recuento de documentos recuperados** puede superar el de **documentos de salida** en una unidad. No es necesario preocuparse por ello.

### <a name="minimize-cross-partition-queries"></a>Minimización de las consultas con particiones cruzadas

Azure Cosmos DB usa [particiones](partitioning-overview.md) para escalar contenedores individuales a medida que aumentan las necesidades de almacenamiento de datos y las unidades de solicitud. Cada partición física tiene un índice independiente. Si la consulta tiene un filtro de igualdad que coincide con la clave de partición del contenedor, solo tendrá que comprobar el índice de la partición pertinente. Esta optimización reduce el número total de RU que requiere la consulta.

Si tiene un gran número de RU aprovisionadas (más de 30 000) o una gran cantidad de datos almacenados (más de aproximadamente 100 GB), es probable que tenga un contenedor lo suficientemente grande como para ver una reducción significativa en los cargos de las RU de consulta.

Por ejemplo, si creamos un contenedor con la clave de partición foodGroup, las siguientes consultas solo tendrían que comprobar una única partición física:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

Las consultas que tienen un filtro `IN` con la clave de partición solo comprobarán las particiones físicas relevantes y no se "distribuirán en ramas":

```sql
SELECT *
FROM c
WHERE c.foodGroup IN("Soups, Sauces, and Gravies", "Vegetables and Vegetable Products") and c.description = "Mushroom, oyster, raw"
```

Las consultas que tienen filtros de rango en la clave de partición o que no tienen ningún filtro en la clave de partición, deberán "distribuirse en ramas" y comprobar los resultados de los índices de todas las particiones físicas.

```sql
SELECT *
FROM c
WHERE c.description = "Mushroom, oyster, raw"
```

```sql
SELECT *
FROM c
WHERE c.foodGroup > "Soups, Sauces, and Gravies" and c.description = "Mushroom, oyster, raw"
```

### <a name="optimize-queries-that-have-filters-on-multiple-properties"></a>Optimización de las consultas que tienen filtros en varias propiedades

Aunque las consultas con filtros en varias propiedades suelen utilizar un índice de intervalo, serán más eficientes si se pueden servir desde un índice compuesto. Esta optimización no afectará considerablemente a cantidades pequeñas de datos. No obstante, puede resultar útil para grandes cantidades de datos. Como máximo puede optimizar un filtro de distinto del de igualdad por índice compuesto. Si la consulta tiene varios filtros distintos del de igualdad, elija uno de ellos para que utilice el índice compuesto. El resto continuará usando índices de intervalo. El filtro distinto del de igualdad debe definirse en último lugar en el índice compuesto. [Consulta más información sobre los índices compuestos](index-policy.md#composite-indexes).

Estos son algunos ejemplos de consultas que se podrían optimizar con un índice compuesto:

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts = 1575503264
```

```sql
SELECT *
FROM c
WHERE c.foodGroup = "Vegetables and Vegetable Products" AND c._ts > 1575503264
```

Este es el índice compuesto pertinente:

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/foodGroup",
                    "order":"ascending"
                },
                {  
                    "path":"/_ts",
                    "order":"ascending"
                }
            ]
        ]
}
```

## <a name="optimizations-that-reduce-query-latency"></a>Optimizaciones que reducen la latencia de las consultas

En muchos casos, el cargo por RU podría ser aceptable cuando la latencia de consulta sigue siendo demasiado alta. En las secciones siguientes se ofrece información general con sugerencias para reducir la latencia de las consultas. Si ejecuta la misma consulta varias veces en el mismo conjunto de datos, normalmente tendrá el mismo cargo por RU cada vez. Sin embargo, la latencia de las consultas podría variar entre una ejecución y otra.

### <a name="improve-proximity"></a>Mejora de la proximidad

Las consultas que se ejecutan desde una región distinta de aquella de la cuenta de Azure Cosmos DB tendrán una latencia mayor que si se ejecutaran desde la misma región. Por ejemplo, si ejecutara código en el equipo de escritorio, debería esperar que la latencia fuera decenas o cientos (o más) de milisegundos más que si la consulta proviniera de una máquina virtual dentro de la misma región de Azure que Azure Cosmos DB. Es fácil [distribuir datos globalmente en Azure Cosmos DB](distribute-data-globally.md) para asegurarse de que puede acercar los datos a la aplicación.

### <a name="increase-provisioned-throughput"></a>Aumento de la capacidad de proceso aprovisionada

En Azure Cosmos DB, el rendimiento aprovisionado se mide en unidades de solicitud (RU). Supongamos que tiene una consulta que consume 5 RU de rendimiento. Por ejemplo, si aprovisiona 1000 RU, podrá ejecutar esa consulta 200 veces por segundo. Si intentó ejecutar la consulta cuando no había suficiente rendimiento disponible, Azure Cosmos DB devolvería un error HTTP 429. Cualquiera de los SDK de Core (SQL) API actuales volverá a intentar automáticamente esta consulta tras una breve espera. Las solicitudes limitadas tardan más, por lo que aumentar el rendimiento aprovisionado puede mejorar la latencia de las consultas. Puede observar el [número total de solicitudes limitadas](use-metrics.md#understand-how-many-requests-are-succeeding-or-causing-errors) en la hoja **Métrica** de Azure Portal.

### <a name="increase-maxconcurrency"></a>Aumento de MaxConcurrency

Las consultas paralelas funcionan creando consultas en varias particiones en paralelo. Sin embargo, los datos de una recopilación con particiones individual se capturan en serie con respecto a la consulta. Establecer MaxConcurrency en el número de particiones tiene la máxima probabilidad de conseguir el mejor rendimiento de consulta, siempre y cuando el resto de las condiciones del sistema permanezcan invariables. Si no conoce el número de particiones, puede establecer el valor de MaxConcurrency (o MaxDegreesOfParallelism en versiones anteriores del SDK) en un número alto. El sistema elegirá el mínimo (número de particiones, entrada proporcionada por el usuario) como el grado máximo de paralelismo.

### <a name="increase-maxbuffereditemcount"></a>Aumento de MaxBufferedItemCount

Las consultas están diseñadas para capturar previamente los resultados mientras el cliente procesa el lote actual de resultados. La captura previa ayuda a mejorar la latencia general de una consulta. La configuración de MaxBufferedItemCount limita el número de resultados capturados previamente. La configuración de este valor en el número esperado de resultados devueltos (o un número más alto) permite que la consulta reciba el máximo beneficio de la captura previa. Si establece este valor en -1, el sistema determinará automáticamente el número de elementos que se van a almacenar en búfer.

## <a name="next-steps"></a>Pasos siguientes
Consulte los artículos siguientes para obtener información sobre cómo medir las RU por consulta, obtener estadísticas de ejecución para optimizar las consultas y mucho más:

* [Obtención de métricas de ejecución de consultas SQL mediante el SDK de .NET](profile-sql-api-query.md)
* [Optimización del rendimiento de consultas con Azure Cosmos DB](./sql-api-query-metrics.md)
* [Sugerencias de rendimiento para el SDK de .NET](performance-tips.md)
* [Sugerencias de rendimiento para el SDK de Java v4](performance-tips-java-sdk-v4-sql.md)