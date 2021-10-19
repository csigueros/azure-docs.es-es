---
author: trngo
description: Aprenda a eliminar contenedores individuales mediante az-cli
title: Eliminación de contenedores de Fluid
ms.author: trngo
ms.date: 09/28/2021
ms.service: azure-fluid
ms.topic: reference
ms.openlocfilehash: 8a94c06bd80b6997c5bd80cbb147bf68ccf91a4b
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728672"
---
# <a name="delete-fluid-containers-in-microsoft-azure-fluid-relay-server"></a>Eliminación de contenedores de Fluid en el servidor de Microsoft Azure Fluid Relay

> [!NOTE]
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.

En este escenario, eliminaremos un contenedor de Fluid existente. Una vez eliminado un contenedor, las aplicaciones que hacen referencia al contenedor ya no podrán acceder al contenedor ni a sus datos. 

## <a name="requirements-to-delete-a-fluid-container"></a>Requisitos para eliminar un contenedor de Fluid
- Para empezar, es preciso instalar la [CLI de Azure](/cli/azure/install-azure-cli). Si ya tiene instalada la CLI de Azure, asegúrese de que la versión es 2.0.67 o posterior mediante la ejecución de `az version`.
- Para eliminar un contenedor de Fluid, debe asegurarse de que la aplicación y sus clientes ya no están conectados al contenedor.

## <a name="list-the-containers-within-a-fluid-relay-resource"></a>Enumeración de los contenedores dentro de un recurso de Fluid Relay
Para ver todos los contenedores que pertenecen al recurso de Fluid Relay, puede ejecutar el siguiente comando:
```
az rest --method get --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers?api-version=<apiVersion>
```
**subscriptionId**: identificador de la suscripción de Azure a la que pertenece el recurso de Fluid Relay.

**resourceGroupName**: nombre del grupo de recursos en el que se encuentra el recurso de Fluid Relay.

**frsResourceName**: nombre del recurso de Fluid Relay. Tenga en cuenta que esto es diferente a tenantId del recurso de Fluid Relay.

**apiVersion**: versión de API del proveedor de recursos. La versión mínima admitida es **2021-08-30-preview**.  


## <a name="sample-output"></a>Salida de ejemplo
La salida contendrá una lista de contenedores que pertenecen al recurso de Fluid Relay y sus propiedades.
```json
{
  "value": [
    {
      "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.FluidRelay/fluidRelayServers/<frsResourcename>/fluidRelayContainers/<containerId>",
      "name": "<containerId>",
      "properties": {
        "frsContainerId": "<containerId>",
        "frsTenantId": "<frsResourceTenantId>"
      },
      "resourceGroup": "<resourceGroupname>",
      "type": "Microsoft.FluidRelay/fluidRelayServers/fluidRelayContainers"
    },
    ...
  ]
}
```


## <a name="delete-an-existing-container"></a>Eliminación de un contenedor existente
Para eliminar un contenedor, debe identificar el **containerId** del contenedor de la salida anterior y ejecutar el siguiente comando: 
```
az rest --method delete --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers/<frsContainerId>?api-version=<api-version>
```
  **frsContainerId**: identificador del contenedor de Fluid que se va a eliminar. 
