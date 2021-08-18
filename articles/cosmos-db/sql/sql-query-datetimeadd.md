---
title: DateTimeAdd en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información acerca de la función del sistema de SQL DateTimeAdd en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: ae59c4284598a247d4d7da967909ea1164db3887
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206739"
---
# <a name="datetimeadd-azure-cosmos-db"></a>DateTimeAdd (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Devuelve un valor de cadena DateTime resultante de la adición de un valor numérico especificado (como un entero con signo) a una cadena DateTime especificada.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
DateTimeAdd (<DateTimePart> , <numeric_expr> ,<DateTime>)
```

## <a name="arguments"></a>Argumentos
  
*DateTimePart*  
   La parte de la fecha a la que DateTimeAdd agrega un número entero. En esta tabla se enumeran todos los argumentos válidos de DateTimePart.

| DateTimePart | abreviaturas        |
| ------------ | -------------------- |
| Year         | "year", "yyyy", "yy" |
| Month        | "month", "mm", "m"   |
| Día          | "day", "dd", "d"     |
| Hour         | "hour", "hh"         |
| Minute       | "minute", "mi", "n"  |
| Segundo       | "second", "ss", "s"  |
| Millisecond  | "millisecond", "ms"  |
| Microsecond  | "microsecond", "mcs" |
| Nanosecond   | "nanosecond", "ns"   |

*numeric_expr*  
   Es un valor entero con signo que se agregará a DateTimePart del valor DateTime especificado.

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

Devuelve el valor de cadena de fecha y hora UTC ISO 8601 con el formato `YYYY-MM-DDThh:mm:ss.fffffffZ`, donde:
  
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

## <a name="remarks"></a>Observaciones

DateTimeAdd devolverá `undefined` por los siguientes motivos:

- El valor DateTimePart especificado no es válido.
- El valor numeric_expr especificado no es un entero válido.
- El valor DateTime en el argumento o resultado no es un valor DateTime ISO 8601 válido.

## <a name="examples"></a>Ejemplos
  
En el ejemplo siguiente se suma 1 mes a DateTime: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("mm", 1, "2020-07-09T23:20:13.4575530Z") AS OneMonthLater
```

```json
[
    {
        "OneMonthLater": "2020-08-09T23:20:13.4575530Z"
    }
]
```  

En el ejemplo siguiente se restan 2 horas de DateTime: `2020-07-09T23:20:13.4575530Z`

```sql
SELECT DateTimeAdd("hh", -2, "2020-07-09T23:20:13.4575530Z") AS TwoHoursEarlier
```

```json
[
    {
        "TwoHoursEarlier": "2020-07-09T21:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de fecha y hora (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
