---
title: Creación de un centro de eventos mediante la CLI de Azure con Azure Event Hubs | Microsoft Docs
description: En esta guía de inicio rápido se describe cómo crear un centro de eventos mediante la CLI de Azure y, a continuación, cómo enviar y recibir eventos mediante Java.
ms.topic: quickstart
ms.date: 06/18/2021
ms.author: spelluru
ms.custom: devx-track-azurecli
ms.openlocfilehash: 779d83cbf53423c3b4b6c6d8ce2c27c0c9674da7
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414816"
---
# <a name="quickstart-create-an-event-hub-using-azure-cli"></a>Guía de inicio rápido: Creación de un centro de eventos mediante la CLI de Azure

Azure Event Hubs es una plataforma de streaming de macrodatos y servicio de ingesta de eventos de gran escalabilidad capaz de recibir y procesar millones de eventos por segundo. Event Hubs puede procesar y almacenar eventos, datos o telemetría generados por dispositivos y software distribuido. Los datos enviados a un centro de eventos se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de procesamiento por lotes y almacenamiento. Para más información sobre Event Hubs, consulte [Introducción a Event Hubs](event-hubs-about.md) y [Características de Event Hubs](event-hubs-features.md).

En esta guía de inicio rápido se crea un centro de eventos mediante la CLI de Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- En este artículo se necesita la versión 2.0.4 de la CLI de Azure, o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="set-the-subscription-context"></a>Establecimiento del contexto de la suscripción

Los pasos siguientes no son necesarios si ejecuta comandos en Cloud Shell. Si está ejecutando la CLI localmente, realice los pasos siguientes para iniciar sesión en Azure y establecer su suscripción actual:

Establezca el contexto de la suscripción actual. Reemplace `MyAzureSub` por el nombre de la suscripción de Azure que desea usar:

```azurecli-interactive
az account set --subscription MyAzureSub
``` 

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
Un grupo de recursos es una recopilación lógica de recursos de Azure. Todos los recursos se implementan y administran en un grupo de recursos. Ejecute el comando siguiente para crear un grupo de recursos:

```azurecli-interactive
# Create a resource group. Specify a name for the resource group.
az group create --name <resource group name> --location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Creación de un espacio de nombres de Event Hubs
Un espacio de nombres de Event Hubs proporciona un contenedor con un único ámbito, al que hace referencia su nombre de dominio completo, en el que puede crear uno o varios centros de eventos. Para crear un espacio de nombres en el grupo de recursos, ejecute el comando siguiente:

```azurecli-interactive
# Create an Event Hubs namespace. Specify a name for the Event Hubs namespace.
az eventhubs namespace create --name <Event Hubs namespace> --resource-group <resource group name> -l <region, for example: East US>
```

## <a name="create-an-event-hub"></a>Creación de un centro de eventos
Ejecute el comando siguiente para crear un centro de eventos:

```azurecli-interactive
# Create an event hub. Specify a name for the event hub. 
az eventhubs eventhub create --name <event hub name> --resource-group <resource group name> --namespace-name <Event Hubs namespace>
```

¡Enhorabuena! Ha usado la CLI de Azure para crear un espacio de nombres de Event Hubs y un centro de eventos dentro de ese espacio de nombres. 

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha creado un grupo de recursos, un espacio de nombres de Event Hubs y un centro de eventos. Para encontrar instrucciones paso a paso sobre cómo enviar eventos a un centro de eventos o recibirlos de este, consulte los tutoriales sobre **envío y recepción de eventos**: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (solo enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (solo recibir)](event-hubs-storm-getstarted-receive.md)

[create a free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
