---
title: DEGREES en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre la función del sistema SQL DEGREES en Azure Cosmos DB que devuelve el ángulo correspondiente en grados para un ángulo especificado en radianes
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 48af54f33e76b336cf3facbaa44b1025392b419e
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206669"
---
# <a name="degrees-azure-cosmos-db"></a>DEGREES (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve el ángulo correspondiente en grados de un ángulo especificado en radianes.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   Es una expresión numérica.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se devuelven los grados de un ángulo de pi/2 radianes.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema no usará el índice.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones matemáticas (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
