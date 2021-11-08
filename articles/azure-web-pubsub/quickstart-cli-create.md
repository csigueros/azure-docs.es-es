---
title: 'Inicio rápido: Creación de una instancia de Web PubSub con la CLI de Azure'
description: Inicio rápido que explica la creación de una instancia de Web PubSub con la CLI de Azure
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: quickstart
ms.date: 11/08/2021
ms.openlocfilehash: 426b77d1e1a79c3a9818e028edcc79bcad5ef2ec
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131998089"
---
# <a name="quickstart-create-a-web-pubsub-instance-with-the-azure-cli"></a>Inicio rápido: Creación de una instancia de Web PubSub con la CLI de Azure

La [Interfaz de la línea de comandos (CLI) de Azure](/cli/azure) es un conjunto de comandos que se usa para crear y administrar recursos de Azure. La CLI de Azure está disponible en los servicios de Azure y está diseñada para ayudarle a trabajar rápidamente con Azure, con especial atención a la automatización. En este inicio rápido se muestran las opciones para crear una instancia de Azure Web PubSub con la CLI de Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Para realizar este inicio rápido es necesaria la versión 2.22.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create a resource group](includes/cli-rg-creation.md)]

## <a name="create-a-web-pubsub-instance"></a>Creación de una instancia de Web PubSub

[!INCLUDE [Create a Web PubSub instance](includes/cli-awps-creation.md)]

## <a name="try-the-newly-created-instance"></a>Prueba de la instancia recién creada

> [!div class="nextstepaction"]
> [Pruebe la instancia recién creada con la CLI](./quickstart-cli-try.md#play-with-the-instance)

> [!div class="nextstepaction"]
> [Pruebe la instancia recién creada desde el explorador](./quickstart-live-demo.md#try-the-instance-with-an-online-demo)

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [Clean up resource](includes/cli-delete-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En las aplicaciones del mundo real, puede usar SDK en varios lenguajes para compilar su propia aplicación. También proporcionamos extensiones de función para que pueda crear aplicaciones sin servidor fácilmente.

[!INCLUDE [next step](includes/include-next-step.md)]
