---
title: Solución de problemas con consultas de diagnóstico avanzadas (SQL API)
titleSuffix: Azure Cosmos DB
description: Aprenda a consultar los registros de diagnóstico para solucionar problemas de los datos almacenados en Azure Cosmos DB - SQL API
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
ms.openlocfilehash: f45aa0833a7e2f2dd32943c28bfe677e03e93edd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733176"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-sql-core-api"></a>Solución de problemas con consultas de diagnóstico avanzadas para SQL API (Core)

[!INCLUDE[appliesto-all-apis-except-table](includes/appliesto-all-apis-except-table.md)]sdf

> [!div class="op_single_selector"]
> * [SQL API (Core)](cosmos-db-advanced-queries.md)
> * [MongoDB API](mongodb/diagnostic-queries-mongodb.md)
> * [Cassandra API](cassandra/diagnostic-queries-cassandra.md)
> * [Gremlin API](queries-gremlin.md)
>

En este artículo, explicaremos cómo escribir consultas más avanzadas para ayudar a solucionar problemas relacionados con la cuenta de Azure Cosmos DB mediante registros de diagnóstico enviados a las tablas **AzureDiagnostics (heredada)** y **específicas del recurso (versión preliminar)** .

En el caso de las tablas de Azure Diagnostics, todos los datos se escriben en una sola tabla y los usuarios tendrán que especificar la categoría que desean consultar. Si quiere ver la consulta de texto completo de la solicitud, [siga este artículo](cosmosdb-monitor-resource-logs.md#full-text-query) para aprender cómo habilitar esta característica.

En el caso de las [tablas específicas del recurso](cosmosdb-monitor-resource-logs.md#create-setting-portal), los datos se escriben en tablas individuales para cada categoría del recurso. Recomendamos este modo, ya que facilita el trabajo con los datos, proporciona una mejor detectabilidad de los esquemas y mejora el rendimiento tanto en la latencia de ingesta como en los tiempos de consulta.

## <a name="common-queries"></a>Consultas comunes

- Principales N(10) consultas ordenadas por consumo de unidades de solicitud en un período de tiempo determinado

# <a name="resource-specific"></a>[Específico del recurso](#tab/resource-specific)

   ```Kusto
   let topRequestsByRUcharge = CDBDataPlaneRequests 
   | where TimeGenerated > ago(24h)
   | project  RequestCharge , TimeGenerated, ActivityId;
   CDBQueryRuntimeStatistics
   | project QueryText, ActivityId, DatabaseName , CollectionName
   | join kind=inner topRequestsByRUcharge on ActivityId
   | project DatabaseName , CollectionName , QueryText , RequestCharge, TimeGenerated
   | order by RequestCharge desc
   | take 10
   ```
# <a name="azure-diagnostics"></a>[Diagnóstico de Azure](#tab/azure-diagnostics)

   ```Kusto
   let topRequestsByRUcharge = AzureDiagnostics
   | where Category == "DataPlaneRequests" and TimeGenerated > ago(24h)
   | project  requestCharge_s , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   | project querytext_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner topRequestsByRUcharge on activityId_g
   | project databasename_s , collectionname_s , querytext_s , requestCharge_s, TimeGenerated
   | order by requestCharge_s desc
   | take 10
   ```    
---

- Solicitudes limitadas (statusCode = 429) en un período de tiempo determinado 

# <a name="resource-specific"></a>[Específico del recurso](#tab/resource-specific)

   ```Kusto
   let throttledRequests = CDBDataPlaneRequests
   | where StatusCode == "429"
   | project  OperationName , TimeGenerated, ActivityId;
   CDBQueryRuntimeStatistics
   | project QueryText, ActivityId, DatabaseName , CollectionName
   | join kind=inner throttledRequests on ActivityId
   | project DatabaseName , CollectionName , QueryText , OperationName, TimeGenerated
   ```
# <a name="azure-diagnostics"></a>[Diagnóstico de Azure](#tab/azure-diagnostics)

   ```Kusto
   let throttledRequests = AzureDiagnostics
   | where Category == "DataPlaneRequests" and statusCode_s == "429"
   | project  OperationName , TimeGenerated, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   | project querytext_s, activityId_g, databasename_s , collectionname_s
   | join kind=inner throttledRequests on activityId_g
   | project databasename_s , collectionname_s , querytext_s , OperationName, TimeGenerated
   ```    
---

- Consultas con las longitudes de respuesta más grandes (tamaño de carga de la respuesta del servidor)

# <a name="resource-specific"></a>[Específico del recurso](#tab/resource-specific)

   ```Kusto
   let operationsbyUserAgent = CDBDataPlaneRequests
   | project OperationName, DurationMs, RequestCharge, ResponseLength, ActivityId;
   CDBQueryRuntimeStatistics
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on ActivityId
   | summarize max(ResponseLength) by QueryText
   | order by max_ResponseLength desc
   ```
# <a name="azure-diagnostics"></a>[Diagnóstico de Azure](#tab/azure-diagnostics)

   ```Kusto
   let operationsbyUserAgent = AzureDiagnostics
   | where Category=="DataPlaneRequests"
   | project OperationName, duration_s, requestCharge_s, responseLength_s, activityId_g;
   AzureDiagnostics
   | where Category == "QueryRuntimeStatistics"
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   | join kind=inner operationsbyUserAgent on activityId_g
   | summarize max(responseLength_s1) by querytext_s
   | order by max_responseLength_s1 desc
   ```    
---

- Consumo de RU por partición física (en todas las réplicas del conjunto de réplicas)

# <a name="resource-specific"></a>[Específico del recurso](#tab/resource-specific)

   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by toint(PartitionKeyRangeId)
   | render columnchart
   ```
# <a name="azure-diagnostics"></a>[Diagnóstico de Azure](#tab/azure-diagnostics)

   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by toint(partitionKeyRangeId_s)
   | render columnchart  
   ```    
---

- Consumo de RU por partición lógica (en todas las réplicas del conjunto de réplicas)

# <a name="resource-specific"></a>[Específico del recurso](#tab/resource-specific)

   ```Kusto
   CDBPartitionKeyRUConsumption
   | where TimeGenerated >= now(-1d)
   //specify collection and database
   //| where DatabaseName == "DBNAME" and CollectionName == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(RequestCharge)) by PartitionKey, PartitionKeyRangeId
   | render columnchart  
   ```
# <a name="azure-diagnostics"></a>[Diagnóstico de Azure](#tab/azure-diagnostics)

   ```Kusto
   AzureDiagnostics
   | where TimeGenerated >= now(-1d)
   | where Category == 'PartitionKeyRUConsumption'
   //specify collection and database
   //| where databasename_s == "DBNAME" and collectioname_s == "COLLECTIONNAME"
   // filter by operation type
   //| where operationType_s == 'Create'
   | summarize sum(todouble(requestCharge_s)) by partitionKey_s, partitionKeyRangeId_s
   | render columnchart  
   ```
---

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre cómo crear una configuración de diagnóstico para Cosmos DB, consulte el artículo sobre cómo [crear una configuración de diagnóstico](cosmosdb-monitor-resource-logs.md).

* Para obtener información detallada sobre cómo crear una configuración de diagnóstico mediante Azure Portal, la CLI o PowerShell, consulte el artículo [Creación de una configuración de diagnóstico para recopilar registros de plataforma y métricas en Azure](../azure-monitor/essentials/diagnostic-settings.md).
