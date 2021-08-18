---
title: ARRAY_LENGTH en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre cómo la función del sistema SQL ARRAY_LENGTH en Azure Cosmos DB devuelve el número de elementos de la expresión de matriz especificada.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04db916a95fe12b7b4f52f00adce093eeee589cc
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206757"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve el número de elementos de la expresión de matriz especificada.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   Es una expresión de matriz.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
  En el siguiente ejemplo se muestra cómo obtener la longitud de una matriz con `ARRAY_LENGTH`.  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Observaciones

Esta función del sistema no usará el índice.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de matriz (Azure Cosmos DB)](sql-query-array-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
