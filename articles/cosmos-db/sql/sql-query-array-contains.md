---
title: ARRAY_CONTAINS en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre cómo la función del sistema SQL Array Contains en Azure Cosmos DB devuelve un valor booleano que indica si la matriz contiene el valor especificado
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 027d4b5a846f95bc16ae0592790ebfd4492d0de5
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206712"
---
# <a name="array_contains-azure-cosmos-db"></a>ARRAY_CONTAINS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Devuelve un valor booleano que indica si la matriz contiene el valor especificado. Puede buscar una coincidencia parcial o total de un objeto mediante el uso de una expresión booleana dentro del comando. 

## <a name="syntax"></a>Sintaxis
  
```sql
ARRAY_CONTAINS (<arr_expr>, <expr> [, bool_expr])  
```  
  
## <a name="arguments"></a>Argumentos
  
*arr_expr*  
   Es la expresión de matriz que se va a buscar.  
  
*expr*  
   Es la expresión que se va a buscar.  

*bool_expr*  
   Es una expresión booleana. Si se evalúa en ' true' y el valor de búsqueda especificado es un objeto, el comando busca una coincidencia parcial (el objeto de búsqueda es un subconjunto de uno de los objetos). Si se evalúa en 'false', el comando busca una coincidencia total de todos los objetos dentro de la matriz. Si no se especifica, el valor predeterminado es 'false'. 
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve un valor booleano.  
  
## <a name="examples"></a>Ejemplos
  
  En el siguiente ejemplo se muestra cómo comprobar la pertenencia a una matriz con `ARRAY_CONTAINS`.  
  
```sql
SELECT   
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "apples") AS b1,  
           ARRAY_CONTAINS(["apples", "strawberries", "bananas"], "mangoes") AS b2  
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"b1": true, "b2": false}]  
```  

En el siguiente ejemplo se indica cómo buscar una coincidencia parcial de JSON en una matriz mediante ARRAY_CONTAINS.  
  
```sql
SELECT  
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}, true) AS b1, 
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "apples"}) AS b2,
    ARRAY_CONTAINS([{"name": "apples", "fresh": true}, {"name": "strawberries", "fresh": true}], {"name": "mangoes"}, true) AS b3 
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{
  "b1": true,
  "b2": false,
  "b3": false
}]
```

## <a name="remarks"></a>Observaciones

Esta función del sistema se beneficiará de un [índice de intervalo](../index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de matriz (Azure Cosmos DB)](sql-query-array-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
