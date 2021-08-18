---
title: Trabajar con matrices y objetos en Azure Cosmos DB
description: Obtenga información sobre la sintaxis de SQL para crear matrices y objetos en Azure Cosmos DB. En este artículo también se proporcionan algunos ejemplos para realizar operaciones en objetos de matriz.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: ca62bbb86134e3553c9ebe822096419b8051a872
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206635"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Trabajar con matrices y objetos en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Una característica clave de la API de SQL de Azure Cosmos DB es la creación de matrices y objetos. En este documento se usan ejemplos que se pueden recrear con el [conjunto de datos Family](sql-query-getting-started.md#upload-sample-data).

Este es un elemento de ejemplo de este conjunto de datos:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

## <a name="arrays"></a>Matrices

Puede construir matrices como se muestra en los siguientes ejemplos:

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

Los resultados son:

```json
[
  {
    "CityState": [
      "Seattle",
      "WA"
    ]
  },
  {
    "CityState": [
      "NY", 
      "NY"
    ]
  }
]
```

También puede usar la [expresión ARRAY](sql-query-subquery.md#array-expression) para construir una matriz a partir de los resultados de la [subconsulta](sql-query-subquery.md). Esta consulta obtiene todos los nombres proporcionados distintos de los hijos en una matriz.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Los resultados son:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

## <a name="iteration"></a><a id="Iteration"></a>Iteración

La API de SQL permite iterar en las matrices JSON mediante la [palabra clave IN](sql-query-keywords.md#in) en el origen FROM. En el ejemplo siguiente:

```sql
SELECT *
FROM Families.children
```

Los resultados son:

```json
[
  [
    {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy"}]
    }
  ], 
  [
    {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1
    }, 
    {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }
  ]
]
```

La siguiente consulta realiza la iteración en `children` en el contenedor `Families`. La matriz de salida es diferente de la consulta anterior. En este ejemplo se divide `children` y se reducen los resultados a una sola matriz:  

```sql
SELECT *
FROM c IN Families.children
```

Los resultados son:

```json
[
  {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy" }]
  },
  {
      "familyName": "Merriam",
      "givenName": "Jesse",
      "gender": "female",
      "grade": 1
  },
  {
      "familyName": "Miller",
      "givenName": "Lisa",
      "gender": "female",
      "grade": 8
  }
]
```

Puede filtrar aún más por cada entrada individual de la matriz, como se muestra en el ejemplo siguiente:

```sql
SELECT c.givenName
FROM c IN Families.children
WHERE c.grade = 8
```

Los resultados son:

```json
[{
  "givenName": "Lisa"
}]
```

También puede aplicar agregaciones al resultado de la iteración de una matriz. Por ejemplo, la consulta siguiente cuenta el número de hijos entre todas las familias:

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

Los resultados son:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> Cuando se usa la palabra clave IN para la iteración, no se pueden filtrar ni proyectar las propiedades fuera de la matriz. En su lugar, debe usar [combinaciones](sql-query-join.md).

Para obtener más ejemplos, lea nuestra [entrada de blog sobre cómo trabajar con matrices en Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Pasos siguientes

- [Introducción](sql-query-getting-started.md)
- [Ejemplos de .NET de Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Combinaciones](sql-query-join.md)
