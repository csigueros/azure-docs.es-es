---
title: SUM en el lenguaje de consulta de Azure Cosmos DB
description: Aprenda sobre la función del sistema de SQL Sum (SUM) en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 77f00474a53b3fa068989a111517dc05806a1ed4
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206795"
---
# <a name="sum-azure-cosmos-db"></a>SUM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Esta función de agregado devuelve la suma de todos los valores de la expresión.
  
## <a name="syntax"></a>Sintaxis
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   Es una expresión numérica.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
El siguiente ejemplo devuelve la suma de `propertyA`:
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema se beneficiará de un [índice de intervalo](../index-policy.md#includeexclude-strategy). Si algún argumento de `SUM` es una cadena, un valor booleano o un valor null, toda la función del sistema de agregado devolverá `undefined`. Si algún argumento tiene un valor `undefined`, no afectará al cálculo de `SUM`.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones matemáticas en Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciones del sistema en Azure Cosmos DB](sql-query-system-functions.md)
- [Funciones de agregado en Azure Cosmos DB](sql-query-aggregate-functions.md)