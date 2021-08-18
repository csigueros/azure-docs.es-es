---
title: DateTimeFromParts en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información acerca de la función del sistema de SQL DateTimeFromParts en Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 4cf613b7ab58b22df9db613ebfadcbf9b0aad91a
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206869"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Devuelve un valor DateTime de cadena construido a partir de los valores de entrada.
  
## <a name="syntax"></a>Sintaxis
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Argumentos
  
*numberYear* Valor entero del año en el formato `YYYY`

*numberMonth*  
   Valor entero del mes en el formato `MM`

*numberDay*  
   Valor entero del día en el formato `DD`

*numberHour* (opcional) Valor entero de la hora en el formato `hh`

*numberMinute* (opcional) Valor entero del minuto en el formato `mm`

*numberSecond* (opcional) Valor entero del segundo con el formato `ss`

*numberOfFractionsOfSecond* (opcional) Valor entero de la fracción de segundo con el formato `.fffffff`

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
  
 Para más información sobre el formato ISO 8601, consulte [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Observaciones

Si los enteros especificados creasen un valor DateTime no válido, DateTimeFromParts devolverá `undefined`.

Si no se especifica un argumento opcional, su valor será de 0.

## <a name="examples"></a>Ejemplos

Este es un ejemplo que solo incluye los argumentos necesarios para construir un valor DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Este es otro ejemplo que también usa algunos argumentos opcionales para construir un valor DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Este es otro ejemplo que también usa todos los argumentos opcionales para construir un valor DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de fecha y hora (Azure Cosmos DB)](sql-query-date-time-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
