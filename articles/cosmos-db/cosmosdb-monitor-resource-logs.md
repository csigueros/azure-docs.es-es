---
title: Supervisión de datos de Azure Cosmos DB mediante la configuración de diagnóstico de Azure
description: Aprenda a usar la configuración de diagnóstico de Azure para supervisar el rendimiento y la disponibilidad de los datos almacenados en Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/20/2021
ms.author: sngun
ms.openlocfilehash: 8c9303097a7b3b545d8fa106dd23f8d5662fc69d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111964216"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Supervisión de datos de Azure Cosmos DB mediante la configuración de diagnóstico en Azure
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

La configuración de diagnóstico de Azure se usa para recopilar los registros de los recursos. Dichos registros de recursos de Azure los emite un recurso y proporcionan información detallada y frecuente sobre la operación de dicho recurso. Los registros se capturan por solicitud y también se denominan "registros de plano de datos". Algunos ejemplos de las operaciones del plano de datos son Delete, Insert y ReadFeed. El contenido de estos registros varía según el tipo de recurso.

Las métricas de la plataforma y el registro de actividad se recopilan automáticamente, mientras que debe crear una configuración de diagnóstico para recopilar registros de recursos o reenviarlos fuera de Azure Monitor. Puede activar la configuración de diagnóstico para las cuentas de Azure Cosmos DB y enviar registros de recursos a los siguientes orígenes:
- Áreas de trabajo de Log Analytics
  - Los datos enviados a Log Analytics se pueden escribir en tablas de **Azure Diagnostics** (heredadas) o **específicas del recurso (versión preliminar)** . 
- Centro de eventos
- Cuenta de almacenamiento
  
> [!NOTE]
> En el caso de las cuentas de API de SQL, se recomienda crear la configuración de diagnóstico en modo específico del recurso [siguiendo nuestras instrucciones para crear la configuración de diagnóstico a través de la API REST](cosmosdb-monitor-resource-logs.md#create-diagnostic-setting). Esta opción proporciona optimizaciones de costos adicionales con una vista mejorada para controlar los datos.

## <a name="create-using-the-azure-portal"></a>Creación mediante Azure Portal

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

1. Vaya a la cuenta de Azure Cosmos. Abra el panel **Configuración de diagnóstico** y, después, seleccione la opción **Agregar configuración de diagnóstico**.

2. En el panel **Configuración de diagnóstico**, rellene el formulario con las categorías que prefiera.

### <a name="choosing-log-categories"></a>Selección de las categorías de registro

|Categoría  |API   | Definición  | Propiedades clave   |
|---------|---------|---------|---------|
|DataPlaneRequests     |  Todas las API        |     Registra las solicitudes de back-end como operaciones del plano de datos, que son solicitudes ejecutadas para crear, actualizar, eliminar o recuperar datos dentro de la cuenta.   |   `Requestcharge`, `statusCode`, `clientIPaddress`, `partitionID`, `resourceTokenPermissionId` `resourceTokenPermissionMode`      |
|MongoRequests     |    Mongo    |   Registra las solicitudes iniciadas por el usuario desde el servidor front-end con el fin de atender solicitudes de la API de Azure Cosmos DB para MongoDB. Al habilitar esta categoría, asegúrese de deshabilitar DataPlaneRequests.      |  `Requestcharge`, `opCode`, `retryCount`, `piiCommandText`      |
|CassandraRequests     |   Cassandra      |    Registra las solicitudes iniciadas por el usuario desde el servidor front-end con el fin de atender solicitudes de la API de Azure Cosmos DB para Cassandra. Al habilitar esta categoría, asegúrese de deshabilitar DataPlaneRequests.     |     `operationName`, `requestCharge`, `piiCommandText`    |
|GremlinRequests     |    Gremlin    |     Registra las solicitudes iniciadas por el usuario desde el servidor front-end con el fin de atender solicitudes de la API de Azure Cosmos DB para Gremlin. Al habilitar esta categoría, asegúrese de deshabilitar DataPlaneRequests.    |   `operationName`, `requestCharge`, `piiCommandText`, `retriedDueToRateLimiting`       |
|QueryRuntimeStatistics     |   SQL      |     En esta tabla se detallan las operaciones de consulta ejecutadas en una cuenta de API de SQL. De forma predeterminada, el texto de la consulta y sus parámetros se ofuscan para evitar el registro de datos PII con el registro de consultas de texto completo disponible por solicitud.    |    `databasename`, `partitionkeyrangeid`, `querytext`    |
|PartitionKeyStatistics     |    Todas las API     |   Registra las estadísticas de las claves de partición lógicas representando el tamaño de almacenamiento (KB) de las claves de partición. Esta tabla es útil al solucionar problemas de sesgos de almacenamiento.      |   `subscriptionId`, `regionName`, `partitionKey`, `sizeKB`      |
|PartitionKeyRUConsumption     |   API DE SQL    |     Registra el consumo agregado de RU por segundo de las claves de partición. Esta tabla es útil para solucionar problemas de particiones activas. Actualmente, Azure Cosmos DB solo informa de las claves de partición para las cuentas de la API de SQL y para las operaciones de lectura/escritura y de procedimientos almacenados.   |     `subscriptionId`, `regionName`, `partitionKey`, `requestCharge`, `partitionKeyRangeId`   |
|ControlPlaneRequests     |   Todas las API       |    Registra detalles sobre las operaciones del panel de control, tales como la creación de una cuenta, la adición o eliminación de una región, la actualización de la configuración de replicación de la cuenta, etc.     |    `operationName`, `httpstatusCode`, `httpMethod`, `region`       |
|TableApiRequests     |   Table API    |     Registra las solicitudes iniciadas por el usuario desde el servidor front-end con el fin de atender solicitudes de la API de Azure Cosmos DB para Table. Al habilitar esta categoría, asegúrese de deshabilitar DataPlaneRequests.       |    `operationName`, `requestCharge`, `piiCommandText`     |


## <a name="create-diagnostic-setting-via-rest-api"></a><a id="create-diagnostic-setting"></a> Creación de una configuración de diagnóstico a través de la API REST
Use la [API REST de Azure Monitor](/rest/api/monitor/diagnosticsettings/createorupdate) para crear una configuración de diagnóstico a través de la consola interactiva.
> [!Note]
> Si usa la API de SQL, se recomienda establecer la propiedad **logAnalyticsDestinationType** en **Dedicated** para habilitar tablas específicas de recursos.

### <a name="request"></a>Solicitud

```HTTP
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

### <a name="headers"></a>encabezados

|Parámetros y encabezados  | Valor y descripción  |
|---------|---------|
|name     |  El nombre de la configuración de diagnóstico.      |
|resourceUri     |   subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}      |
|api-version     |    2017-05-01-versión preliminar     |
|Content-Type     |    application/json     |

### <a name="body"></a>Cuerpo

```json
{
    "id": "/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/{ACCOUNT_NAME}/providers/microsoft.insights/diagnosticSettings/{DIAGNOSTIC_SETTING_NAME}",
    "type": "Microsoft.Insights/diagnosticSettings",
    "name": "name",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": null,
        "serviceBusRuleId": null,
        "workspaceId": "/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "logs": [
            {
                "category": "DataPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "QueryRuntimeStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyStatistics",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "PartitionKeyRUConsumption",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ControlPlaneRequests",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ],
        "logAnalyticsDestinationType": "Dedicated"
    },
    "identity": null
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>Creación de una configuración de diagnóstico mediante la CLI de Azure
Use el comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) para crear una configuración de diagnóstico con la CLI de Azure. Consulte la documentación de este comando para las descripciones de sus parámetros.

> [!Note]
> Si usa la API de SQL, se recomienda establecer la propiedad **export-to-resource-specific** en **true**.

```azurecli-interactive
az monitor diagnostic-settings create --resource /subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDb/databaseAccounts/ --name {DIAGNOSTIC_SETTING_NAME} --export-to-resource-specific true --logs '[{"category": "QueryRuntimeStatistics","categoryGroup": null,"enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]' --workspace /subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}"
```

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre cómo consultar tablas específicas de recursos, vea [Solución de problemas mediante tablas específicas de recursos](cosmosdb-monitor-logs-basic-queries.md#resource-specific-queries).

* Para más información sobre cómo consultar tablas de AzureDiagnostics, vea [Solución de problemas con tablas de AzureDiagnostics](cosmosdb-monitor-logs-basic-queries.md#azure-diagnostics-queries).

* Para más información sobre cómo crear una configuración de diagnóstico mediante Azure Portal, la CLI o PowerShell, consulte el artículo [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](../azure-monitor/essentials/diagnostic-settings.md).