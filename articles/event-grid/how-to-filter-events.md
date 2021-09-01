---
title: Filtrado de eventos para Azure Event Grid
description: En este artículo se muestra cómo filtrar eventos (por tipo de evento, por asunto, por operadores y datos, etc.) al crear una suscripción de Event Grid.
ms.topic: conceptual
ms.date: 08/11/2021
ms.openlocfilehash: 666690fc7c0d21c7ae257a20b49949961d89cb60
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744637"
---
# <a name="filter-events-for-event-grid"></a>Filtrado de eventos para Event Grid

En este artículo se muestra cómo filtrar eventos al crear una suscripción de Event Grid. Para obtener información sobre las opciones de filtrado de eventos, consulte [Understand event filtering for Event Grid subscriptions](event-filtering.md) (Descripción del filtrado de events para suscripciones de Event Grid).

## <a name="filter-by-event-type"></a>Filtrar por tipo de evento

Al crear una suscripción de Event Grid, puede especificar qué [tipos de evento](event-schema.md) se envían al punto de conexión. En los ejemplos de esta sección se crean suscripciones de eventos para un grupo de recursos, pero se limitan los eventos que se envían a `Microsoft.Resources.ResourceWriteFailure` y `Microsoft.Resources.ResourceWriteSuccess`. Si necesita más flexibilidad al filtrar eventos por tipos de eventos, consulte Filtrado por operadores avanzados y campos de datos.

### <a name="azure-powershell"></a>Azure PowerShell
Para PowerShell, use el parámetro `-IncludedEventType` al crear la suscripción.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

### <a name="azure-cli"></a>Azure CLI
Para la CLI de Azure, use el parámetro `--included-event-types`. En el ejemplo siguiente se usa la CLI de Azure en un shell de Bash:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

### <a name="azure-portal"></a>Azure portal

1. En la página **Suscripción a eventos**, cambie a la pestaña **Filtros**. 
1. Seleccione **Agregar un tipo de evento** situado junto a **Filtro para tipos de evento**. 

    :::image type="content" source="./media/how-to-filter-events/add-event-type-button.png" alt-text="Captura de pantalla de la página Suscripción a eventos con el botón Agregar un tipo de evento seleccionado.":::    
1. Escriba el tipo de evento y presione ENTRAR. En el ejemplo siguiente, el tipo de evento es `Microsoft.Resources.ResourceWriteSuccess`. 

    :::image type="content" source="./media/how-to-filter-events/sample-event-type.png" alt-text="Captura de pantalla de la página Suscripción a eventos con un tipo de evento de ejemplo.":::    
      
### <a name="azure-resource-manager-template"></a>Plantilla del Administrador de recursos de Azure
Para una plantilla de Resource Manager, use la propiedad `includedEventTypes`.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

> [!NOTE]
> Para más información sobre estos filtros (tipos de eventos, asuntos y avanzados), consulte [Descripción del filtrado de eventos para suscripciones de Event Grid](event-filtering.md). 

## <a name="filter-by-subject"></a>Filtrar por asunto

Puede filtrar eventos por el asunto de los datos del evento. Puede especificar un valor que debe coincidir con el principio o el final del asunto. Si necesita más flexibilidad al filtrar eventos por asunto, consulte Filtrado por operadores avanzados y campos de datos.

En el siguiente ejemplo de PowerShell, se crea una suscripción de eventos que filtra por el principio del asunto. Se usa el parámetro `-SubjectBeginsWith` para limitar los eventos a aquellos de un recurso específico. Se pasa el identificador de recurso de un grupo de seguridad de red.

### <a name="azure-powershell"></a>Azure PowerShell
```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

En el siguiente ejemplo de PowerShell se crea una suscripción para un almacenamiento de blobs. Se limitan los eventos a aquellos con un asunto que termina por `.jpg`.

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

### <a name="azure-cli"></a>Azure CLI
En el siguiente ejemplo de la CLI de Azure, se crea una suscripción de eventos que filtra por el principio del asunto. Se usa el parámetro `--subject-begins-with` para limitar los eventos a aquellos de un recurso específico. Se pasa el identificador de recurso de un grupo de seguridad de red.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

En el siguiente ejemplo de la CLI de Azure se crea una suscripción para un almacenamiento de blobs. Se limitan los eventos a aquellos con un asunto que termina por `.jpg`.

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

### <a name="azure-portal"></a>Azure portal

1. En la página **Suscripción a eventos**, seleccione **Habilitar el filtrado del asunto**. 
1. Especifique los valores de uno o varios de los siguientes campos: **El asunto comienza por** y **El asunto termina con**. En las siguientes opciones se seleccionan ambas opciones. 

    :::image type="content" source="./media/how-to-filter-events/subject-filter-example.png" alt-text="Captura de pantalla de la página Suscripción a eventos con el ejemplo de filtrado por asuntos.":::
1. Seleccione la opción **La coincidencia de asunto distingue mayúsculas de minúsculas** si desea que el asunto del evento coincida con las mayúsculas y minúsculas de los filtros especificados. 

### <a name="azure-resource-manager-template"></a>Plantilla del Administrador de recursos de Azure
En el siguiente ejemplo de plantilla de Resource Manager, se crea una suscripción de eventos que filtra por el principio del asunto. Se usa la propiedad `subjectBeginsWith` para limitar los eventos a aquellos de un recurso específico. Se pasa el identificador de recurso de un grupo de seguridad de red.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

En el siguiente ejemplo de plantilla de Resource Manager se crea una suscripción para un almacenamiento de blobs. Se limitan los eventos a aquellos con un asunto que termina por `.jpg`.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

> [!NOTE]
> Para más información sobre estos filtros (tipos de eventos, asuntos y avanzados), consulte [Descripción del filtrado de eventos para suscripciones de Event Grid](event-filtering.md). 

## <a name="filter-by-operators-and-data"></a>Filtrado por operadores y datos

Para obtener más flexibilidad en el filtrado, puede usar propiedades de datos y operadores para filtrar eventos.

### <a name="subscribe-with-advanced-filters"></a>Suscripción con filtros avanzados

Para obtener información acerca de los operadores y las claves que puede usar para el filtrado avanzado, consulte [Advanced filtering](event-filtering.md#advanced-filtering) (Filtrado avanzado).

En estos ejemplos se crea un tema personalizado. Se suscriben al tema personalizado y filtra por un valor en el objeto de datos. Los eventos que tienen la propiedad de color establecida en azul, rojo o verde se envían a la suscripción.

### <a name="azure-powershell"></a>Azure PowerShell

Para PowerShell, use:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="azure-cli"></a>Azure CLI

Para la CLI de Azure, utilice:

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Tenga en cuenta que se ha establecido una [fecha de expiración](concepts.md#event-subscription-expiration) para la suscripción.


### <a name="azure-portal"></a>Azure portal

1. En la página **Suscripción a eventos**, seleccione **Agregar nuevo filtro** en la sección **FILTROS AVANZADOS**. 

    :::image type="content" source="./media/how-to-filter-events/add-new-filter-button.png" alt-text="Captura de pantalla que muestra la página Suscripción A eventos con el vínculo Agregar nuevo filtro resaltado.":::    
2. Especifique una clave, un operador y uno o varios valores para comparar. En el ejemplo siguiente se usa **data.color** como clave, **La cadena está en** como operador y **azul**, **rojo** y **verde** como valores. 

    :::image type="content" source="./media/how-to-filter-events/advanced-filter-example.png" alt-text="Captura de pantalla que muestra un ejemplo de un filtro avanzado."::: 

    > [!NOTE]
    > Para más información sobre los filtros avanzados, consulte [Descripción del filtrado de eventos para suscripciones de Event Grid](event-filtering.md). 


### <a name="test-the-filter"></a>Prueba del filtro
Para probar el filtro, envíe un evento con el campo de color establecido en verde. Dado que verde es uno de los valores del filtro, el evento se entrega en el punto de conexión.

### <a name="azure-powershell"></a>Azure PowerShell
Para PowerShell, use:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Para probar un escenario donde no se envía el evento, envíe un evento con el campo de color establecido en amarillo. El amarillo no es uno de los valores especificados en la suscripción, por lo que el evento no se entrega a su suscripción.

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```


### <a name="azure-cli"></a>Azure CLI
Para la CLI de Azure, utilice:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

Para probar un escenario donde no se envía el evento, envíe un evento con el campo de color establecido en amarillo. El amarillo no es uno de los valores especificados en la suscripción, por lo que el evento no se entrega a su suscripción.

Para la CLI de Azure, utilice:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los filtros (tipos de eventos, asuntos y avanzados), consulte [Descripción del filtrado de eventos para suscripciones de Event Grid](event-filtering.md). 

