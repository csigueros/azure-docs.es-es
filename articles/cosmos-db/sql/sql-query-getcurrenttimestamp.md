---
title: GetCurrentTimestamp en lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre la función del sistema SQL GetCurrentTimestamp en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 4ddb01175f0affad4e2ed3c441e7fa3919b6f174
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206705"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Devuelve el número de milisegundos que han transcurrido desde las 00:00:00 del jueves, 1 de enero de 1970.
  
## <a name="syntax"></a>Sintaxis
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
Devuelve un valor numérico con signo, el número actual de milisegundos que han transcurrido desde la época de Unix, es decir, el número de milisegundos que han transcurrido desde las 00:00:00 del jueves, 1 de enero de 1970.

## <a name="remarks"></a>Observaciones

GetCurrentTimestamp() es una función no determinista. El resultado devuelto está en UTC (hora universal coordinada).

> [!NOTE]
> Esta función del sistema no usará el índice. Si necesita comparar valores con la hora actual, obtenga esta antes de la ejecución de la consulta y use ese valor de cadena constante en la cláusula `WHERE`.

## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se muestra cómo obtener la marca de tiempo actual mediante la función integrada GetCurrentTimestamp().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Este es un conjunto de resultados de ejemplo.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de fecha y hora (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
