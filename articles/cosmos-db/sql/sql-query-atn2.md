---
title: ATN2 en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre cómo la función del sistema SQL ATN2 en Azure Cosmos DB devuelve el valor principal del arco tangente de y/x, expresado en radianes
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b2e24960686d176d5445553d56541b3e6b42cd09
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206646"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve el valor principal de la arcotangente de y/x, expresado en radianes.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   Es una expresión numérica.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
  En el siguiente ejemplo se calcula ATN2 para los componentes x e y especificados.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema no usará el índice.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones matemáticas (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
