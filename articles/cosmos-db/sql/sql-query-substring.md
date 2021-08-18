---
title: SUBSTRING en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información acerca de la función del sistema de SQL SUBSTRING en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c4df749de8c80b4b81410693c848b8a3c669f77d
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206656"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve parte de una expresión de cadena a partir de la posición de base cero del carácter especificado y continúa hasta la longitud especificada, o hasta el final de la cadena.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   Es una expresión de cadena.
  
*num_expr1*  
   Expresión numérica válida que indica el carácter inicial. Un valor de 0 es el primer carácter de *str_expr*.
  
*num_expr2*  
   Expresión numérica que indica el número máximo de caracteres de *str_expr* que se va a devolver. Un valor de 0 o menos da como resultado una cadena vacía.

## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión de cadena.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se devuelve la subcadena de "abc" que empieza por 1 y tiene una longitud de 1 carácter.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Observaciones

Esta función del sistema se beneficiará de un [índice de intervalo](../index-policy.md#includeexclude-strategy) si la posición inicial es `0`.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de cadena Azure Cosmos DB](sql-query-string-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
