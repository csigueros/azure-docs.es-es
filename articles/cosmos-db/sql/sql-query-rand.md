---
title: RAND en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información acerca de la función del sistema de SQL RAND en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cd903a6f067c116277a7ec3c2d7d01ce8e2dedad
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206749"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve un valor numérico generado de forma aleatoria a partir de [0,1).
 
## <a name="syntax"></a>Sintaxis
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Tipos de valores devueltos

  Devuelve una expresión numérica.

## <a name="remarks"></a>Observaciones

  `RAND` es una función no determinista. Las llamadas repetitivas de `RAND` no devuelven los mismos resultados. Esta función del sistema no usará el índice.


## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente, se devuelve un valor numérico generado de forma aleatoria.
  
```sql
SELECT RAND() AS rand 
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="next-steps"></a>Pasos siguientes

- [Funciones matemáticas (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
