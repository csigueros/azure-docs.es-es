---
title: 'Azure Cosmos DB: API, SDK y recursos de .NET para SQL'
description: Obtenga toda la información sobre la API y el SDK de .NET para SQL, incluidas la fechas de lanzamiento, fechas de retirada y cambios realizados entre las versiones del SDK para .NET de Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/11/2021
ms.author: anfeldma
ms.custom: devx-track-dotnet
ms.openlocfilehash: a52d3e5690a043e3f886dd0a87db5ffba155528f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493369"
---
# <a name="azure-cosmos-db-net-sdk-v2-for-sql-api-download-and-release-notes-deprecated"></a>SDK de Azure Cosmos DB para .NET v2 para SQL API: descarga y notas de la versión (en desuso)
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
> * [REST](/rest/api/cosmos-db/)
> * [Proveedor de recursos de REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-query-getting-started.md)
> * [Bulk Executor: .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor: Java](sql-api-sdk-bulk-executor-java.md)

| | Vínculos |
|---|---|
|**Descarga del SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Documentación de la API**|[Documentación de referencia de API de .NET](/dotnet/api/overview/azure/cosmosdb)|
|**Muestras**|[Ejemplos de código de .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Introducción**|[Introducción al SDK para .NET de Azure Cosmos DB](sql-api-get-started.md)|
|**Tutorial de la aplicación web**|[Desarrollo de aplicaciones web con Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Plataforma admitida actualmente**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

> [!IMPORTANT]
> El 31 de agosto de 2024 se retirará el SDK v2.x para .NET de Azure Cosmos DB; el SDK y todas las aplicaciones que lo usan seguirán funcionando; Azure Cosmos DB simplemente dejará de proporcionar mantenimiento y soporte técnico para este SDK. Se recomienda seguir estas instrucciones para migrar a la versión más reciente del SDK v3 para .NET.
>

> [!NOTE]
> Si usa .NET Framework, consulte la versión 3.x más reciente del [SDK de .NET](sql-api-sdk-dotnet-standard.md), que tiene como destino .NET Standard.

## <a name="release-history"></a><a name="release-history"></a> Historial de versiones

El historial de versiones se mantiene en el repositorio de origen del SDK de .NET de Azure Cosmos DB. Para obtener una lista detallada de las versiones de características y los errores corregidos en cada versión, consulte la [documentación del registro de cambios del SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)

Dado que la versión 3 del SDK de .NET de Azure Cosmos DB incluye características actualizadas y un rendimiento mejorado, la versión 2.x de este SDK se retirará el 31 de agosto de 2024.Debe actualizar el SDK a la versión 3 en esa fecha. Se recomienda seguir las instrucciones anteriores para migrar al SDK de .NET v3 de Azure Cosmos DB.

## <a name="recommended-version"></a><a name="recommended-version"></a>Versión recomendada

Hay diferentes versiones secundarias de los SDK de .NET están disponibles en la versión 2.x.x. **La versión mínima recomendada es de 2.16.2**.

## <a name="known-issues"></a><a name="known-issues"></a> Problemas conocidos

A continuación se muestra una lista de los problemas conocidos que afectan a la [versión mínima recomendada](#recommended-version):

| Incidencia | Impacto | Mitigación | Vínculo de seguimiento |
| --- | --- | --- | --- |
| Al usar el modo directo con una cuenta con varias ubicaciones de escritura, es posible que el SDK no detecte cuándo se agrega una región a la cuenta. No se puede iniciar el proceso en segundo plano que [actualiza la información de la cuenta](troubleshoot-sdk-availability.md#adding-a-region-to-an-account). |Si se agrega una nueva región a la cuenta que forma parte de PreferredLocations en un orden superior al de la región actual, el SDK no detectará la nueva región disponible. |Reinicie la aplicación. |https://github.com/Azure/azure-cosmos-dotnet-v2/issues/852 |

## <a name="faq"></a>Preguntas más frecuentes

[!INCLUDE [cosmos-db-sdk-faq](../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Consulte también

Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).
