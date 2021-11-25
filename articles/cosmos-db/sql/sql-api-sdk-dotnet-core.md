---
title: 'Azure Cosmos DB: API, SDK y recursos de .NET Core para SQL'
description: Aprenda todo lo necesario sobre el SDK y la API de .NET Core para SQL como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios realizados de una versión a otra del SDK para .NET Core de Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/11/2021
ms.author: anfeldma
ms.custom: devx-track-dotnet
ms.openlocfilehash: 60715fd9f3aaebec07e14350107ad2b3037d0171
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487674"
---
# <a name="azure-cosmos-db-net-core-sdk-v2-for-sql-api-release-notes-and-resources"></a>SDK de .NET Core de Azure Cosmos DB v2 para SQL API: Notas de la versión y recursos
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [SDK de .NET v2](sql-api-sdk-dotnet.md)
> * [SDK de .NET Core v2](sql-api-sdk-dotnet-core.md)
> * [SDK de fuente de cambios de .NET, versión 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [SDK de Java v4](sql-api-sdk-java-v4.md)
> * [Versión 2 del SDK de Java asincrónico](sql-api-sdk-async-java.md)
> * [SDK de Java v2 sincrónico](sql-api-sdk-java.md)
> * [Spring Data v2](sql-api-sdk-java-spring-v2.md)
> * [Spring Data v3](sql-api-sdk-java-spring-v3.md)
> * [Conector Spark 3 OLTP](sql-api-sdk-java-spark-v3.md)
> * [Conector Spark 2 OLTP](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api
> * [Proveedor de recursos de REST](/azure/azure-resource-manager/management/azure-services-resource-providers)
> * [SQL](sql-query-getting-started.md)
> * [Bulk Executor: .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor: Java](sql-api-sdk-bulk-executor-java.md)

| | Vínculos |
|---|---|
|**Descarga del SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**Documentación de la API**|[Documentación de referencia de API de .NET](/dotnet/api/overview/azure/cosmosdb)|
|**Muestras**|[Ejemplos de código de .NET](sql-api-dotnet-samples.md)|
|**Introducción**|[Introducción a .NET de Azure Cosmos DB](sql-api-sdk-dotnet.md)|
|**Tutorial de la aplicación web**|[Desarrollo de aplicaciones web con Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Plataforma admitida actualmente**|[.NET Standard 1.6 y .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

> [!NOTE]
> Si usa .NET Core, consulte la versión 3. x más reciente del [SDK de .NET](sql-api-sdk-dotnet-standard.md), que tiene como destino .NET Standard.

## <a name="release-history"></a><a name="release-history"></a>Historial de versiones

El historial de versiones se mantiene en el repositorio de origen del SDK de .NET de Azure Cosmos DB. Para obtener una lista detallada de las versiones de características y los errores corregidos en cada versión, consulte la [documentación del registro de cambios del SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md).

Dado que la versión 3 del SDK de .NET de Azure Cosmos DB incluye características actualizadas y un rendimiento mejorado, la versión 2.x de este SDK se retirará el 31 de agosto de 2024.Debe actualizar el SDK a la versión 3 en esa fecha. Se recomienda seguir las instrucciones anteriores para migrar al SDK de .NET v3 de Azure Cosmos DB.

## <a name="recommended-version"></a><a name="recommended-version"></a>Versión recomendada

Hay diferentes versiones secundarias de los SDK de .NET están disponibles en la versión 2.x.x: **La versión mínima recomendada es de 2.16.2**.

## <a name="known-issues"></a><a name="known-issues"></a> Problemas conocidos

A continuación se muestra una lista de los problemas conocidos que afectan a la [versión mínima recomendada](#recommended-version):

| Incidencia | Impacto | Mitigación | Vínculo de seguimiento |
| --- | --- | --- | --- |
| Al usar el modo directo con una cuenta con varias ubicaciones de escritura, es posible que el SDK no detecte cuándo se agrega una región a la cuenta. No se puede iniciar el proceso en segundo plano que [actualiza la información de la cuenta](troubleshoot-sdk-availability.md#adding-a-region-to-an-account). |Si se agrega una nueva región a la cuenta que forma parte de PreferredLocations en un orden superior al de la región actual, el SDK no detectará la nueva región disponible. |Reinicie la aplicación. |https://github.com/Azure/azure-cosmos-dotnet-v2/issues/852 |

## <a name="see-also"></a>Consulte también

Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

