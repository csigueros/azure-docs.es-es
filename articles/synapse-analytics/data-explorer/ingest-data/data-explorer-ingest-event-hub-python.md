---
title: Uso de la ingesta de Python para ingerir datos del centro de eventos en Azure Synapse Data Explorer (versión preliminar)
description: Aprenda a usar Python para ingerir (cargar) datos en Azure Synapse Data Explorer desde el centro de eventos.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 64327836a5b2b9559699c4516cfaf4710b2754bb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478222"
---
# <a name="create-an-event-hub-data-connection-for-azure-synapse-data-explorer-by-using-python-preview"></a>Creación de una conexión de datos del centro de eventos en Azure Synapse Data Explorer con Python (versión preliminar)

> [!div class="op_single_selector"]
> * [Portal](data-explorer-ingest-event-hub-portal.md)
> * [Un solo clic](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Plantilla de Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

En este artículo va a crear una conexión de datos del centro de eventos en Azure Synapse Data Explorer con Python. 

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [Event Hub con datos para la ingesta](data-explorer-ingest-event-hub-portal.md#create-an-event-hub).
- [Python 3.4+](https://www.python.org/downloads/).
<!-- - [Database and table policies](database-table-policies-python.md) (optional). -->

[!INCLUDE [data-explorer-ingest-event-hub-table-mapping](../includes/data-explorer-ingest-event-hub-table-mapping.md)]

[!INCLUDE [data-explorer-data-connection-install-package-python](../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Incorporación de una conexión de datos de Event Hubs

En el ejemplo siguiente, se explica cómo se agrega una conexión de datos de Event Hubs mediante programación. Consulte [Conexión a Event Hubs](data-explorer-ingest-event-hub-portal.md#connect-to-the-event-hub) para agregar una conexión de datos de Event Hubs en Azure Portal.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Configuración** | **Valor sugerido** | **Descripción del campo**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | El identificador de inquilino. También conocido como identificador de directorio.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identificador de suscripción que se usa para la creación de recursos.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identificador del cliente de la aplicación que puede acceder a los recursos del inquilino.|
| client_secret | *xxxxxxxxxxxxxx* | Secreto del cliente de la aplicación que puede acceder a los recursos del inquilino. |
| resource_group_name | *testrg* | Nombre del grupo de recursos que contiene el clúster.|
| cluster_name | *mykustocluster* | Nombre del clúster.|
| database_name | *mykustodatabase* | Nombre de la base de datos de destino del clúster.|
| data_connection_name | *myeventhubconnect* | Nombre que desea asignar a la conexión de datos.|
| table_name | *StormEvents* | Nombre de la tabla de destino de la base de datos de destino.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | Nombre de la asignación de columnas asociada a la tabla de destino.|
| data_format | *csv* | Formato de datos del mensaje.|
| event_hub_resource_id | *Identificador del recurso* | Identificador del recurso del centro de eventos que contiene los datos que se van a ingerir. |
| consumer_group | *$Default* | Grupo de consumidores del centro de eventos.|
| ubicación | *Centro de EE. UU.* | Ubicación del recurso de conexión de datos.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../includes/data-explorer-data-connection-clean-resources-python.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Análisis con Data Explorer](../../get-started-analyze-data-explorer.md)
- [Supervisión de grupos de Data Explorer](../data-explorer-monitor-pools.md)
