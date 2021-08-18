---
title: Funciones matemáticas en el lenguaje de consulta de Azure Cosmos DB
description: Aprenda cómo las funciones matemáticas en Azure Cosmos DB realizan un cálculo en función de los valores de entrada proporcionados como argumentos y devuelven un valor numérico.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/22/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9e3114220b6250afb9d633b073342ba6f2cf92b8
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206751"
---
# <a name="mathematical-functions-azure-cosmos-db"></a>Funciones matemáticas (Azure Cosmos DB)  
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Las funciones matemáticas realizan un cálculo, basado en valores de entrada proporcionados como argumentos, y devuelven un valor numérico.

Puede ejecutar consultas similares a las del ejemplo siguiente:

```sql
    SELECT VALUE ABS(-4)
```

El resultado es el siguiente:

```json
    [4]
```

## <a name="functions"></a>Functions

Las siguientes funciones matemáticas integradas y compatibles realizan un cálculo, basado normalmente en valores de entrada proporcionados como argumentos, y devuelven un valor numérico. La columna de **uso del índice** supone, si procede, que está comparando la función del sistema matemática con otro valor con un filtro de igualdad.
 
| Función del sistema                 | Uso de índices | [Uso de índices en consultas con funciones de agregado escalares](../index-overview.md#index-utilization-for-scalar-aggregate-functions) | Observaciones                                                      |
| ------------------------------- | ----------- | ------------------------------------------------------ | ------------------------------------------------------------ |
| [ABS](sql-query-abs.md)         | Búsqueda del índice  | Búsqueda del índice                                             |                                                              |
| [ACOS](sql-query-acos.md)       | Examen completo   | Examen completo                                              |                                                              |
| [ASIN](sql-query-asin.md)       | Examen completo   | Examen completo                                              |                                                              |
| [ATAN](sql-query-atan.md)       | Examen completo   | Examen completo                                              |                                                              |
| [ATN2](sql-query-atn2.md)       | Examen completo   | Examen completo                                              |                                                              |
| [CEILING](sql-query-ceiling.md) | Búsqueda del índice  | Búsqueda del índice                                             |                                                              |
| [COS](sql-query-cos.md)         | Examen completo   | Examen completo                                              |                                                              |
| [COT](sql-query-cot.md)         | Examen completo   | Examen completo                                              |                                                              |
| [DEGREES](sql-query-degrees.md) | Búsqueda del índice  | Búsqueda del índice                                             |                                                              |
| [EXP](sql-query-exp.md)         | Examen completo   | Examen completo                                              |                                                              |
| [FLOOR](sql-query-floor.md)     | Búsqueda del índice  | Búsqueda del índice                                             |                                                              |
| [LOG](sql-query-log.md)         | Examen completo   | Examen completo                                              |                                                              |
| [LOG10](sql-query-log10.md)     | Examen completo   | Examen completo                                              |                                                              |
| [PI](sql-query-pi.md)           | N/D         | N/D                                                    | PI() devuelve un valor constante. Dado que el resultado es determinista, las comparaciones con PI() pueden usar el índice. |
| [POWER](sql-query-power.md)     | Examen completo   | Examen completo                                              |                                                              |
| [RADIANS](sql-query-radians.md) | Búsqueda del índice  | Búsqueda del índice                                             |                                                              |
| [RAND](sql-query-rand.md)       | N/D         | N/D                                                    | Rand() devuelve un número aleatorio. Dado que el resultado no es determinista, las comparaciones con Rand() no pueden usar el índice. |
| [ROUND](sql-query-round.md)     | Búsqueda del índice  | Búsqueda del índice                                             |                                                              |
| [SIGN](sql-query-sign.md)       | Búsqueda del índice  | Búsqueda del índice                                             |                                                              |
| [SIN](sql-query-sin.md)         | Examen completo   | Examen completo                                              |                                                              |
| [SQRT](sql-query-sqrt.md)       | Examen completo   | Examen completo                                              |                                                              |
| [SQUARE](sql-query-square.md)   | Examen completo   | Examen completo                                              |                                                              |
| [TAN](sql-query-tan.md)         | Examen completo   | Examen completo                                              |                                                              |
| [TRUNC](sql-query-trunc.md)     | Búsqueda del índice  | Búsqueda del índice                                              |                                                              |
## <a name="next-steps"></a>Pasos siguientes

- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
- [Funciones definidas por el usuario](sql-query-udfs.md)
- [Aggregates](sql-query-aggregate-functions.md) (Agregados)
