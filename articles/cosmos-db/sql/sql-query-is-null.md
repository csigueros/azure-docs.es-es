---
title: IS_NULL en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información acerca de la función del sistema de SQL IS_NULL en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 330a94b233a50002e3f65482bac2da29cb9379cb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206729"
---
# <a name="is_null-azure-cosmos-db"></a>IS_NULL (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve un valor booleano que indica si el tipo de la expresión especificada es nulo.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
IS_NULL(<expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*expr*  
   Es cualquier expresión.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión condicional.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se comprueban objetos de los tipos JSON booleano, numérico, cadena, null, objeto, matriz y undefined mediante la función `IS_NULL`.  
  
```sql
SELECT   
    IS_NULL(true) AS isNull1,   
    IS_NULL(1) AS isNull2,  
    IS_NULL("value") AS isNull3,   
    IS_NULL(null) AS isNull4,  
    IS_NULL({prop: "value"}) AS isNull5,   
    IS_NULL([1, 2, 3]) AS isNull6,  
    IS_NULL({prop: "value"}.prop2) AS isNull7  
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"isNull1":false,"isNull2":false,"isNull3":false,"isNull4":true,"isNull5":false,"isNull6":false,"isNull7":false}]
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema se beneficiará de un [índice de intervalo](../index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de comprobación de tipos de Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
