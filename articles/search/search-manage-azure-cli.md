---
title: Scripts de la CLI de Azure que utilizan el módulo az search
titleSuffix: Azure Cognitive Search
description: Cree y configure un servicio de Azure Cognitive Search con la CLI de Azure. Puede escalar o reducir un servicio verticalmente, gestionar la administración y las claves de API de consulta y consultar información del sistema.
author: DerekLegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 08/03/2021
ms.openlocfilehash: b5689b17bf2e4eace52e7c3cb28c40dc05e58ade
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179395"
---
# <a name="manage-your-azure-cognitive-search-service-with-the-azure-cli"></a>Administración del servicio de Azure Cognitive Search con la CLI de Azure
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [CLI de Azure](search-manage-azure-cli.md)
> * [REST API](/rest/api/searchmanagement/)
> * [SDK de .NET](/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)

Puede ejecutar scripts y comandos de la CLI de Azure en Windows, macOS, Linux o en [Azure Cloud Shell](../cloud-shell/overview.md) para crear y configurar Azure Cognitive Search. El módulo [**az search**](/cli/azure/search) amplía la [CLI de Azure](/cli/) con paridad completa con las [API REST de administración de búsquedas](/rest/api/searchmanagement) y permite desempeñar las tareas siguientes:

> [!div class="checklist"]
> * [Enumerar servicios de búsqueda en una suscripción](#list-search-services)
> * [Devolver información del servicio](#get-search-service-information)
> * [Crear o eliminar un servicio](#create-or-delete-a-service)
> * [Creación de un servicio con un punto de conexión privado](#create-a-service-with-a-private-endpoint)
> * [Regenerar claves de API de administración](#regenerate-admin-keys)
> * [Crear o eliminar claves de API de consulta](#create-or-delete-query-keys)
> * [Escalar o reducir verticalmente con réplicas y particiones](#scale-replicas-and-partitions)
> * [Creación de un recurso compartido de vínculo privado](#create-a-shared-private-link-resource)

En ocasiones, se formulan preguntas sobre las tareas que *no* están en la lista anterior. Actualmente no se puede usar el módulo **az search** ni la API REST de administración para cambiar el nombre, la región ni el nivel de un servidor. Cuando se crea un servicio, se asignan recursos dedicados. Por tanto, el cambio del hardware subyacente (tipo de nodo o ubicación), requiere un nuevo servicio. De igual manera, no hay herramientas ni API para transferir contenido, como un índice, de un servicio a otro.

Dentro de un servicio, la creación y administración de contenido se realizan a través de la [API REST del servicio de búsqueda](/rest/api/searchservice/) o el [SDK de .NET](/dotnet/api/overview/azure/search.documents-readme). Aunque no haya comandos de PowerShell dedicados para el contenido, puede escribir scripts que llamen a las API REST o .NET para crear y cargar índices.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

<a name="list-search-services"></a>

## <a name="list-services-in-a-subscription"></a>Enumeración de servicios en una suscripción

Los siguientes comandos son de [**az resource**](/cli/azure/resource) y devuelven información sobre los recursos existentes y servicios ya aprovisionados en su suscripción. Si no sabe cuántos servicios de búsqueda ya se han creado, estos comandos devuelven esa información, lo que le ahorra un viaje al portal.

El primer comando devuelve todos los servicios de búsqueda.

```azurecli-interactive
az resource list --resource-type Microsoft.Search/searchServices --output table
```

De la lista de servicios, devuelva información sobre un recurso específico.

```azurecli-interactive
az resource list --name <search-service-name>
```

## <a name="list-all-az-search-commands"></a>Lista de todos los comandos az search

Puede ver información sobre los subgrupos y los comandos disponibles en [**az search**](/cli/azure/search) desde la CLI. También puede revisar la [documentación](/cli/azure/search).

Para ver los subgrupos disponibles dentro de `az search`, ejecute el siguiente comando.

```azurecli-interactive
az search --help
```

La respuesta debe ser similar a la siguiente salida.

```
Group
    az search : Manage Azure Search services, admin keys and query keys.
        WARNING: This command group is in preview and under development. Reference and support
        levels: https://aka.ms/CLI_refstatus
Subgroups:
    admin-key                    : Manage Azure Search admin keys.
    private-endpoint-connection  : Manage Azure Search private endpoint connections.
    private-link-resource        : Manage Azure Search private link resources.
    query-key                    : Manage Azure Search query keys.
    service                      : Manage Azure Search services.
    shared-private-link-resource : Manage Azure Search shared private link resources.

For more specific examples, use: az find "az search"
```

Dentro de cada subgrupo hay disponibles varios comandos. Puede ejecutar la línea siguiente para ver los comandos disponibles para el subgrupo `service`.

```azurecli-interactive
az search service --help
```

También puede ver los argumentos disponibles para un comando determinado.

```azurecli-interactive
az search service create --help
```

## <a name="get-search-service-information"></a>Obtención de información del servicio de búsqueda

Si conoce el grupo de recursos que contiene su servicio de búsqueda, ejecute [**az search service show**](/cli/azure/search/service#az_search_service_show) para devolver la definición del servicio, incluido el nombre, la región, el nivel y los recuentos de réplicas y particiones. Para este comando, proporcione el grupo de recursos que contiene el servicio de búsqueda.

```azurecli-interactive
az search service show --name <service-name> --resource-group <search-service-resource-group-name>
```

## <a name="create-or-delete-a-service"></a>Creación o eliminación de un servicio

Para [crear un servicio de búsqueda](search-create-service-portal.md), use el comando [**az search service create**](/cli/azure/search/service#az_search_service_show).

```azurecli-interactive
az search service create \
    --name <service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1
``` 

Los resultados deben tener un aspecto similar a la siguiente salida:

```
{
  "hostingMode": "default",
  "id": "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/demo-westus/providers/Microsoft.Search/searchServices/my-demo-searchapp",
  "identity": null,
  "location": "West US",
  "name": "my-demo-searchapp",
  "networkRuleSet": {
    "bypass": "None",
    "ipRules": []
  },
  "partitionCount": 1,
  "privateEndpointConnections": [],
  "provisioningState": "succeeded",
  "publicNetworkAccess": "Enabled",
  "replicaCount": 1,
  "resourceGroup": "demo-westus",
  "sharedPrivateLinkResources": [],
  "sku": {
    "name": "standard"
  },
  "status": "running",
  "statusDetails": "",
  "tags": null,
  "type": "Microsoft.Search/searchServices"
}
```

### <a name="create-a-service-with-ip-rules"></a>Creación de un servicio con reglas de IP

En función de los requisitos de seguridad, es posible que desee crear un servicio de búsqueda con un [firewall de IP configurado](service-configure-firewall.md). Para ello, pase las direcciones IP públicas (v4) o los intervalos de CIDR al argumento `ip-rules`, como se muestra a continuación. Las reglas se deben separar con una coma (`,`) o con un punto y coma (`;`).

```azurecli-interactive
az search service create \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --ip-rules "55.5.63.73;52.228.215.197;101.37.221.205"
```

### <a name="create-a-service-with-a-system-assigned-managed-identity"></a>Creación de un servicio con una identidad administrada asignada por el sistema

En algunos casos, como cuando [se usa una identidad administrada para conectarse a un origen de datos](search-howto-managed-identities-storage.md), deberá activar la [identidad administrada asignada por el sistema](../active-directory/managed-identities-azure-resources/overview.md). Esto se hace agregando `--identity-type SystemAssigned` al comando.

```azurecli-interactive
az search service create \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --identity-type SystemAssigned
```

## <a name="create-a-service-with-a-private-endpoint"></a>Creación de un servicio con un punto de conexión privado

Los [puntos de conexión privados](../private-link/private-endpoint-overview.md) para Azure Cognitive Search permiten a un cliente de una red virtual obtener acceso de forma segura a los datos de un índice de búsqueda a través de un [vínculo privado](../private-link/private-link-overview.md). El punto de conexión privado usa una dirección IP del [espacio de direcciones de la red virtual](../virtual-network/private-ip-addresses.md) para el servicio de búsqueda. El tráfico de red entre el cliente y el servicio de búsqueda atraviesa la red virtual y un vínculo privado de la red troncal de Microsoft, lo que elimina la exposición a la red pública de Internet. Para obtener más información, consulte la documentación sobre la [creación de un punto de conexión privado para Azure Cognitive Search](service-create-private-endpoint.md).

En el ejemplo siguiente se muestra cómo crear un servicio de búsqueda con un punto de conexión privado.

En primer lugar, implemente un servicio de búsqueda con `PublicNetworkAccess` establecido en `Disabled`.

```azurecli-interactive
az search service create \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --sku Standard \
    --partition-count 1 \
    --replica-count 1 \
    --public-access Disabled
```

A continuación, cree una red virtual y el punto de conexión privado.

```azurecli-interactive
# Create the virtual network
az network vnet create \
    --resource-group <vnet-resource-group-name> \
    --location "West US" \
    --name <virtual-network-name> \
    --address-prefixes 10.1.0.0/16 \
    --subnet-name <subnet-name> \
    --subnet-prefixes 10.1.0.0/24

# Update the subnet to disable private endpoint network policies
az network vnet subnet update \
    --name <subnet-name> \
    --resource-group <vnet-resource-group-name> \
    --vnet-name <virtual-network-name> \
    --disable-private-endpoint-network-policies true

# Get the id of the search service
id=$(az search service show \
    --resource-group <search-service-resource-group-name> \
    --name <search-service-name> \
    --query [id] \
    --output tsv)

# Create the private endpoint
az network private-endpoint create \
    --name <private-endpoint-name> \
    --resource-group <private-endpoint-resource-group-name> \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id $id \
    --group-id searchService \
    --connection-name <private-link-connection-name>  
```

Por último, cree una zona DNS privada. 

```azurecli-interactive
## Create private DNS zone
az network private-dns zone create \
    --resource-group <private-dns-resource-group-name> \
    --name "privatelink.search.windows.net"

## Create DNS network link
az network private-dns link vnet create \
    --resource-group <private-dns-resource-group-name> \
    --zone-name "privatelink.search.windows.net" \
    --name "myLink" \
    --virtual-network <virtual-network-name> \
    --registration-enabled false

## Create DNS zone group
az network private-endpoint dns-zone-group create \
   --resource-group <private-endpoint-resource-group-name>\
   --endpoint-name <private-endpoint-name> \
   --name "myZoneGroup" \
   --private-dns-zone "privatelink.search.windows.net" \
   --zone-name "searchServiceZone"
```

Para más información sobre cómo crear puntos de conexión privados en PowerShell, consulte este [inicio rápido de Private Link](../private-link/create-private-endpoint-cli.md).

### <a name="manage-private-endpoint-connections"></a>Administración de conexiones de punto de conexión privado

Además de crear una conexión de punto de conexión privado, también puede aplicar `show`, `update` y `delete` a la conexión.

Para recuperar una conexión de punto de conexión privado y ver su estado, use [**az search private-endpoint-connection show**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_show).

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

Para actualizar la conexión, use [**az search private-endpoint-connection update**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_update). En el ejemplo siguiente se establece una conexión de punto de conexión privado como rechazada:

```azurecli-interactive
az search private-endpoint-connection show \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
    --status Rejected \
    --description "Rejected" \
    --actions-required "Please fix XYZ"
```

Para eliminar la conexión del punto de conexión privado, use [**az search private-endpoint-connection delete**](/cli/azure/search/private-endpoint-connection#az_search_private_endpoint_connection_delete).

```azurecli-interactive
az search private-endpoint-connection delete \
    --name <pe-connection-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

## <a name="regenerate-admin-keys"></a>Regeneración de claves de administración

Para sustituir las [claves de API](search-security-api-keys.md) de administración, use [**az search admin-key renew**](/cli/azure/search/admin-key#az_search_admin_key_renew). Se crean dos claves de administración con cada servicio para el acceso autenticado. Las claves son necesarias en cada solicitud. Ambas claves de administración son funcionalmente equivalentes, concediendo acceso completo de escritura a un servicio de búsqueda con la capacidad de recuperar cualquier información, o crear y eliminar cualquier objeto. Existen dos claves, de forma que puede usar una mientras reemplaza la otra. 

Solo puede regenerar una en cada momento, especificada como clave `primary` o `secondary`. Para un servicio ininterrumpido, no olvide actualizar todo el código de cliente para usar una clave secundaria mientras sustituye la clave principal. Evite cambiar las claves mientras haya operaciones en tránsito.

Como puede imaginar, si regenera las claves sin actualizar el código de cliente, se producirá un error en las solicitudes que usan la clave antigua. La regeneración de todas las claves nuevas no lo bloquea permanentemente para acceder al servicio y puede seguir accediendo a este a través del portal. Después de regenerar las claves principal y secundaria, puede actualizar el código de cliente para utilizar las nuevas claves y las operaciones se reanudarán en consecuencia.

El servicio genera los valores de las claves de API. No puede proporcionar una clave personalizada para que la use Azure Cognitive Search. De forma similar, no hay ningún nombre definido por el usuario para las claves de API de administración. Las referencias a la clave son cadenas fijas, ya sea `primary` o `secondary`. 

```azurecli-interactive
az search admin-key renew \
    --resource-group <search-service-resource-group-name> \
    --service-name <search-service-name> \
    --key-kind primary
```

Los resultados deben tener un aspecto similar a la siguiente salida. Se devuelven las dos claves aunque solo cambie una a la vez.

```   
{
  "primaryKey": <alphanumeric-guid>,
  "secondaryKey": <alphanumeric-guid>  
}
```

## <a name="create-or-delete-query-keys"></a>Creación o eliminación de claves de consulta

Para crear [claves de API](search-security-api-keys.md) de consulta con acceso de solo lectura desde aplicaciones cliente en un índice de Azure Cognitive Search, use [**az search query-key create**](/cli/azure/search/query-key#az_search_query_key_create). Las claves de consulta se utilizan para autenticarse en un índice específico con el fin de recuperar los resultados de búsqueda. Las claves de consulta no conceden acceso de solo lectura a otros elementos en el servicio, como un índice, origen de datos o indizador.

No puede proporcionar una clave para que la use Azure Cognitive Search. El servicio genera las claves de API.

```azurecli-interactive
az search query-key create \
    --name myQueryKey \
    --resource-group <search-service-resource-group-name> \
    --service-name <search-service-name>
```

## <a name="scale-replicas-and-partitions"></a>Escalado de réplicas y particiones

Para [aumentar o disminuir réplicas y particiones](search-capacity-planning.md), use [**az search service update**](/cli/azure/search/service#az_search_service_update). El aumento de las réplicas o particiones se agrega a la factura, que tiene cargos fijos y variables. Si tiene una necesidad temporal de potencia de procesamiento adicional, puede aumentar las réplicas y particiones para controlar la carga de trabajo. El área de supervisión de la página Introducción del portal tiene iconos en la latencia de consulta, consultas por segundo y limitación, que indican si la capacidad actual es adecuada.

La incorporación o retirada de recursos puede tardar un unos minutos. Los ajustes de capacidad se producen en segundo plano, lo que permite que las cargas de trabajo existentes continúen. La capacidad adicional se usa para las solicitudes entrantes en cuanto esté lista, sin ninguna configuración adicional necesaria. 

La retirada de la capacidad puede ser perjudicial. Para evitar la eliminación de solicitudes, es recomendable detener todos los trabajos de indexación y del indexador antes de reducir la capacidad. Si esto no es factible, podría plantearse reducir la capacidad de forma incremental, una réplica y partición a la vez, hasta que se alcancen los nuevos niveles marcados como objetivo.

Una vez que envíe el comando, no hay forma de terminarlo a la mitad. Tendrá que esperar hasta que el comando finalice antes de revisar los recuentos.

```azurecli-interactive
az search service update \
    --name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --partition-count 6 \
    --replica-count 6
```

Además de actualizar los recuentos de réplicas y particiones, también puede actualizar `ip-rules`, `public-access` y `identity-type`.

## <a name="create-a-shared-private-link-resource"></a>Creación de un recurso compartido de vínculo privado

Los puntos de conexión privados de recursos protegidos que se crean a través de las API de Azure Cognitive Search se conocen como *recursos de vínculo privado compartido*. Eso se debe a que "comparte" el acceso a un recurso, como una cuenta de almacenamiento, que se ha integrado en el [servicio Azure Private Link](https://azure.microsoft.com/services/private-link/).

Si usa un indexador para indexar datos en Azure Cognitive Search y el origen de datos se encuentra en una red privada, puede crear una [conexión de punto de conexión privado](../private-link/private-endpoint-overview.md) de salida para acceder a los datos.

Puede encontrar una lista completa de los recursos de Azure para los que puede crear puntos de conexión privados de salida desde Azure Cognitive Search [aquí](search-indexer-howto-access-private.md#shared-private-link-resources-management-apis), junto con los valores del **identificador de grupo** relacionados.

Para crear el recurso compartido de Private Link, use [**az search shared-private-link-resource create**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list). Tenga en cuenta que es posible que se requiera una configuración para el origen de datos antes de ejecutar este comando.

```azurecli-interactive
az search shared-private-link-resource create \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> \
    --group-id blob \
    --resource-id "/subscriptions/<alphanumeric-subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/myBlobStorage"  \
    --request-message "Please approve" 
```


Para recuperar los recursos compartidos de Private Link y ver su estado, use [**az search shared-private-link-resource list**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_list).

```azurecli-interactive
az search shared-private-link-resource list \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

Deberá aprobar la conexión con el comando siguiente para poder usarlo. El identificador de la conexión del punto de conexión privado deberá recuperarse del recurso secundario. En este caso, obtenemos el identificador de la conexión de az storage.

```azurecli-interactive
id = (az storage account show -n myBlobStorage --query "privateEndpointConnections[0].id")

az network private-endpoint-connection approve --id $id
```

Para eliminar el recurso compartido de Private Link, use [**az search shared-private-link-resource delete**](/cli/azure/search/shared-private-link-resource#az_search_shared_private_link_resource_delete).

```azurecli-interactive
az search shared-private-link-resource delete \
    --name <spl-name> \
    --service-name <search-service-name> \
    --resource-group <search-service-resource-group-name> 
```

Para obtener información detallada sobre la configuración de recursos compartidos de vínculo privado, consulte la documentación sobre la [creación de conexiones de indexador a través de un punto de conexión privado](search-indexer-howto-access-private.md).

## <a name="next-steps"></a>Pasos siguientes

Cree un [índice](search-what-is-an-index.md) y [consulte un índice](search-query-overview.md) mediante el portal, API REST o el SDK de .NET.

* [Creación de un índice de Azure Cognitive Search en Azure Portal](search-get-started-portal.md)
* [Configuración de un indexador para cargar datos desde otros servicios](search-indexer-overview.md)
* [Consulta de un índice de Azure Cognitive Search mediante el Explorador de búsqueda de Azure Portal](search-explorer.md)
* [Uso de Azure Cognitive Search en .NET](search-howto-dotnet-sdk.md)