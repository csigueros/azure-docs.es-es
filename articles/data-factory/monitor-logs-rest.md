---
title: Configuración de registros de diagnóstico mediante la API REST de Azure Monitor
description: Aprenda a configurar registros de diagnóstico para Azure Data Factory mediante la API REST de Azure Monitor.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 309b900f6c5f2ffe8cc0fd9101e7aa0408cb2dd2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837979"
---
# <a name="setup-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Configuración de registros de diagnóstico mediante la API REST de Azure Monitor

En este artículo se describe cómo configurar registros de diagnóstico para Azure Data Factory mediante la API REST de Azure Monitor.

## <a name="diagnostic-settings"></a>Configuración de diagnóstico

Use la configuración de diagnóstico para configurar los registros de diagnóstico de los recursos que no son de proceso. La configuración de un control de recursos tiene las siguientes características:

* Especifican dónde se envían los registros de diagnóstico. Entre los ejemplos se incluyen una cuenta de almacenamiento de Azure, un centro de eventos de Azure o registros de Monitor.
* Especifican las categorías de registro que se envían.
* Especifican cuánto tiempo se debe conservar cada categoría de registro en una cuenta de almacenamiento.
* Una retención de cero días significa que los registros se conservan de forma indefinida. De lo contrario, el valor puede ser cualquier número de días comprendido entre 1 y 2 147 483 647.
* Si se establecen directivas de retención, pero el almacenamiento de registros en una cuenta de almacenamiento está deshabilitado, las directivas de retención no surten ningún efecto. Por ejemplo, esta condición puede ocurrir cuando solo se seleccionan las opciones de registros de Event Hubs o de Monitor.
* Las directivas de retención se aplican por día. El límite entre los días se produce a la medianoche de la hora universal coordinada (UTC). Al final del día, se eliminan los registros de los días que superan la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy.

## <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Habilitación de registros de diagnóstico mediante la API REST de Azure Monitor

### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Creación o actualización de una configuración de diagnóstico en la API REST de Monitor

#### <a name="request"></a>Solicitud

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>encabezados

* Reemplace `{api-version}` por `2016-09-01`.
* Reemplace `{resource-id}` por el identificador del recurso para el que quiere editar la configuración de diagnóstico. Para más información, consulte el artículo sobre el [uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Establezca el encabezado `Content-Type` en `application/json`.
* Establezca el encabezado de autorización en un token web de JSON que se obtiene de Azure Active Directory (AAD). Par más información, consulte [Solicitudes de autenticación](../active-directory/develop/authentication-vs-authorization.md).

#### <a name="body"></a>Body

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Propiedad | Tipo | Descripción |
| --- | --- | --- |
| **storageAccountId** |String | El identificador de recurso de la cuenta de almacenamiento en la que quiere enviar los registros de diagnóstico. |
| **serviceBusRuleId** |String | El identificador de regla de Service Bus para el espacio de nombres de Service Bus donde quiere tener los centros de eventos creados para la transmisión de los registros de diagnóstico. El identificador de la regla tiene el formato `{service bus resource ID}/authorizationrules/{key name}`.|
| **workspaceId** | String | Identificador del área de trabajo donde se van a guardar los registros. |
|**métricas**| Valores de parámetros de la ejecución de canalización que se pasan a la canalización invocada| Objeto JSON que asigna nombres de parámetro a los valores de argumento. |
| **logs**| Tipo complejo| El nombre de una categoría de registro de diagnóstico para un tipo de recurso. Para obtener la lista de categorías de registro de diagnóstico para un recurso, realice una operación de configuración de diagnóstico GET. |
| **category**| String| Una matriz de las categorías de registro y sus directivas de retención. |
| **timeGrain** | String | La granularidad de las métricas que se capturan en formato de duración ISO 8601. El valor de propiedad debe ser `PT1M`, que especifica un minuto. |
| **enabled**| Boolean | Especifica si la colección de la categoría de métrica o registro está habilitada para este recurso. |
| **retentionPolicy**| Tipo complejo| Describe la directiva de retención para una categoría de métrica o registro. Esta propiedad se usa solo para las cuentas de almacenamiento. |
|**days**| Int| El número de días para retener las métricas o registros. Si el valor de propiedad es 0, los registros se mantienen indefinidamente. Esta propiedad se usa solo para las cuentas de almacenamiento. |

#### <a name="response"></a>Response

200 CORRECTO.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Permite obtener información sobre la configuración de diagnóstico en la API REST de Monitor.

#### <a name="request"></a>Solicitud

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

#### <a name="headers"></a>encabezados

* Reemplace `{api-version}` por `2016-09-01`.
* Reemplace `{resource-id}` por el identificador del recurso para el que quiere editar la configuración de diagnóstico. Para más información, consulte el artículo sobre el [uso de grupos de recursos para administrar los recursos de Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Establezca el encabezado `Content-Type` en `application/json`.
* Establezca el encabezado de autorización en un token web de JSON que obtuvo de Azure AD. Par más información, consulte [Solicitudes de autenticación](../active-directory/develop/authentication-vs-authorization.md).

#### <a name="response"></a>Response

200 CORRECTO.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Para más información, consulte [Configuración de diagnóstico](/rest/api/monitor/diagnosticsettings).

## <a name="next-steps"></a>Pasos siguientes

[Supervisión de operaciones de SSIS con Azure Monitor](monitor-ssis.md)
