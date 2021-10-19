---
title: Supervisión de datos de Azure Cache for Redis mediante la configuración de diagnóstico
titleSuffix: Azure Cache for Redis
description: Aprenda a usar la configuración de diagnóstico para supervisar las direcciones IP conectadas a su instancia de Azure Cache for Redis.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: e62215649a79f16fcb9cbfc20cfe16bc98f2251f
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812398"
---
# <a name="monitor-azure-cache-for-redis-data-using-diagnostic-settings"></a>Supervisión de datos de Azure Cache for Redis mediante la configuración de diagnóstico

La configuración de diagnóstico de Azure se usa para recopilar los registros de los recursos. Dichos registros de recursos de Azure los emite un recurso y proporcionan información detallada y frecuente sobre la operación de dicho recurso. Estos registros se capturan por solicitud y también se denominan "registros de plano de datos". El contenido de estos registros varía según el tipo de recurso.

Azure Cache for Redis usa la configuración de diagnóstico de Azure para registrar información sobre todas las conexiones de cliente a la caché. El registro y el análisis de esta configuración de diagnóstico le ayudarán a comprender quién se conecta a las cachés y la marca de tiempo de esas conexiones. Estos datos se pueden usar para identificar el ámbito de una vulneración de seguridad y con fines de auditoría de seguridad.

Una vez configurada, la caché comienza a registrar las conexiones de cliente entrantes por dirección IP. También se registra el número de conexiones que se originan en cada dirección IP única. Los registros no son acumulativos. Representan instantáneas a un momento dado tomadas a intervalos de 10 segundos.

Puede activar la configuración de diagnóstico para instancias de Azure Cache for Redis y enviar registros de recursos a los siguientes destinos:

- **Centro de eventos**: la configuración de diagnósticos no puede tener acceso a los recursos del centro de eventos cuando están habilitadas las redes virtuales. Habilite la opción **¿Quiere permitir que los servicios de confianza de Microsoft puedan omitir este firewall?** en los centros de eventos para conceder acceso a los recursos del centro de eventos. El centro de eventos debe estar en la misma región que el almacén.
- **Cuenta de almacenamiento**: debe estar en la misma región que la caché.

Para más información sobre los requisitos de diagnóstico, consulte [Configuración de diagnóstico](/azure/azure-monitor/essentials/diagnostic-settings?tabs=CMD).

Se le aplicarán las tarifas de datos normales por el uso de la cuenta de almacenamiento y del centro de eventos al enviar registros de diagnóstico a cualquier destino. La facturación se realiza en Azure Monitor, no en Azure Cache for Redis.
Para más información sobre los precios, consulte [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="create-diagnostics-settings-via-the-azure-portal"></a> Creación de la configuración de diagnóstico a través de Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. Vaya a la cuenta de Azure Cache for Redis. Abra el panel **Configuración de diagnóstico** en la sección **Supervisión** a la izquierda. Después, seleccione **Agregar configuración de diagnóstico**.

   :::image type="content" source="media/cache-monitor-diagnostic-settings/cache-monitor-diagnostic-setting.png" alt-text="Selección de diagnósticos":::

1. En el panel **Configuración de diagnóstico**, seleccione **ConnectedClientList** en **Detalles de la categoría**.

   |Category  | Definición  | Propiedades clave   |
   |---------|---------|---------|
   |ConnectedClientList |  Direcciones IP y recuentos de clientes conectados a la caché, registrados a intervalos regulares. | `connectedClients` y anidados en `ip`, `count`, `privateLinkIpv6` |
  
1. Una vez que seleccione los **detalles de la categoría**, envíe los registros al destino que prefiera. Seleccione la información de la derecha.

    :::image type="content" source="media/cache-monitor-diagnostic-settings/diagnostics-resource-specific.png" alt-text="Selección para habilitar la opción de específico del recurso":::

## <a name="create-diagnostic-setting-via-rest-api"></a> Creación de una configuración de diagnóstico a través de la API REST

Use la API REST de Azure Monitor para crear una configuración de diagnóstico a través de la consola interactiva. Para más información, vea el tema sobre [creación o actualización](/rest/api/monitor/diagnostic-settings/create-or-update.md).

### <a name="request"></a>Solicitud

```http
PUT https://management.azure.com/{resourceUri}/providers/Microsoft.Insights/diagnosticSettings/{name}?api-version=2017-05-01-preview
```

### <a name="headers"></a>encabezados

   | Parámetros y encabezados | Valor y descripción |
   |---------|---------|
   | name | El nombre de la configuración de diagnóstico. |
   | resourceUri | subscriptions/{SUBSCRIPTION_ID}/resourceGroups/{RESOURCE_GROUP}/providers/Microsoft.Cache/Redis/{CACHE_NAME} |
   | api-version | 2017-05-01-versión preliminar |
   | Content-Type | application/json |

### <a name="body"></a>Cuerpo

```json
{
    "properties": {
      "storageAccountId": "/subscriptions/df602c9c-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/apptest/providers/Microsoft.Storage/storageAccounts/appteststorage1",
      "eventHubAuthorizationRuleId": "/subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/eventhubs/myeventhub/authorizationrules/myrule",
      "eventHubName": "myeventhub",
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

Use el comando `az monitor diagnostic-settings create` para crear una configuración de diagnóstico con la CLI de Azure. Para más información sobre este comando y las descripciones de parámetros, consulte [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](../azure-monitor/essentials/diagnostic-settings.md).

```azurecli

az monitor diagnostic-settings create 
    --resource /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Cache/Redis/myname
    --name constoso-setting
    --logs '[{"category": "ConnectedClientList","enabled": true,"retentionPolicy": {"enabled": false,"days": 0}}]'    
    --event-hub MyEventHubName 
    --event-hub-rule /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/microsoft.eventhub/namespaces/mynamespace/authorizationrules/RootManageSharedAccessKey 
    --storage-account /subscriptions/1a66ce04-b633-4a0b-b2bc-a912ec8986a6/resourceGroups/montest/providers/Microsoft.Storage/storageAccounts/myuserspace


```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo crear una configuración de diagnóstico mediante Azure Portal, la CLI o PowerShell, consulte el artículo [Creación de una configuración de diagnóstico para enviar los registros y las métricas de la plataforma a diferentes destinos](../azure-monitor/essentials/diagnostic-settings.md).
