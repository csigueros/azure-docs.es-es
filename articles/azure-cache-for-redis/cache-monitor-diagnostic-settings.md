---
title: Supervisión de datos de Azure Cache for Redis mediante la configuración de diagnóstico
titleSuffix: Azure Cache for Redis
description: Aprenda a usar la configuración de diagnóstico para supervisar las direcciones IP conectadas a su instancia de Azure Cache for Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.date: 11/3/2021
ms.custom: template-how-to
ms.openlocfilehash: 8fd6ae4eb82c7b8cb1439f621f26eef7860bcc0d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340231"
---
# <a name="monitor-azure-cache-for-redis-data-using-diagnostic-settings"></a>Supervisión de datos de Azure Cache for Redis mediante la configuración de diagnóstico

La configuración de diagnóstico de Azure se usa para recopilar los registros de los recursos. Dichos registros de recursos de Azure los emite un recurso y proporcionan información detallada y frecuente sobre la operación de dicho recurso. Estos registros se capturan por solicitud y también se denominan "registros de plano de datos". El contenido de estos registros varía según el tipo de recurso.

Azure Cache for Redis usa la configuración de diagnóstico de Azure para registrar información sobre todas las conexiones de cliente a la caché. El registro y el análisis de esta configuración de diagnóstico le ayudarán a comprender quién se conecta a las cachés y la marca de tiempo de esas conexiones. Los datos del registro se pueden usar para identificar el ámbito de una vulneración de seguridad y con fines de auditoría de seguridad.

Una vez configurada, la caché comienza a registrar las conexiones de cliente entrantes por dirección IP. También se registra el número de conexiones que se originan en cada dirección IP única. Los registros no son acumulativos. Representan instantáneas a un momento dado tomadas a intervalos de 10 segundos.

Puede activar la configuración de diagnóstico para instancias de Azure Cache for Redis y enviar registros de recursos a los siguientes destinos:

- **Área de trabajo de Log Analytics**: no es necesario que esté en la misma región que el recurso que se esté supervisando.
- **Cuenta de almacenamiento**: debe estar en la misma región que la caché.
- **Centro de eventos**: la configuración de diagnósticos no puede tener acceso a los recursos del centro de eventos cuando están habilitadas las redes virtuales. Habilite la opción **¿Quiere permitir que los servicios de confianza de Microsoft puedan omitir este firewall?** en los centros de eventos para conceder acceso a los recursos del centro de eventos. El centro de eventos debe estar en la misma región que el almacén.

Para más información sobre los requisitos de diagnóstico, consulte [Configuración de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md?tabs=CMD).

Se le aplicarán las tarifas de datos normales por el uso de la cuenta de almacenamiento y del centro de eventos al enviar registros de diagnóstico a cualquier destino. La facturación se realiza en Azure Monitor, no en Azure Cache for Redis. Al enviar registros a **Log Analytics**, solo se le cobrará por la ingesta de datos de Log Analytics.

Para más información sobre los precios, consulte [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="create-diagnostics-settings-via-the-azure-portal"></a> Creación de la configuración de diagnóstico a través de Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Vaya a la cuenta de Azure Cache for Redis. Abra el panel **Configuración de diagnóstico** en la sección **Supervisión** a la izquierda. Después, seleccione **Agregar configuración de diagnóstico**.

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="Selección de diagnósticos":::

1. En el panel **Configuración de diagnóstico**, seleccione **ConnectedClientList** en **Detalles de la categoría**.

   |Category  | Definición  | Propiedades clave   |
   |---------|---------|---------|
   |ConnectedClientList |  Direcciones IP y recuentos de clientes conectados a la caché, registrados a intervalos regulares. | `connectedClients` y anidados en `ip`, `count`, `privateLinkIpv6` |

   Para más información sobre otros campos, consulte los siguientes [registros de recursos](#resource-logs).

1. Una vez que seleccione los **detalles de la categoría**, envíe los registros al destino que prefiera. Seleccione la información de la derecha.

    :::image type="content" source="media/cache-monitor-diagnostic-settings/diagnostics-resource-specific.png" alt-text="Selección para habilitar la opción de específico del recurso":::

## <a name="create-diagnostic-setting-via-rest-api"></a> Creación de una configuración de diagnóstico a través de la API REST

Use la API REST de Azure Monitor para crear una configuración de diagnóstico a través de la consola interactiva. Para más información, vea el tema sobre [creación o actualización](/rest/api/monitor/diagnostic-settings/create-or-update).

### <a name="request"></a>Solicitud

```http
PUT https://management.azure.com/{resourceUri}/providers/Microsoft.Insights/diagnosticSettings/{name}?api-version=2017-05-01-preview
```

### <a name="headers"></a>encabezados

   | Parámetros y encabezados | Valor y descripción |
   |---------|---------|
   | `name` | El nombre de la configuración de diagnóstico. |
   | `resourceUri` | subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Cache/Redis/{CACHE_NAME} |
   | `api-version` | 2017-05-01-versión preliminar |
   | `Content-Type` | application/json |

### <a name="body"></a>Cuerpo

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
      "workspaceId": "/subscriptions/4b9e8510-67ab-4e9a-95a9-e2f1e570ea9c/resourceGroups/insights-integration/providers/Microsoft.OperationalInsights/workspaces/myworkspace",
      "logs": [
        {
          "category": "ConnectedClientList",
          "enabled": true,
          "retentionPolicy": {
            "enabled": false,
            "days": 0
          }
        }
      ]
    }
}
```

## <a name="create-diagnostic-setting-via-azure-cli"></a>Creación de una configuración de diagnóstico mediante la CLI de Azure

Use el comando `az monitor diagnostic-settings create` para crear una configuración de diagnóstico con la CLI de Azure. Para más información sobre el comando y las descripciones de parámetros, consulte [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest&branch=main&preserve-view=true#az_monitor_diagnostic_settings_create).

```azurecli
az monitor diagnostic-settings create 
    --resource /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Cache/Redis/myname
    --name constoso-setting
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub MyEventHubName 
    --event-hub-rule /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/authorizationrules/RootManageSharedAccessKey 
    --storage-account /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Storage/storageAccounts/myuserspace
    --workspace /subscriptions/4b9e8510-67ab-4e9a-95a9-e2f1e570ea9c/resourceGroups/insights-integration/providers/Microsoft.OperationalInsights/workspaces/myworkspace
```

## <a name="resource-logs"></a>Registros de recurso

Estos campos y propiedades aparecen en la categoría de registro `ConnectedClientList`. En **Azure Monitor**, los registros se recopilan en la tabla `ACRConnectedClientList` con el nombre `MICROSOFT.CACHE` para el proveedor de recursos.

| Propiedad o campo de Azure Storage | Propiedad de registros de Azure Monitor | Descripción |
| --- | --- | --- |
| `time` | `TimeGenerated` | Marca de tiempo de cuándo se generó el registro en UTC. |
| `location` | `Location` | Ubicación (región) en la que se ha accedido a la instancia de Azure Cache for Redis. |
| `category` | N/D | Categorías de registro disponibles: `ConnectedClientList`. |
| `resourceId` | `_ResourceId` | Recurso Azure Cache for Redis para el que están habilitados los registros.|
| `operationName` | `OperationName` | La operación de Redis asociada a la entrada de registro. |
| `properties` | N/D | El contenido de este campo se describe en las filas siguientes. |
| `tenant` | `CacheName` | Nombre de la instancia de Azure Cache for Redis. |
| `roleInstance` | `RoleInstance` | Instancia de rol que registró la lista de clientes. |
| `connectedClients.ip` | `ClientIp` | Dirección IP del cliente de Redis. |
| `connectedClients.privateLinkIpv6` | `PrivateLinkIpv6` | La dirección IPv6 del vínculo privado del cliente de Redis (si procede). |
| `connectedClients.count` | `ClientCount` | Número de conexiones de cliente de Redis desde la dirección IP asociada. |

### <a name="sample-storage-account-log"></a>Registro de la cuenta de almacenamiento de ejemplo

Si envía los registros a una cuenta de almacenamiento, el contenido de los registros tiene este aspecto.

```json
{
    "time": "2021-08-05T21:04:58.0466086Z",
    "location": "canadacentral",
    "category": "ConnectedClientList",
    "properties": {
        "tenant": "mycache", 
        "connectedClients": [
            {
                "ip": "192.123.43.36", 
                "count": 86
            },
            {
                "ip": "10.1.1.4",
                "privateLinkIpv6": "fd40:8913:31:6810:6c31:200:a01:104", 
                "count": 1
            }
        ],
        "roleInstance": "1"
    },
    "resourceId": "/SUBSCRIPTIONS/E6761CE7-A7BC-442E-BBAE-950A121933B5/RESOURCEGROUPS/AZURE-CACHE/PROVIDERS/MICROSOFT.CACHE/REDIS/MYCACHE", 
    "Level": 4,
    "operationName": "Microsoft.Cache/ClientList"
}
```

## <a name="log-analytics-queries"></a>Consultas de Log Analytics

Estas son algunas consultas básicas que se usarán como modelos.

- Conexiones cliente de Azure Cache for Redis por hora en el intervalo de direcciones IP especificado:

```kusto
let IpRange = "10.1.1.0/24";
ACRConnectedClientList
// For particular datetime filtering, add '| where TimeGenerated between (StartTime .. EndTime)'
| where ipv4_is_in_range(ClientIp, IpRange)
| summarize ConnectionCount = sum(ClientCount) by TimeRange = bin(TimeGenerated, 1h)
```

- Direcciones IP de cliente de Redis únicas que se han conectado a la memoria caché:

```kusto
ACRConnectedClientList
| summarize count() by ClientIp
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo crear una configuración de diagnóstico mediante Azure Portal, la CLI o PowerShell, consulte el artículo [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](../azure-monitor/essentials/diagnostic-settings.md).
