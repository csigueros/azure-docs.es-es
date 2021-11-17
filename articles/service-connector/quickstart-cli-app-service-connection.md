---
title: 'Inicio rápido: Creación de una conexión de servicio en App Service con la CLI de Azure'
description: Inicio rápido que muestra cómo crear una conexión de servicio en App Service con la CLI de Azure
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: quickstart
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5e4864db07dbf7618e5b8e03a29e08c1344d7ebd
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553408"
---
# <a name="quickstart-create-a-service-connection-in-app-service-with-the-azure-cli"></a>Inicio rápido: Creación de una conexión de servicio en App Service con la CLI de Azure

La [Interfaz de la línea de comandos (CLI) de Azure](/cli/azure) es un conjunto de comandos que se usa para crear y administrar recursos de Azure. La CLI de Azure está disponible en los servicios de Azure y está diseñada para ayudarle a trabajar rápidamente con Azure, con especial atención a la automatización. En este inicio rápido se muestran las opciones para crear una instancia de Azure Web PubSub con la CLI de Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Para realizar este inicio rápido es necesaria la versión 2.30.0 o posterior de la CLI de Azure. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.

- En este inicio rápido, se da por supuesto que ya tiene al menos una instancia de App Service en ejecución en Azure. Si no tiene una instancia de App Service, [créela](../app-service/quickstart-dotnetcore.md).

## <a name="view-supported-target-service-types"></a>Visualización de los tipos de servicio de destino admitidos

Use el comando [az webapp connection](/cli/azure/webapp/connection) de la CLI de Azure para crear y administrar las conexiones de servicio a App Service. 

```azurecli-interactive
az provider register -n Microsoft.ServiceLinker
az webapp connection list-support-types --output table
```

## <a name="create-a-service-connection"></a>Creación de una conexión de servicio

#### <a name="using-access-key"></a>[Con la clave de acceso](#tab/Using-access-key)

Use el comando [az webapp connection](/cli/azure/webapp/connection) de la CLI de Azure para crear una conexión de servicio a un almacenamiento de blobs con la clave de acceso, proporcionando la siguiente información:

- **Nombre del grupo de recursos del servicio de proceso de origen:** el nombre del grupo de recursos de la aplicación de App Service.
- **Nombre de la aplicación de App Service:** el nombre de la aplicación de App Service que se conecta al servicio de destino.
- **Nombre del grupo de recursos del servicio de destino:** el nombre del grupo de recursos de la aplicación del almacenamiento de blobs.
- **Nombre de cuenta de almacenamiento:** el nombre de la cuenta de almacenamiento de blobs.

```azurecli-interactive
az webapp connection create storage-blob --secret
```

> [!NOTE]
> Si no tiene un almacenamiento de blobs, puede ejecutar `az webapp connection create storage-blob --new --secret` para aprovisionar uno nuevo y conectarse directamente a App Service.

#### <a name="using-managed-identity"></a>[Con la identidad administrada](#tab/Using-Managed-Identity)

> [!IMPORTANT]
> El uso de una identidad administrada requiere que tenga el permiso para la [asignación de roles de Azure AD](/azure/active-directory/managed-identities-azure-resources/howto-assign-access-portal). Si no tiene el permiso, se producirá un error en la creación de la conexión. Puede pedir al propietario de la suscripción el permiso o usar la clave de acceso para crear la conexión.

Use el comando [az webapp connection](/cli/azure/webapp/connection) de la CLI de Azure para crear una conexión de servicio a un almacenamiento de blobs con la identidad administrada asignada por el sistema, proporcionando la siguiente información:

- **Nombre del grupo de recursos del servicio de proceso de origen:** el nombre del grupo de recursos de la aplicación de App Service.
- **Nombre de la aplicación de App Service:** el nombre de la aplicación de App Service que se conecta al servicio de destino.
- **Nombre del grupo de recursos del servicio de destino:** el nombre del grupo de recursos de la aplicación del almacenamiento de blobs.
- **Nombre de cuenta de almacenamiento:** el nombre de la cuenta de almacenamiento de blobs.

```azurecli-interactive
az webapp connection create storage-blob --system-identity
```

> [!NOTE]
> Si no tiene un almacenamiento de blobs, puede ejecutar `az webapp connection create storage-blob --new --system-identity` para aprovisionar uno nuevo y conectarse directamente a App Service.

---

## <a name="view-connections"></a>Ver conexiones

Use el comando [az webapp connection](/cli/azure/webapp/connection) de la CLI de Azure para mostrar una conexión a la aplicación de App Service, proporcionando la siguiente información:

- **Nombre del grupo de recursos del servicio de proceso de origen:** el nombre del grupo de recursos de la aplicación de App Service.
- **Nombre de la aplicación de App Service:** el nombre de la aplicación de App Service que se conecta al servicio de destino.

```azurecli-interactive
az webapp connection list -g "<your-app-service-resource-group>" --webapp "<your-app-service-name>"
```

## <a name="next-steps"></a>Pasos siguientes

Complete los tutoriales que se indican a continuación para empezar a crear su propia aplicación con el conector de servicio.

> [!div class="nextstepaction"]
> [Tutorial: Aplicación web y Storage con la CLI de Azure](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [Tutorial: Aplicación web y PostgreSQL con la CLI de Azure](./tutorial-django-webapp-postgres-cli.md)
