---
title: Suscripción a eventos de Azure Kubernetes Service con Azure Event Grid (versión preliminar)
description: Use Azure Event Grid para suscribirse a los eventos de Azure Kubernetes Service.
services: container-service
author: zr-msft
ms.topic: article
ms.date: 07/12/2021
ms.author: zarhoads
ms.openlocfilehash: cc4801dc588216cbcfb2c6c2742643b2e0546e63
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113734241"
---
# <a name="quickstart-subscribe-to-azure-kubernetes-service-aks-events-with-azure-event-grid-preview"></a>Inicio rápido: Suscripción a eventos de Azure Kubernetes Service (AKS) con Azure Event Grid (versión preliminar)

Azure Event Grid es un servicio de enrutamiento de eventos completamente administrado que ofrece un consumo de eventos uniforme mediante un modelo de publicación-suscripción.

En este inicio rápido, creará un clúster de AKS y se suscribirá a eventos de AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free).
* [La CLI de Azure instalada](/cli/azure/install-azure-cli).

### <a name="register-the-eventgridpreview-preview-feature"></a>Registro de la característica en vista previa (GB) `EventgridPreview`

Para usar la característica, también debe habilitar la marca de característica `EventgridPreview` en la suscripción.

Registre la marca de la característica `EventgridPreview` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EventgridPreview"
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EventgridPreview')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [event-grid-register-provider-cli.md](../../includes/event-grid-register-provider-cli.md)]

## <a name="create-an-aks-cluster"></a>Creación de un clúster de AKS

Cree un clúster de AKS con el comando [az aks create][az-aks-create]. En el ejemplo siguiente se crea un grupo de recursos *MyResourceGroup* y un clúster llamado *MyAKS* con un nodo en el grupo de recursos *MyResourceGroup*:

```azurecli
az group create --name MyResourceGroup --location eastus
az aks create -g MyResourceGroup -n MyAKS --location eastus  --node-count 1 --generate-ssh-keys
```

## <a name="subscribe-to-aks-events"></a>Suscripción a eventos de AKS

Cree un espacio de nombres y un centro de eventos mediante [az eventhubs namespace create][az-eventhubs-namespace-create] y [az eventhubs eventhub create][az-eventhubs-eventhub-create]. En el ejemplo siguiente se crea un espacio de nombres *MyNamespace* y un centro de eventos *MyEventGridHub* en *MyNamespace*, ambos en el grupo de recursos *MyResourceGroup*.

```azurecli
az eventhubs namespace create --location eastus --name MyNamespace -g MyResourceGroup
az eventhubs eventhub create --name MyEventGridHub --namespace-name MyNamespace -g MyResourceGroup
```

> [!NOTE]
> El *nombre* del espacio de nombres debe ser único.

Suscríbase a los eventos de AKS mediante [az eventgrid event-subscription create][az-eventgrid-event-subscription-create]:

```azurecli
SOURCE_RESOURCE_ID=$(az aks show -g MyResourceGroup -n MyAKS --query id --output tsv)
ENDPOINT=$(az eventhubs eventhub show -g MyResourceGroup -n MyEventGridHub --namespace-name MyNamespace --query id --output tsv)
az eventgrid event-subscription create --name MyEventGridSubscription \
--source-resource-id $SOURCE_RESOURCE_ID \
--endpoint-type eventhub \
--endpoint $ENDPOINT
```

Compruebe la suscripción a los eventos de AKS mediante `az eventgrid event-subscription list`:

```azurecli
az eventgrid event-subscription list --source-resource-id $SOURCE_RESOURCE_ID
```

En la siguiente salida de ejemplo se muestra que está suscrito a eventos del clúster *MyAKS*, y esos eventos se entregan al centro de eventos *MyEventGridHub*:

```output
$ az eventgrid event-subscription list --source-resource-id $SOURCE_RESOURCE_ID
[
  {
    "deadLetterDestination": null,
    "deadLetterWithResourceIdentity": null,
    "deliveryWithResourceIdentity": null,
    "destination": {
      "deliveryAttributeMappings": null,
      "endpointType": "EventHub",
      "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/Microsoft.EventHub/namespaces/MyNamespace/eventhubs/MyEventGridHub"
    },
    "eventDeliverySchema": "EventGridSchema",
    "expirationTimeUtc": null,
    "filter": {
      "advancedFilters": null,
      "enableAdvancedFilteringOnArrays": null,
      "includedEventTypes": [
        "Microsoft.ContainerService.NewKubernetesVersionAvailable"
      ],
      "isSubjectCaseSensitive": null,
      "subjectBeginsWith": "",
      "subjectEndsWith": ""
    },
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/MyAKS/providers/Microsoft.EventGrid/eventSubscriptions/MyEventGridSubscription",
    "labels": null,
    "name": "MyEventGridSubscription",
    "provisioningState": "Succeeded",
    "resourceGroup": "MyResourceGroup",
    "retryPolicy": {
      "eventTimeToLiveInMinutes": 1440,
      "maxDeliveryAttempts": 30
    },
    "systemData": null,
    "topic": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/MyResourceGroup/providers/microsoft.containerservice/managedclusters/MyAKS",
    "type": "Microsoft.EventGrid/eventSubscriptions"
  }
]
```

Cuando se produzcan eventos de AKS, verá que esos eventos aparecen en el centro de eventos. Por ejemplo, cuando cambie la lista de versiones disponibles de Kubernetes para los clústeres, verá un evento `Microsoft.ContainerService.NewKubernetesVersionAvailable`. Para más información sobre los eventos que AKS emite, vea [Azure Kubernetes Service (AKS) como origen de Event Grid][aks-events].

## <a name="delete-the-cluster-and-subscriptions"></a>Eliminación del clúster y las suscripciones

Use el comando [az group delete][az-group-delete] para eliminar el grupo de recursos, el clúster de AKS, el espacio de nombres, el centro de eventos y todos los recursos relacionados.

```azurecli-interactive
az group delete --name MyResourceGroup --yes --no-wait
```

> [!NOTE]
> Cuando elimina el clúster, la entidad de servicio Azure Active Directory que utiliza el clúster de AKS no se quita. Para conocer los pasos que hay que realizar para quitar la entidad de servicio, consulte [Consideraciones principales y eliminación de AKS][sp-delete].
> 
> Si usó una identidad administrada, esta está administrada por la plataforma y no requiere que la quite.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha implementado un clúster de Kubernetes y, a continuación, se ha suscrito a eventos de AKS en Azure Event Hubs.

Para obtener más información sobre AKS y un ejemplo completo desde el código hasta la implementación, continúe con el tutorial del clúster de Kubernetes.

> [!div class="nextstepaction"]
> [Tutorial de AKS][aks-tutorial]

[aks-events]: ../event-grid/event-schema-aks.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-eventhubs-namespace-create]: /cli/azure/eventhubs/namespace?view=azure-cli-latest&preserve-view=true#az-eventhubs-namespace-create
[az-eventhubs-eventhub-create]: /cli/azure/eventhubs/eventhub?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-create
[az-eventgrid-event-subscription-create]: /cli/azure/eventgrid/event-subscription?view=azure-cli-latest&preserve-view=true#az-eventgrid-event-subscription-create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-group-delete]: /cli/azure/group#az_group_delete
[sp-delete]: kubernetes-service-principal.md#additional-considerations
