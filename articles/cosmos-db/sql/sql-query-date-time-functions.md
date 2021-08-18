---
title: Funciones de fecha y hora en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre las funciones del sistema de fecha y hora de SQL en Azure Cosmos DB para realizar operaciones de fecha y hora y marca de tiempo.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2ca024ae9de0f4bd31a56e90dac7659219356108
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122206671"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funciones de fecha y hora (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Las funciones de fecha y hora permiten realizar operaciones de fecha y hora y marca de tiempo en Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funciones para obtener la fecha y hora

Las siguientes funciones escalares permiten obtener la fecha y hora UTC actual en tres formas: una cadena que se ajusta al formato ISO 8601, una marca de tiempo numérica cuyo valor es el número de milisegundos que han transcurrido desde la época de Unix o tics numéricos cuyo valor es el número de tics de 100 nanosegundos que han transcurrido desde la época de Unix:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Funciones para trabajar con valores DateTime

Las siguientes funciones permiten manipular fácilmente valores DateTime, de marca de tiempo y de tic:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)
* [DateTimeToTimestamp](sql-query-datetimetotimestamp.md)
* [TicksToDateTime](sql-query-tickstodatetime.md)
* [TimestampToDateTime](sql-query-timestamptodatetime.md)

## <a name="next-steps"></a>Pasos siguientes

- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](../introduction.md)
- [Funciones definidas por el usuario](sql-query-udfs.md)
- [Aggregates](sql-query-aggregate-functions.md) (Agregados)
