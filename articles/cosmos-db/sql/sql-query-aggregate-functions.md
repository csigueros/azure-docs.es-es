---
title: Funciones de agregado en Azure Cosmos DB
description: Obtenga información sobre la sintaxis de las funciones de agregado de SQL, los tipos de funciones de agregado compatibles con Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: 77dcba82af8d369115b26bd8ec250a8037fe4951
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206763"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Funciones de agregado en Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Las funciones de agregado realizan un cálculo en un conjunto de valores en la cláusula `SELECT` y devuelven un valor único. Por ejemplo, la consulta siguiente devuelve el número de elementos dentro de un contenedor:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Tipos de funciones de agregado

La API de SQL admite las siguientes funciones de agregado. `SUM` y `AVG` operan en valores numéricos, y `COUNT`, `MIN` y `MAX` trabajan con números, cadenas, valores booleanos y valores null.

| Función | Descripción |
|-------|-------------|
| [AVG](sql-query-aggregate-avg.md) | Devuelve la media de los valores de la expresión. |
| [COUNT](sql-query-aggregate-count.md) | Devuelve el número de elementos de la expresión. |
| [MAX](sql-query-aggregate-max.md) | Devuelve el valor máximo de la expresión. |
| [MIN](sql-query-aggregate-min.md) | Devuelve el valor mínimo de la expresión. |
| [SUM](sql-query-aggregate-sum.md) | Devuelve la suma de todos los valores de la expresión. |


También puede devolver solo el valor escalar del agregado mediante la palabra clave VALUE. Por ejemplo, la siguiente consulta devuelve el número de valores como un único número:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Los resultados son:

```json
    [ 2 ]
```

También puede combinar las agregaciones con filtros. Por ejemplo, la consulta siguiente devuelve el número de elementos cuyo estado de dirección es `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Los resultados son:

```json
    [ 1 ]
```

## <a name="remarks"></a>Comentarios

Estas funciones de agregado del sistema se beneficiarán de un [índice de intervalo](../index-policy.md#includeexclude-strategy). Si espera ejecutar `AVG`, `COUNT`, `MAX`, `MIN` o `SUM` en una propiedad, debe [incluir la ruta de acceso relevante en la directiva de indexación](../index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Azure Cosmos DB](../introduction.md)
- [System functions](sql-query-system-functions.md) (Funciones del sistema)
- [User defined functions](sql-query-udfs.md) (Funciones definidas por el usuario)