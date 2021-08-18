---
title: Cláusula ORDER BY en Azure Cosmos DB
description: Obtenga información sobre la cláusula ORDER BY de SQL para Azure Cosmos DB. Use SQL como lenguaje de consulta de JSON para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: abc81e54b2b3ce1a59d3026f3b0908b41559d1a4
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206828"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>Cláusula ORDER BY en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

La cláusula opcional `ORDER BY` especifica el criterio de ordenación para los resultados que devuelve la consulta.

## <a name="syntax"></a>Sintaxis
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argumentos
  
- `<sort_specification>`  
  
   Especifica una propiedad o una expresión para ordenar el conjunto de resultados de la consulta. Se puede especificar una columna de ordenación como alias de nombre o de propiedad.  
  
   Se pueden especificar varias propiedades. Los nombres de propiedad deben ser únicos. La secuencia de las propiedades de ordenación en la cláusula `ORDER BY` define la organización del conjunto de resultados ordenado. Es decir, el conjunto de resultados se ordena en función de la primera propiedad, esa lista ordenada se ordena en función de la segunda propiedad y así sucesivamente.  
  
   Los nombres de propiedad indicados en la cláusula `ORDER BY` deben corresponderse con una propiedad de la lista seleccionada o con una que se defina en la colección que se especifique en la cláusula `FROM` sin ambigüedades.  
  
- `<sort_expression>`  
  
   Especifica una o más propiedades o expresiones para ordenar el conjunto de resultados de la consulta.  
  
- `<scalar_expression>`  
  
   Consulte la sección [Expresiones escalares](sql-query-scalar-expressions.md) para más información.  
  
- `ASC | DESC`  
  
   Indica que los valores de la columna especificada se deben ordenar en sentido ascendente o descendente. `ASC` ordena del valor mínimo al valor máximo. `DESC` ordena del valor máximo al valor mínimo. `ASC` es el criterio de ordenación predeterminado. Los valores NULL se tratan como los valores más bajos posibles.  
  
## <a name="remarks"></a>Observaciones  
  
   La cláusula `ORDER BY` exige que la directiva de indexación incluya un índice de los campos que se van a ordenar. El tiempo de ejecución de consulta de Azure Cosmos DB admite la ordenación con un nombre de propiedad y no con las propiedades calculadas. Azure Cosmos DB admite varias propiedades `ORDER BY`. Para ejecutar una consulta con varias propiedades ORDER BY, debe definir un [índice compuesto](../index-policy.md#composite-indexes) en los campos que se ordenan.

> [!Note]
> Si las propiedades que se van a ordenar pueden no estar definidas en algunos documentos y quiere recuperarlos en una consulta ORDER BY, debe incluir esta ruta de acceso de forma explícita en el índice. La directiva de indexación predeterminada no permite la recuperación de los documentos en los que la propiedad de ordenación no está definida. [Revise consultas de ejemplo en documentos en los que faltan algunos campos](#documents-with-missing-fields).

## <a name="examples"></a>Ejemplos

Por ejemplo, aquí hay una consulta que recupera las familias por nombre de la ciudad de residencia en orden ascendente:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Los resultados son:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

La consulta siguiente recupera los `id` de familia en el orden de la fecha de creación del elemento. El elemento `creationDate` es un número que representa la *hora de época* o el tiempo transcurrido desde el 1 de enero de 1970, en segundos.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Los resultados son:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Además, puede ordenar por varias propiedades. Una consulta que ordena por varias propiedades requiere un [índice compuesto](../index-policy.md#composite-indexes). Considere la siguiente consulta:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Esta consulta recupera los `id` de familia en orden ascendente del nombre de la ciudad. Si varios elementos tienen el mismo nombre de ciudad, la consulta se ordena por `creationDate` en orden descendente.

## <a name="documents-with-missing-fields"></a>Documentos en los que faltan campos

Las consultas con `ORDER BY` que se ejecutan en contenedores con la directiva de indexación predeterminada no devuelven documentos en los que la propiedad de ordenación no esté definida. Si quiere incluir documentos en los que la propiedad de ordenación esté sin definir, debe incluir de forma explícita esta propiedad en la directiva de indexación.

Por ejemplo, este es un contenedor con una directiva de indexación que no incluye explícitamente ninguna ruta de acceso además de `"/*"`:

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

Si ejecuta una consulta que incluye `lastName` en la cláusula `Order By`, los resultados solo abarcan los documentos con una propiedad `lastName` definida. No se ha definido una ruta de acceso incluida explícita para `lastName`, por lo que los documentos sin `lastName` no aparecen en los resultados de la consulta.

Esta es una consulta que ordena por `lastName` en dos documentos, uno de los cuales no tiene definido el elemento `lastName`:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Los resultados solo incluyen el documento con `lastName`definido:

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Si se actualiza la directiva de indexación del contenedor para incluir de forma explícita una ruta de acceso para `lastName`, se incluyen los documentos con una propiedad de ordenación sin definir en los resultados de la consulta. Debe definir explícitamente la ruta de acceso a este valor escalar (y no más allá). Debe usar el carácter `?` en la definición de la ruta de acceso de la directiva de indexación a fin de asegurarse de que la propiedad `lastName` se indexa explícitamente y de que no hay más rutas anidadas adicionales. Si la consulta `Order By` usa un [índice compuesto](../index-policy.md#composite-indexes), los resultados siempre incluirán documentos con una propiedad de ordenación sin definir en los resultados de la consulta.

Esta es una directiva de indexación de ejemplo que permite que documentos con `lastName` sin definir aparezcan en los resultados de la consulta:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Si vuelve a ejecutar la misma consulta, los documentos en los que falta `lastName` aparecen en primer lugar en los resultados de la consulta:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Los resultados son:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Si modifica el criterio de ordenación de `DESC`, los documentos en los que falta `lastName` aparecen en último lugar en los resultados de la consulta:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Los resultados son:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> Solo la versión 3.4.0 o posterior del SDK de .NET admite ORDER BY con tipos mixtos. Por lo tanto, si quiere ordenar por una combinación de valores definidos y sin definir, debe usar esta versión (o una posterior).

No se puede controlar el orden en el que aparecen los distintos tipos en los resultados. En el ejemplo anterior, se muestra cómo se ordenaron los valores sin definir antes de los valores de cadena. Si, por ejemplo, prefiere más control sobre el criterio de ordenación de los valores sin definir, puede asignar a cualquier propiedad sin definir un valor de cadena "aaaaaaaaa" o "zzzzzzzz" para asegurarse de que sea la primera o la última.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción](sql-query-getting-started.md)
- [Directivas de indexación en Azure Cosmos DB](../index-policy.md)
- [Cláusula OFFSET LIMIT](sql-query-offset-limit.md)
