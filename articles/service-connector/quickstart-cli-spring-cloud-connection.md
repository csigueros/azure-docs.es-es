---
title: 'Inicio rápido: Creación de una conexión de servicio en Spring Cloud con la CLI de Azure'
description: Inicio rápido que muestra cómo crear una conexión de servicio en Spring Cloud con la CLI de Azure
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4ee0fffb402ba3553055c732df9e0ebad94ea511
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091217"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-with-the-azure-cli"></a>Inicio rápido: Creación de una conexión de servicio en Spring Cloud con la CLI de Azure

La [Interfaz de la línea de comandos (CLI) de Azure](/cli/azure) es un conjunto de comandos que se usa para crear y administrar recursos de Azure. La CLI de Azure está disponible en los servicios de Azure y está diseñada para ayudarle a trabajar rápidamente con Azure, con especial atención a la automatización. En este inicio rápido se muestran las opciones para crear una instancia de Azure Web PubSub con la CLI de Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Para realizar este inicio rápido es necesaria la versión 2.22.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

- En este inicio rápido se da por supuesto que tiene al menos una aplicación Spring Cloud que se ejecuta en Azure. Si no tiene una aplicación Spring Cloud, puede [crearla](../spring-cloud/quickstart.md).


## <a name="view-supported-target-service-types"></a>Visualización de los tipos de servicio de destino admitidos

Use el comando [az spring-cloud connection]() de la CLI de Azure para crear y administrar las conexiones de servicio a una aplicación de Spring Cloud. 

```azurecli-interactive
az spring-cloud connection list-support-types
```

## <a name="create-a-service-connection"></a>Creación de una conexión de servicio

Use el comando [az spring-cloud connection]() de la CLI de Azure para crear una conexión de servicio a un almacenamiento de blobs, proporcionando la siguiente información:

- **Nombre del grupo de recursos de Spring Cloud:** el nombre del grupo de recursos de Spring Cloud.
- **Nombre de Spring Cloud:** nombre de Spring Cloud.
- **Nombre de la aplicación de Spring Cloud:** el nombre de la aplicación de Spring Cloud que se conecta al servicio de destino.
- **Nombre del grupo de recursos del servicio de destino:** el nombre del grupo de recursos de la aplicación del almacenamiento de blobs.
- **Nombre de cuenta de almacenamiento:** el nombre de la cuenta de almacenamiento de blobs.

```azurecli-interactive
az spring-cloud connection create storage-blob -g <spring_cloud_resource_group> --service <spring_cloud_name> --app <app_name> --deployment default --tg <storage_resource_group> --account <storage_account_name> --system-identity
```

> [!NOTE]
> Si no tiene un almacenamiento de blobs, puede ejecutar `az spring-cloud connection create storage-blob -g <app_service_resource_group> -n <app_service_name> --tg <storage_resource_group> --account <storage_account_name> --system-identity --new` para aprovisionar uno nuevo y conectarse directamente a App Service.

## <a name="view-connections"></a>Ver conexiones

Use el comando [az spring-cloud connection]() de la CLI de Azure para enumerar la conexión a una aplicación de Spring Cloud, para lo que proporciona la siguiente información:

```azurecli-interactive
az spring-cloud connection list -g <your-spring-cloud-resource-group> --spring-cloud <your-spring-cloud-name>
```

## <a name="next-steps"></a>Pasos siguientes

Complete los tutoriales que se indican a continuación para empezar a crear su propia aplicación con Service Connector.

> [!div class="nextstepaction"]
> [Tutorial: Spring Cloud y MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [Tutorial: Spring Cloud y Apache Kafka en la nube de Confluent](./tutorial-java-spring-confluent-kafka.md)
