---
title: DateTimeToTimestamp en lenguaje de consulta de Azure Cosmos DB
description: Más información sobre la función del sistema SQL DateTimeToTimestamp en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 27cee804d8c16cd3a3e2af1ac79a69c6cfd4de67
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206710"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Convierte el valor de DateTime especificado en una marca de tiempo.
  
## <a name="syntax"></a>Sintaxis
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>Argumentos

*DateTime*  
   Valor de cadena de fecha y hora UTC ISO 8601 con el formato `YYYY-MM-DDThh:mm:ss.fffffffZ`, donde:
  
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

## <a name="return-types"></a>Tipos de valores devueltos

Devuelve un valor numérico con signo, el número actual de milisegundos que han transcurrido desde la época de Unix, es decir, el número de milisegundos que han transcurrido desde las 00:00:00 del jueves, 1 de enero de 1970.

## <a name="remarks"></a>Observaciones

DateTimeToTimestamp devolverá `undefined` si el valor de DateTime especificado no es válido.

## <a name="examples"></a>Ejemplos
  
En el siguiente ejemplo, DateTime se convierte en una marca de tiempo:

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

Este es otro ejemplo:

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de fecha y hora (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
