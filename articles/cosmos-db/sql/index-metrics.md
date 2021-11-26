---
title: Métricas de indexación de Azure Cosmos DB
description: Obtenga información sobre cómo obtener e interpretar las métricas de indexación en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: tisande
ms.openlocfilehash: f10208e83c9c7f23600285444d22ed5b5faf2488
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551983"
---
# <a name="indexing-metrics-in-azure-cosmos-db"></a>Métricas de indexación de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB proporciona métricas de indexación para mostrar las rutas de acceso indexadas utilizadas y las rutas de acceso indexadas recomendadas. Puede usar las métricas de indexación para optimizar el rendimiento de las consultas, especialmente en los casos en los que no está seguro de cómo modificar la [directiva de indexación](../index-policy.md)).

> [!NOTE]
> Las métricas de indexación solo se admiten en el SDK de .NET (versión 3.21.0 o posterior) y el SDK de Java (versión 4.19.0 o posterior).

## <a name="enable-indexing-metrics"></a>Habilitación de las métricas de indexación

Puede habilitar las métricas de indexación para una consulta estableciendo la propiedad `PopulateIndexMetrics` en `true`. Si no se especifica, el valor predeterminado de `PopulateIndexMetrics` es `false`. Solo se recomienda habilitar las métricas de índice para solucionar problemas de rendimiento de las consultas. Siempre que las consultas y la directiva de indexación permanezcan igual, es poco probable que las métricas de índice cambien. En su lugar, se recomienda identificar consultas costosas mediante la supervisión de la latencia y el cargo de RU de consulta mediante registros de diagnóstico.

### <a name="net-sdk-example"></a>Ejemplo de SDK de .NET

```csharp
    string sqlQueryText = "SELECT TOP 10 c.id FROM c WHERE c.Item = 'value1234' AND c.Price > 2";

    QueryDefinition query = new QueryDefinition(sqlQueryText);

    FeedIterator<Item> resultSetIterator = container.GetItemQueryIterator<Item>(
                query, requestOptions: new QueryRequestOptions
        {
            PopulateIndexMetrics = true
        });

    FeedResponse<Item> response = null;

    while (resultSetIterator.HasMoreResults)
        {
          response = await resultSetIterator.ReadNextAsync();
          Console.WriteLine(response.IndexMetrics);
        }
```

### <a name="example-output"></a>Salida de ejemplo

En esta consulta de ejemplo, se observan las rutas de acceso utilizadas `/Item/?` y `/Price/?` y los posibles índices compuestos `(/Item ASC, /Price ASC)`.

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /Item/?
    Index Impact Score: High
    ---
    Index Spec: /Price/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /Item ASC, /Price ASC
    Index Impact Score: High
    ---
```

## <a name="utilized-indexed-paths"></a>Rutas de acceso indexadas utilizadas

Los índices únicos utilizados y los índices compuestos utilizados muestran respectivamente las rutas de acceso incluidas y los índices compuestos que usó la consulta. Las consultas pueden usar varias rutas de acceso indexadas, así como una combinación de rutas de acceso incluidas e índices compuestos. Si una ruta de acceso indexada no aparece como utilizada, la eliminación de la ruta de acceso indexada no tendrá ningún impacto en el rendimiento de la consulta.

Considere la lista de rutas de acceso indexadas utilizadas como prueba de que una consulta ha usado esas rutas de acceso. Si no está seguro de si una nueva ruta de acceso indexada mejorará el rendimiento de las consultas, debe intentar agregar las nuevas rutas de acceso indexadas y comprobar si la consulta las usa.

## <a name="potential-indexed-paths"></a>Posibles rutas de acceso indexadas

Los índices únicos posibles y los índices compuestos utilizados muestran respectivamente las rutas de acceso incluidas y los índices compuestos que, si se agregan, puede que la consulta utilice. Si ve posibles rutas de acceso indexadas, considere la posibilidad de agregarlas a la directiva de indexación y observar si mejoran el rendimiento de las consultas.

Considere la lista de posibles rutas de acceso indexadas como recomendaciones en lugar de evidencia concluyente de que una consulta usará una ruta de acceso indexada específica. Las posibles rutas de acceso indexadas no son una lista exhaustiva de rutas de acceso indexadas que una consulta podría usar. Además, es posible que algunas rutas de acceso indexadas posibles no tengan ningún impacto en el rendimiento de las consultas. [Agregue las rutas de acceso indexadas recomendadas](how-to-manage-indexing-policy.md) y confirme que mejoran el rendimiento de las consultas.

> [!NOTE]
> ¿Tiene comentarios sobre las métricas de indexación? Queremos conocerlos. No dude en compartir comentarios directamente con el equipo de ingeniería de Azure Cosmos DB: cosmosdbindexing@microsoft.com.

## <a name="index-impact-score"></a>Puntuación de impacto del índice

La puntuación de impacto del índice es la probabilidad de que una ruta de acceso indexada, basada en la forma de la consulta, tenga un impacto significativo en el rendimiento de las consultas. En otras palabras, la puntuación de impacto del índice es la probabilidad de que, sin esa ruta de acceso indexada específica, el cargo de RU de consulta habría sido considerablemente mayor. 

Hay dos posibles puntuaciones de impacto en el índice: **alta** y **baja**. Si tiene varias rutas de acceso indexadas posibles, se recomienda centrarse en rutas de acceso indexadas con una puntuación de impacto **alta**.

Los únicos criterios usados en la puntuación de impacto del índice son la forma de la consulta. Por ejemplo, en la consulta siguiente, a la ruta de acceso indexada `/name/?` se le asignaría una puntuación de impacto de índice **alta**:

```sql
SELECT * 
FROM c
WHERE c.name = "Samer"
```

El impacto real depende de la naturaleza de los datos. Si solo unos pocos elementos coinciden con el filtro `/name`, la ruta de acceso indexada mejorará considerablemente el cargo de RU de consulta. Sin embargo, si la mayoría de los elementos terminan igualando el filtro `/name` de todos modos, es posible que la ruta de acceso indexada no termine mejorando el rendimiento de las consultas. En cada uno de estos casos, a la ruta de acceso indexada `/name/?` se le asignaría una puntuación de impacto de índice **alta** porque, en función de la forma de la consulta, la ruta de acceso indexada tiene una alta probabilidad de mejorar el rendimiento de las consultas.

## <a name="additional-examples"></a>Ejemplos adicionales

### <a name="example-query"></a>Ejemplo de consulta

```sql
SELECT c.id 
FROM c 
WHERE c.name = 'Tim' AND c.age > 15 AND c.town = 'Redmond' AND c.timestamp > 2349230183
```

### <a name="index-metrics"></a>Indexación de métricas

```
Index Utilization Information
  Utilized Single Indexes
    Index Spec: /name/?
    Index Impact Score: High
    ---
    Index Spec: /age/?
    Index Impact Score: High
    ---
    Index Spec: /town/?
    Index Impact Score: High
    ---
    Index Spec: /timestamp/?
    Index Impact Score: High
    ---
  Potential Single Indexes
  Utilized Composite Indexes
  Potential Composite Indexes
    Index Spec: /name ASC, /town ASC, /age ASC
    Index Impact Score: High
    ---
    Index Spec: /name ASC, /town ASC, /timestamp ASC
    Index Impact Score: High
    ---
```
Estas métricas de índice muestran que la consulta usó las rutas de acceso indexadas `/name/?`, `/age/?`, `/town/?` y `/timestamp/?`. Las métricas de índice también indican que hay una alta probabilidad de que al agregar los índices compuestos `(/name ASC, /town ASC, /age ASC)` y `(/name ASC, /town ASC, /timestamp ASC)` mejorará aún más el rendimiento.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información acerca de la indexación en los siguientes artículos:

- [Introducción a la indexación](../index-overview.md)
- [Cómo administrar la directiva de indexación](how-to-manage-indexing-policy.md)
