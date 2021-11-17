---
title: Azure Monitor
description: Azure Monitor
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: dcb4dcd6037f8efcbac33e970566babf10c7d262
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858484"
---
# <a name="monitor-azure-managed-instance-for-apache-cassandra-using-azure-monitor"></a>Supervisión de Azure Managed Instance for Apache Cassandra con Azure Monitor

Azure Managed Instance for Apache Cassandra proporciona métricas y registro de diagnóstico mediante [Azure Monitor](../azure-monitor/overview.md). 

## <a name="azure-metrics"></a>Métricas de Azure

Puede visualizar las métricas de Azure Managed Instance for Apache Cassandra; para ello, vaya al recurso del clúster y seleccione la pestaña Métricas. A continuación, puede elegir entre las métricas y agregaciones disponibles.

:::image type="content" source="./media/azure-monitor/metrics.png" alt-text="Visualización de métricas":::

## <a name="diagnostic-settings-in-azure"></a>Configuración de diagnóstico en Azure

La configuración de diagnóstico de Azure se usa para recopilar los registros de los recursos. Dichos registros de recursos de Azure los emite un recurso y proporcionan información detallada y frecuente sobre la operación de dicho recurso. Los registros se capturan por solicitud y también se denominan "registros de plano de datos". Algunos ejemplos de las operaciones del plano de datos son Delete, Insert y ReadFeed. El contenido de estos registros varía según el tipo de recurso.

Las métricas de la plataforma y el registro de actividad se recopilan automáticamente, mientras que debe crear una configuración de diagnóstico para recopilar registros de recursos o reenviarlos fuera de Azure Monitor. Puede activar la configuración de diagnóstico para los recursos de clúster de Azure Managed Instance for Apache Cassandra y enviar registros de recursos a los siguientes orígenes:
- Áreas de trabajo de Log Analytics
  - Los datos enviados a Log Analytics se pueden escribir en tablas de **Azure Diagnostics** (heredadas) o **específicas del recurso (versión preliminar)** . 
- Centro de eventos
- Cuenta de almacenamiento
  
> [!NOTE]
> Se recomienda crear la configuración de diagnóstico de forma específica para el recurso.

## <a name="create-diagnostic-settings-via-the-azure-portal"></a><a id="create-setting-portal"></a> Creación de la configuración de diagnóstico en Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Vaya al recurso de clúster de Azure Managed Instance for Apache Cassandra. 

    :::image type="content" source="./media/azure-monitor/cluster.png" alt-text="Seleccionar clúster":::
 
1. Abra el panel **Configuración de diagnóstico** en la **sección de supervisión** y, después, seleccione la opción **Agregar configuración de diagnóstico**.

    :::image type="content" source="./media/azure-monitor/settings.png" alt-text="Agregar configuración de diagnóstico":::


1. En el panel **Configuración de diagnóstico**, elija un nombre para la configuración y seleccione los **detalles de la categoría**. La categoría **CassandraAudit** registra las operaciones de auditoría y CQL. La categoría **CassandraLogs** registra las operaciones del servidor de Cassandra. A continuación, envíe los registros a su destino preferido. Si va a enviar los registros a un **Área de trabajo de Log Analytics**, asegúrese de seleccionar **Específico del recurso** como tabla de destino. 

    :::image type="content" source="./media/azure-monitor/preferred-categories.png" alt-text="Seleccionar categoría":::

   > [!WARNING]
   > Si va a enviar registros a un área de trabajo de Log Analytics, los registros pueden tardar hasta **20 minutos** en aparecer por primera vez. Hasta entonces, las tablas específicas del recurso (que se muestran a continuación debajo de Azure Managed Instance for Apache Cassandra) no estarán visibles.  


1. Una vez configurado el registro de diagnóstico y después de que los datos fluyan, puede ir a la pestaña **Registros** y consultar los registros de diagnóstico disponibles mediante Azure Data Explorer. Consulte [este artículo](../azure-monitor/logs/log-query-overview.md) para obtener más información sobre Azure Monitor y el lenguaje de consulta Kusto. 

    :::image type="content" source="./media/azure-monitor/query.png" alt-text="Registros de consultas":::

## <a name="create-diagnostic-setting-via-azure-cli"></a><a id="create-setting-cli"></a> Creación de una configuración de diagnóstico mediante la CLI de Azure
Use el comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) para crear una configuración de diagnóstico con la CLI de Azure. Consulte la documentación de este comando para las descripciones de sus parámetros.

```azurecli-interactive
    logs='[{"category":"CassandraAudit","enabled":true,"retentionPolicy":{"enabled":true,"days":3}},{"category":"CassandraLogs","enabled":true,"retentionPolicy":{"enabled":true,"days":3}}]'
    resourceId='/subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.DocumentDB/cassandraClusters/{CLUSTER_NAME}'
    workspace='/subscriptions/{SUBSCRIPTION_ID}/resourcegroups/{RESOURCE_GROUP}/providers/microsoft.operationalinsights/workspaces/{WORKSPACE_NAME}'

    az monitor diagnostic-settings create  --name tvk-doagnostic-logs-cassandra --resource $resourceId --logs  $logs --workspace $workspace --export-to-resource-specific true
```

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
                "category": "CassandraAudit",
                "categoryGroup": null,
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "CassandraLogs",
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


## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre cómo crear una configuración de diagnóstico mediante Azure Portal, la CLI o PowerShell, consulte el artículo [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](../azure-monitor/essentials/diagnostic-settings.md).