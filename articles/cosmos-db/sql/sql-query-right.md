---
title: RIGHT en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información acerca de la función del sistema de SQL RIGHT en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 89d69945a392aa1bde712d957b5121e560770b05
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206777"
---
# <a name="right-azure-cosmos-db"></a>RIGHT (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve la parte derecha de una cadena con el número especificado de caracteres.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   Es la expresión de cadena de la que se van a extraer caracteres.  
  
*num_expr*  
   Es una expresión numérica que especifica el número de caracteres.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión de cadena.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se devuelve la parte derecha de "abc" para distintos valores de longitud.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema no usará el índice.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de cadena Azure Cosmos DB](sql-query-string-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
