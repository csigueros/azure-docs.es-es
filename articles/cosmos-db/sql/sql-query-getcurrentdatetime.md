---
title: GetCurrentDateTime en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre la función del sistema SQL GetCurrentDateTime en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 323986d06ec956ee891d0b4831415d68bc59700a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206848"
---
# <a name="getcurrentdatetime-azure-cosmos-db"></a>GetCurrentDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Devuelve la fecha y hora actual de la hora UTC (hora universal coordinada) como una cadena ISO 8601.
  
## <a name="syntax"></a>Sintaxis
  
```sql
GetCurrentDateTime ()
```

## <a name="return-types"></a>Tipos de valores devueltos
  
Devuelve el valor de la cadena de fecha y hora UTC actuales ISO 8601 con el formato `YYYY-MM-DDThh:mm:ss.fffffffZ`, donde:
  
|Formato|Descripción|
|-|-|
|AAAA|año con cuatro dígitos|
|MM|mes con dos dígitos (01 = enero, etc.)|
|DD|día con dos dígitos del mes (de 01 a 31)|
|T|indica el comienzo de los elementos de hora|
|hh|hora con dos dígitos (de 00 a 23)|
|MM|minutos con dos dígitos (de 00 a 59)|
|ss|segundos con dos dígitos (de 00 a 59)|
|.fffffff|fracciones de segundos de siete dígitos|
|Z|Indicador de UTC (hora universal coordinada)|
  
  Para más información sobre el formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Observaciones

GetCurrentDateTime() es una función no determinista. El resultado devuelto es UTC. La precisión es de siete dígitos, con una precisión de 100 nanosegundos.

> [!NOTE]
> Esta función del sistema no usará el índice. Si necesita comparar valores con la hora actual, obtenga esta antes de la ejecución de la consulta y use ese valor de cadena constante en la cláusula `WHERE`.

## <a name="examples"></a>Ejemplos
  
El ejemplo siguiente muestra cómo obtener la fecha y hora UTC actuales mediante la función integrada GetCurrentDateTime().
  
```sql
SELECT GetCurrentDateTime() AS currentUtcDateTime
```  
  
 Este es un conjunto de resultados de ejemplo.
  
```json
[{
  "currentUtcDateTime": "2019-05-03T20:36:17.1234567Z"
}]  
```  

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de fecha y hora (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
