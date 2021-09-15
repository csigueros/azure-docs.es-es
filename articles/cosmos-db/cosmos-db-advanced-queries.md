---
title: Solución de problemas con consultas de diagnóstico avanzadas (SQL API)
titleSuffix: Azure Cosmos DB
description: Aprenda a consultar los registros de diagnóstico para solucionar problemas asociados a los datos almacenados en Azure Cosmos DB - SQL API.
author: StefArroyo
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 06/12/2021
ms.author: esarroyo
ms.openlocfilehash: 16c4fe809fc411a9d6eec89ed7ceeb04dce317d2
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097779"
---
# <a name="troubleshoot-issues-with-advanced-diagnostics-queries-for-the-sql-core-api"></a>Solución de problemas mediante consultas avanzadas de diagnóstico para SQL (Core) API

[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [SQL API (Core)](cosmos-db-advanced-queries.md)
> * [MongoDB API](mongodb/diagnostic-queries-mongodb.md)
> * [Cassandra API](cassandra/diagnostic-queries-cassandra.md)
> * [Gremlin API](graph/diagnostic-queries-gremlin.md)
>

En este artículo, se explicará cómo escribir consultas más avanzadas para ayudar a solucionar problemas relacionados con la cuenta de Azure Cosmos DB mediante registros de diagnóstico enviados a las tablas de **Azure Diagnostics (heredado)** y **específicas del recurso (versión preliminar)** .

Para las tablas de Azure Diagnostics, todos los datos se escriben en una única tabla. Los usuarios especifican qué categoría quieren consultar. Si quiere ver la consulta de texto completo de la solicitud, vea [Supervisión de datos de Azure Cosmos DB mediante la configuración de diagnóstico en Azure](cosmosdb-monitor-resource-logs.md#full-text-query) para obtener información sobre cómo habilitar esta característica.

En el caso de las [tablas específicas del recurso](cosmosdb-monitor-resource-logs.md#create-setting-portal), los datos se escriben en tablas individuales para cada categoría del recurso. Se recomienda este modo porque:

- Facilita considerablemente el trabajo con los datos. 
- Proporciona una mejor detectabilidad de los esquemas.
- Mejora el rendimiento tanto de la latencia de ingesta como de los tiempos de consulta.

## <a name="common-queries"></a>Consultas comunes
Las consultas comunes se muestran en las tablas específicas del recurso y de Azure Diagnostics.

### <a name="top-n10-queries-ordered-by-request-unit-ru-consumption-in-a-specific-time-frame"></a>Principales consultas N(10) ordenadas por consumo de unidades de solicitud en un período de tiempo determinado

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

### <a name="requests-throttled-statuscode--429-in-a-specific-time-window"></a>Solicitudes limitadas (statusCode = 429) en un período de tiempo determinado 

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

### <a name="queries-with-the-largest-response-lengths-payload-size-of-the-server-response"></a>Consultas con las longitudes de respuesta más grandes (tamaño de carga de la respuesta del servidor)

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

### <a name="ru-consumption-by-physical-partition-across-all-replicas-in-the-replica-set"></a>Consumo de RU por partición física (en todas las réplicas del conjunto de réplicas)

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

### <a name="ru-consumption-by-logical-partition-across-all-replicas-in-the-replica-set"></a>Consumo de RU por partición lógica (en todas las réplicas del conjunto de réplicas)

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
* Para obtener más información sobre cómo crear una configuración de diagnóstico para Azure Cosmos DB, vea [Creación de una configuración de diagnóstico](cosmosdb-monitor-resource-logs.md).
* Para obtener más información sobre cómo crear una configuración de diagnóstico mediante Azure Portal, la CLI de Azure o PowerShell, vea [Creación de una configuración de diagnóstico para recopilar los registros y las métricas de la plataforma en Azure](../azure-monitor/essentials/diagnostic-settings.md).
