---
title: Funciones del sistema en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información acerca de las funciones del sistema de SQL integradas y definidas por el usuario en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e15d27b966f08dce8de5369e3ed8fbd7465b4c86
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206824"
---
# <a name="system-functions-azure-cosmos-db"></a>Funciones del sistema (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

 Cosmos DB proporciona muchas funciones de SQL integradas. Las categorías de las funciones integradas aparecen a continuación.  
  
|Grupo de funciones|Descripción|Operaciones|  
|--------------|-----------------|-----------------| 
|[Funciones de matriz](sql-query-array-functions.md)|Las siguientes funciones de matriz realizan una operación en un valor de entrada de matriz y devuelven un valor numérico, booleano o de matriz. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Funciones de fecha y hora](sql-query-date-time-functions.md)|Las funciones de fecha y hora le permiten obtener la fecha y la hora UTC actuales de dos formas: una marca de tiempo numérica cuyo valor es la época de Unix en milisegundos, o una cadena que se ajusta al formato ISO 8601. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md), [GetCurrentTicks](sql-query-getcurrentticks.md) |
|[Funciones matemáticas](sql-query-mathematical-functions.md)|Las funciones matemáticas realizan un cálculo, basado normalmente en valores de entrada proporcionados como argumentos, y devuelven un valor numérico. | [ABS](sql-query-abs.md), [ACOS](sql-query-acos.md), [ASIN](sql-query-asin.md), [ATAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [CEILING](sql-query-ceiling.md), [COS](sql-query-cos.md), [COT](sql-query-cot.md), [DEGREES](sql-query-degrees.md), [EXP](sql-query-exp.md), [FLOOR](sql-query-floor.md), [LOG](sql-query-log.md), [LOG10](sql-query-log10.md), [PI](sql-query-pi.md), [POWER](sql-query-power.md), [RADIANS](sql-query-radians.md), [RAND](sql-query-rand.md), [ROUND](sql-query-round.md), [SIGN](sql-query-sign.md), [SIN](sql-query-sin.md), [SQRT](sql-query-sqrt.md), [SQUARE](sql-query-square.md), [TAN](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Funciones espaciales](sql-query-spatial-functions.md)|Las siguientes funciones espaciales realizan una operación en un valor de entrada de objeto espacial y devuelven un valor numérico o booleano. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Funciones de cadena](sql-query-string-functions.md)|Las siguientes funciones de cadena realizan una operación sobre un valor de entrada de cadena y devuelven una cadena, un valor numérico o un booleano. | [CONCAT](sql-query-concat.md), [CONTAINS](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [LEFT](sql-query-left.md), [LENGTH](sql-query-length.md), [LOWER](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [REGEXMATCH](sql-query-regexmatch.md)[REPLACE](sql-query-replace.md), [REPLICATE](sql-query-replicate.md), [REVERSE](sql-query-reverse.md), [RIGHT](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), [StringToBoolean](sql-query-stringtoboolean.md), [StringToNull](sql-query-stringtonull.md), [StringToNumber](sql-query-stringtonumber.md), [StringToObject](sql-query-stringtoobject.md), [SUBSTRING](sql-query-substring.md), [ToString](sql-query-tostring.md), [TRIM](sql-query-trim.md), [UPPER](sql-query-upper.md) |
|[Funciones de comprobación de tipos](sql-query-type-checking-functions.md)|Las funciones de comprobación de tipos permiten comprobar el tipo de una expresión dentro de consultas SQL. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Funciones integradas frente a funciones definidas por el usuario (UDF)

Si actualmente usa una función definida por el usuario (UDF) para la que ahora hay disponible una función integrada, la función integrada correspondiente será más rápida de ejecutar y más eficaz.

## <a name="built-in-versus-ansi-sql-functions"></a>Funciones integradas frente a funciones de ANSI SQL

La diferencia principal entre las funciones de Cosmos DB y las de ANSI SQL es que las primeras están diseñadas para funcionar bien con datos sin esquemas y con datos de esquemas mixtos. Por ejemplo, si falta una propiedad o tiene un valor no numérico, como `undefined`, se omite el elemento en lugar de devolver un error.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a Azure Cosmos DB](../introduction.md)
- [Funciones de matriz](sql-query-array-functions.md)
- [Funciones de fecha y hora](sql-query-date-time-functions.md)
- [Funciones matemáticas](sql-query-mathematical-functions.md)
- [Funciones espaciales](sql-query-spatial-functions.md)
- [Funciones de cadena](sql-query-string-functions.md)
- [Funciones de comprobación de tipos](sql-query-type-checking-functions.md)
- [Funciones definidas por el usuario](sql-query-udfs.md)
- [Aggregates](sql-query-aggregate-functions.md) (Agregados)
