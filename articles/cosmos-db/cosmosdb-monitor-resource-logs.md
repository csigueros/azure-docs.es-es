---
title: Supervisión de datos de Azure Cosmos DB mediante la configuración de diagnóstico de Azure
description: Aprenda a usar la configuración de diagnóstico de Azure para supervisar el rendimiento y la disponibilidad de los datos almacenados en Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/20/2021
ms.author: sngun
ms.openlocfilehash: 55e84478d8744aae05f8f3a0df89aac605d6de12
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779927"
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
> Se recomienda crear la configuración de diagnóstico en modo específico del recurso (para todas las API excepto Table API) [de acuerdo con nuestras instrucciones para crear la configuración de diagnóstico a través de la API REST](cosmosdb-monitor-resource-logs.md#create-diagnostic-setting). Esta opción proporciona optimizaciones de costos adicionales con una vista mejorada para controlar los datos.

## <a name="create-diagnostics-settings-via-the-azure-portal"></a><a id="create-setting-portal"></a> Creación de la configuración de diagnóstico a través de Azure Portal

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

2. Vaya a la cuenta de Azure Cosmos. Abra el panel **Configuración de diagnóstico** en la **sección de supervisión** y, después, seleccione la opción **Agregar configuración de diagnóstico**.

   :::image type="content" source="./media/monitor-cosmos-db/diagnostics-settings-selection.png" alt-text="Selección de diagnósticos":::


3. En el panel **Configuración de diagnóstico**, rellene el formulario con las categorías que prefiera.

### <a name="choose-log-categories"></a>Elección de categorías del registro

   |Categoría  |API   | Definición  | Propiedades clave   |
   |---------|---------|---------|---------|
   |DataPlaneRequests     |  Todas las API        |     Registra las solicitudes de back-end como operaciones del plano de datos, que son solicitudes ejecutadas para crear, actualizar, eliminar o recuperar datos dentro de la cuenta.   |   `Requestcharge`, `statusCode`, `clientIPaddress`, `partitionID`, `resourceTokenPermissionId` `resourceTokenPermissionMode`      |
   |MongoRequests     |    Mongo    |   Registra las solicitudes iniciadas por el usuario desde el servidor front-end con el fin de atender solicitudes de la API de Azure Cosmos DB para MongoDB. Al habilitar esta categoría, asegúrese de deshabilitar DataPlaneRequests.      |  `Requestcharge`, `opCode`, `retryCount`, `piiCommandText`      |
   |CassandraRequests     |   Cassandra      |    Registra las solicitudes iniciadas por el usuario desde el servidor front-end con el fin de atender solicitudes de la API de Azure Cosmos DB para Cassandra. Al habilitar esta categoría, asegúrese de deshabilitar DataPlaneRequests.     |     `operationName`, `requestCharge`, `piiCommandText`    |
   |GremlinRequests     |    Gremlin    |     Registra las solicitudes iniciadas por el usuario desde el servidor front-end con el fin de atender solicitudes de la API de Azure Cosmos DB para Gremlin. Al habilitar esta categoría, asegúrese de deshabilitar DataPlaneRequests.    |   `operationName`, `requestCharge`, `piiCommandText`, `retriedDueToRateLimiting`       |
   |QueryRuntimeStatistics     |   SQL      |     En esta tabla se detallan las operaciones de consulta ejecutadas en una cuenta de API de SQL. De forma predeterminada, el texto de la consulta y sus parámetros se ofuscan para evitar el registro de datos personales con el registro de consultas de texto completo disponible por solicitud.    |    `databasename`, `partitionkeyrangeid`, `querytext`    |
   |PartitionKeyStatistics     |    Todas las API     |   Registra las estadísticas de las claves de partición lógicas mediante la representación del tamaño de almacenamiento estimado (KB) de las claves de partición. Esta tabla es útil al solucionar problemas de sesgos de almacenamiento. El registro PartitionKeyStatistics solo se emite si se cumplen las condiciones siguientes: <br/><ul><li> Al menos el 1 % de los documentos de la partición física tienen la misma clave de partición lógica. </li><li> De todas las claves de la partición física, el registro PartitionKeyStatistics captura las tres primeras con el mayor tamaño de almacenamiento. </li></ul> Si no se cumplen las condiciones anteriores, los datos estadísticos de las claves de partición no están disponibles. No importa si su cuenta no cumple las condiciones anteriores; esto suele indicar que no tiene asimetría de almacenamiento de las particiones lógicas. <br/><br/>Nota: El tamaño estimado de las claves de partición se calcula mediante un enfoque de muestreo que da por hecho que los documentos de la partición física tienen aproximadamente el mismo tamaño. Si los tamaños de los documentos no son uniformes en la partición física, es posible que el tamaño estimado de las claves de partición no sea preciso.  |   `subscriptionId`, `regionName`, `partitionKey`, `sizeKB`      |
   |PartitionKeyRUConsumption     |   API DE SQL    |     Registra el consumo agregado de RU por segundo de las claves de partición. Esta tabla es útil para solucionar problemas de particiones activas. Actualmente, Azure Cosmos DB solo informa de las claves de partición para las cuentas de la API de SQL y para las operaciones de lectura/escritura y de procedimientos almacenados.   |     `subscriptionId`, `regionName`, `partitionKey`, `requestCharge`, `partitionKeyRangeId`   |
   |ControlPlaneRequests     |   Todas las API       |    Registra detalles sobre las operaciones del panel de control, tales como la creación de una cuenta, la adición o eliminación de una región, la actualización de la configuración de replicación de la cuenta, etc.     |    `operationName`, `httpstatusCode`, `httpMethod`, `region`       |
   |TableApiRequests     |   Table API    |     Registra las solicitudes iniciadas por el usuario desde el servidor front-end con el fin de atender solicitudes de la API de Azure Cosmos DB para Table. Al habilitar esta categoría, asegúrese de deshabilitar DataPlaneRequests.       |    `operationName`, `requestCharge`, `piiCommandText`     |

4. Una vez que seleccione los **detalles de las categorías**, envíe los registros al destino que prefiera. Si va a enviar los registros a un **Área de trabajo de Log Analytics**, asegúrese de seleccionar **Específico del recurso** como tabla de destino.

    :::image type="content" source="./media/monitor-cosmos-db/diagnostics-resource-specific.png" alt-text="Selección para habilitar la opción de específico del recurso":::

## <a name="create-diagnostic-setting-via-rest-api"></a><a id="create-diagnostic-setting"></a> Creación de una configuración de diagnóstico a través de la API REST
Use la [API REST de Azure Monitor](/rest/api/monitor/diagnosticsettings/createorupdate) para crear una configuración de diagnóstico a través de la consola interactiva.
> [!Note]
> Se recomienda establecer la propiedad **logAnalyticsDestinationType** en **Dedicated** para habilitar tablas específicas de recursos.

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
## <a name="enable-full-text-query-for-logging-query-text"></a><a id="full-text-query"></a> Habilitación de la consulta de texto completo para registrar texto de consulta

> [!Note]
> La habilitación de esta característica puede dar lugar a costos de registro adicionales; para obtener más información sobre los precios, visite [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Se recomienda deshabilitar esta característica después de solucionar los problemas.

Azure Cosmos DB proporciona un registro avanzado para una solución de problemas detallada. Al habilitar la consulta de texto, podrá ver la consulta desofuscada para todas las solicitudes en la cuenta de Azure Cosmos DB.  También concederá permiso para que Azure Cosmos DB acceda a estos datos y los presente en los registros. 

1. Para habilitar esta característica, vaya a la hoja `Features` de la cuenta de Cosmos DB.
   
   :::image type="content" source="./media/monitor-cosmos-db/full-text-query-features.png" alt-text="Navegar a la hoja Features":::

2. Seleccione `Enable`; esta configuración se aplicará en los próximos minutos. Todos los registros recién ingeridos tendrán el texto completo o PIICommand para cada solicitud.
   
    :::image type="content" source="./media/monitor-cosmos-db/select-enable-full-text.png" alt-text="Seleccione habilitar el texto completo":::

Para obtener información sobre cómo realizar consultas con esta característica recién habilitada, visite las [consultas avanzadas](cosmos-db-advanced-queries.md).

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información sobre cómo consultar tablas específicas de recursos, vea la [solución de problemas mediante tablas específicas de recursos](cosmosdb-monitor-logs-basic-queries.md#resource-specific-queries).

* Para más información sobre cómo consultar tablas de AzureDiagnostics, vea [Solución de problemas con tablas de AzureDiagnostics](cosmosdb-monitor-logs-basic-queries.md#azure-diagnostics-queries).

* Para más información sobre cómo crear una configuración de diagnóstico mediante Azure Portal, la CLI o PowerShell, consulte el artículo [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](../azure-monitor/essentials/diagnostic-settings.md).
