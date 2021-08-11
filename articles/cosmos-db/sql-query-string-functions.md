---
title: Funciones de cadena en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información acerca de las funciones del sistema de SQL de cadena en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 6051e659502a9dbccf653a2b5009affe343b9df4
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/27/2021
ms.locfileid: "110539195"
---
# <a name="string-functions-azure-cosmos-db"></a>Funciones de cadena (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Las funciones de cadena le permiten realizar operaciones en cadenas en Azure Cosmos DB.

## <a name="functions"></a>Functions

Las siguientes funciones escalares realizan una operación sobre un valor de entrada de cadena y devuelven una cadena, un valor numérico o un valor booleano. La columna de **uso del índice** supone, si procede, que está comparando la función del sistema de cadena con otro valor con un filtro de igualdad.

| Función del sistema                                 | Uso de índices        | [Uso de índices en consultas con funciones de agregado escalares](index-overview.md#index-utilization-for-scalar-aggregate-functions) | Observaciones                                                      |
| ----------------------------------------------- | ------------------ | ------------------------------------------------------ | ------------------------------------------------------------ |
| [CONCAT](sql-query-concat.md)                   | Examen completo          | Examen completo                                              |                                                              |
| [CONTAINS](sql-query-contains.md)               | Examen completo del índice    | Examen completo                                              |                                                              |
| [ENDSWITH](sql-query-endswith.md)               | Examen completo del índice    | Examen completo                                              |                                                              |
| [INDEX_OF](sql-query-index-of.md)               | Examen completo          | Examen completo                                              |                                                              |
| [LEFT](sql-query-left.md)                       | Examen preciso del índice | Examen preciso del índice                                     |                                                              |
| [LENGTH](sql-query-length.md)                   | Examen completo          | Examen completo                                              |                                                              |
| [LOWER](sql-query-lower.md)                     | Examen completo          | Examen completo                                              |                                                              |
| [LTRIM](sql-query-ltrim.md)                     | Examen completo          | Examen completo                                              |                                                              |
| [REGEXMATCH](sql-query-regexmatch.md)           | Examen completo del índice    | Examen completo                                              |                                                              |
| [REPLACE](sql-query-replace.md)                 | Examen completo          | Examen completo                                              |                                                              |
| [REPLICATE](sql-query-replicate.md)             | Examen completo          | Examen completo                                              |                                                              |
| [REVERSE](sql-query-reverse.md)                 | Examen completo          | Examen completo                                              |                                                              |
| [RIGHT](sql-query-right.md)                     | Examen completo          | Examen completo                                              |                                                              |
| [RTRIM](sql-query-rtrim.md)                     | Examen completo          | Examen completo                                              |                                                              |
| [STARTSWITH](sql-query-startswith.md)           | Examen preciso del índice | Examen preciso del índice                                     | Será Examen ampliado del índice si la opción que no distingue mayúsculas y minúsculas es verdadera. |
| [STRINGEQUALS](sql-query-stringequals.md)       | Búsqueda del índice         | Búsqueda del índice                                             | Será Examen ampliado del índice si la opción que no distingue mayúsculas y minúsculas es verdadera. |
| [StringToArray](sql-query-stringtoarray.md)     | Examen completo          | Examen completo                                              |                                                              |
| [StringToBoolean](sql-query-stringtoboolean.md) | Examen completo          | Examen completo                                              |                                                              |
| [StringToNull](sql-query-stringtonull.md)       | Examen completo          | Examen completo                                              |                                                              |
| [StringToNumber](sql-query-stringtonumber.md)   | Examen completo          | Examen completo                                              |                                                              |

Obtenga información sobre el [uso de índices](index-overview.md#index-usage) en Azure Cosmos DB.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](introduction.md)
- [Funciones definidas por el usuario](sql-query-udfs.md)
- [Aggregates](sql-query-aggregate-functions.md) (Agregados)
