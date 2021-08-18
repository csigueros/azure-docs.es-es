---
title: TAN en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre la función del sistema SQL TAN en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 84731817f5ec2ab73c9a8089554508f39db35c70
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206693"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve la tangente del ángulo especificado, en radianes, en la expresión especificada.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   Es una expresión numérica.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se calcula la tangente de pi()/2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema no usará el índice.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones matemáticas (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
