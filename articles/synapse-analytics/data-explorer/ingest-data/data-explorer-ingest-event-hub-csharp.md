---
title: Uso de la ingesta de C\# para ingerir datos del centro de eventos en el Explorador de datos de Azure Synapse (versión preliminar)
description: Aprenda a usar C\# para ingerir (cargar) datos en el Explorador de datos de Azure Synapse desde el centro de eventos.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 9e8cf21f2cb54246152015e42d6c0e5b2cabcad5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478238"
---
# <a name="create-an-event-hub-data-connection-for-azure-synapse-data-explorer-by-using-c-preview"></a>Creación de una conexión de datos del centro de eventos en el Explorador de datos de Azure Synapse con C\# (versión preliminar)

> [!div class="op_single_selector"]
> * [Portal](data-explorer-ingest-event-hub-portal.md)
> * [Un solo clic](data-explorer-ingest-event-hub-one-click.md)
> * [C\#](data-explorer-ingest-event-hub-csharp.md)
> * [Python](data-explorer-ingest-event-hub-python.md)
> * [Plantilla de Azure Resource Manager](data-explorer-ingest-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](../includes/data-explorer-ingest-data-intro.md)]

En este artículo va a crear una conexión de datos del centro de eventos en el Explorador de datos de Azure Synapse con C\#.

## <a name="prerequisites"></a>Prerrequisitos

[!INCLUDE [data-explorer-ingest-prerequisites](../includes/data-explorer-ingest-prerequisites.md)]

- [Event Hub con datos para la ingesta](data-explorer-ingest-event-hub-portal.md#create-an-event-hub).

> [!NOTE]
> La ingesta de datos de un centro de eventos en grupos del Explorador de datos no funcionará si el área de trabajo de Synapse usa una red virtual administrada con la protección de filtración de datos habilitada.

- Visual Studio 2019: descargue y use [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/), que es una edición **gratuita**. Habilite **Desarrollo de Azure** durante la instalación de Visual Studio.

<!-- * Set [database and table policies](database-table-policies-csharp.md) (optional). -->

[!INCLUDE [data-explorer-ingest-event-hub-table-mapping](../includes/data-explorer-ingest-event-hub-table-mapping.md)]

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Incorporación de una conexión de datos de Event Hubs

En el ejemplo siguiente, se explica cómo se agrega una conexión de datos de Event Hubs mediante programación. Consulte [Conexión al centro de eventos](data-explorer-ingest-event-hub-portal.md#connect-to-the-event-hub) para obtener información sobre cómo agregar una conexión de datos del centro de eventos mediante Azure Portal.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The Event Hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
var compression = "None";
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat, compression: compression));
```

|**Configuración** | **Valor sugerido** | **Descripción del campo**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | El identificador de inquilino. También conocido como identificador de directorio.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identificador de suscripción que se usa para la creación de recursos.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Identificador del cliente de la aplicación que puede acceder a los recursos del inquilino.|
| clientSecret | *xxxxxxxxxxxxxx* | Secreto del cliente de la aplicación que puede acceder a los recursos del inquilino.|
| resourceGroupName | *testrg* | Nombre del grupo de recursos que contiene el clúster.|
| clusterName | *mykustocluster* | Nombre del clúster.|
| databaseName | *mykustodatabase* | Nombre de la base de datos de destino del clúster.|
| dataConnectionName | *myeventhubconnect* | Nombre que desea asignar a la conexión de datos.|
| tableName | *StormEvents* | Nombre de la tabla de destino de la base de datos de destino.|
| mappingRuleName | *StormEvents_CSV_Mapping* | Nombre de la asignación de columnas asociada a la tabla de destino.|
| dataFormat | *csv* | Formato de datos del mensaje.|
| eventHubResourceId | *Identificador del recurso* | Identificador del recurso del centro de eventos que contiene los datos que se van a ingerir. |
| consumerGroup | *$Default* | Grupo de consumidores del centro de eventos.|
| ubicación | *Centro de EE. UU.* | Ubicación del recurso de conexión de datos.|
| compression | *Gzip* o *Ninguno* | El tipo de compresión de datos. |

## <a name="generate-data"></a>Generación de datos

Examine la [aplicación de ejemplo](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) que genera los datos y los envía a un centro de eventos.

Un evento puede contener uno o más registros, hasta su límite de tamaño. En el ejemplo siguiente se envían dos eventos, cada uno tiene cinco registros anexados:

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) };
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../includes/data-explorer-data-connection-clean-resources-csharp.md)]

## <a name="next-steps"></a>Pasos siguientes

- [Análisis con el Explorador de datos](../../get-started-analyze-data-explorer.md)
- [Supervisión de grupos del Explorador de datos](../data-explorer-monitor-pools.md)
