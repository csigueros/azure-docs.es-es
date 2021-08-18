---
title: AVG en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre la función del sistema de SQL Average (AVG) en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 9683d4fa138bba5f7427dcf3c8f6fad013e3881f
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206617"
---
# <a name="avg-azure-cosmos-db"></a>AVG (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Esta función de agregado devuelve la media de los valores de la expresión.
  
## <a name="syntax"></a>Sintaxis
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   Es una expresión numérica.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
El siguiente ejemplo devuelve el valor medio de `propertyA`:
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema se beneficiará de un [índice de intervalo](../index-policy.md#includeexclude-strategy). Si algún argumento de `AVG` es una cadena, un valor booleano o un valor null, toda la función del sistema de agregado devolverá `undefined`. Si algún argumento tiene un valor `undefined`, no afectará al cálculo de `AVG`.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones matemáticas en Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Funciones del sistema en Azure Cosmos DB](sql-query-system-functions.md)
- [Funciones de agregado en Azure Cosmos DB](sql-query-aggregate-functions.md)