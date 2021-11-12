---
title: Creación de una conexión de datos de centro de eventos para el Explorador de datos de Azure Synapse mediante una plantilla de Azure Resource Manager (versión preliminar)
description: En este artículo, aprenderá a crear una conexión de datos de centro de eventos para el Explorador de datos de Azure Synapse mediante una plantilla de Azure Resource Manager.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 7b948a25a9a7b259abb0931927d9c2a821be427d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478265"
---
# <a name="create-an-event-hub-data-connection-for-azure-synapse-data-explorer-by-using-azure-resource-manager-template-preview"></a>Creación de una conexión de datos de centro de eventos para el Explorador de datos de Azure Synapse mediante una plantilla de Azure Resource Manager (versión preliminar)

> [!div class="op_single_selector"]
> * [Portal](data-explorer-ingest-event-hub-portal.md)
> * [Un solo clic](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Plantilla de Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)] 

En este artículo, creará una conexión de datos de centro de eventos para el Explorador de datos de Azure Synapse mediante una plantilla de Azure Resource Manager.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [Event Hub con datos para la ingesta](data-explorer-ingest-event-hub-portal.md#create-an-event-hub).

[!INCLUDE [data-explorer-ingest-event-hub-table-mapping](../includes/data-explorer-ingest-event-hub-table-mapping.md)]

## <a name="azure-resource-manager-template-for-adding-an-event-hub-data-connection"></a>Una plantilla de Azure Resource Manager para agregar una conexión de datos de Centro de eventos.

En el ejemplo siguiente se muestra una plantilla de Azure Resource Manager para agregar una conexión de datos de Centro de eventos.  Puede [editar e implementar la plantilla en Azure Portal](/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal#edit-and-deploy-the-template) mediante el formulario.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "namespaces_eventhubns_name": {
            "type": "string",
            "defaultValue": "eventhubns",
            "metadata": {
                "description": "Specifies the Event Hub Namespace name."
            }
        },
        "EventHubs_eventhubdemo_name": {
            "type": "string",
            "defaultValue": "eventhubdemo",
            "metadata": {
                "description": "Specifies the Event Hub name."
            }
        },
        "consumergroup_default_name": {
            "type": "string",
            "defaultValue": "$Default",
            "metadata": {
                "description": "Specifies the consumer group of the Event Hub."
            }
        },
        "Clusters_kustocluster_name": {
            "type": "string",
            "defaultValue": "kustocluster",
            "metadata": {
                "description": "Specifies the name of the cluster"
            }
        },
        "databases_kustodb_name": {
            "type": "string",
            "defaultValue": "kustodb",
            "metadata": {
                "description": "Specifies the name of the database"
            }
        },
        "tables_kustotable_name": {
            "type": "string",
            "defaultValue": "kustotable",
            "metadata": {
                "description": "Specifies the name of the table"
            }
        },
        "mapping_kustomapping_name": {
            "type": "string",
            "defaultValue": "kustomapping",
            "metadata": {
                "description": "Specifies the name of the mapping rule"
            }
        },
        "dataformat_type": {
            "type": "string",
            "defaultValue": "csv",
            "metadata": {
                "description": "Specifies the data format"
            }
        },
        "dataconnections_kustodc_name": {
            "type": "string",
            "defaultValue": "kustodc",
            "metadata": {
                "description": "Name of the data connection to create"
            }
        },
        "subscriptionId": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "Specifies the subscriptionId of the Event Hub"
            }
        },
        "resourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "metadata": {
                "description": "Specifies the resourceGroup of the Event Hub"
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "variables": {
    },
    "resources": [{
            "type": "Microsoft.Kusto/Clusters/Databases/DataConnections",
            "apiVersion": "2019-09-07",
            "name": "[concat(parameters('Clusters_kustocluster_name'), '/', parameters('databases_kustodb_name'), '/', parameters('dataconnections_kustodc_name'))]",
            "location": "[parameters('location')]",
            "kind": "EventHub",
            "properties": {
                "eventHubResourceId": "[resourceId(parameters('subscriptionId'), parameters('resourceGroup'), 'Microsoft.EventHub/namespaces/eventhubs', parameters('namespaces_eventhubns_name'), parameters('EventHubs_eventhubdemo_name'))]",
                "consumerGroup": "[parameters('consumergroup_default_name')]",
                "tableName": "[parameters('tables_kustotable_name')]",
                "mappingRuleName": "[parameters('mapping_kustomapping_name')]",
                "dataFormat": "[parameters('dataformat_type')]"
            }
        }
    ]
}
```

[!INCLUDE [data-explorer-clean-resources](../includes/data-explorer-clean-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Análisis con el Explorador de datos](../../get-started-analyze-data-explorer.md)
- [Supervisión de grupos del Explorador de datos](../data-explorer-monitor-pools.md)
