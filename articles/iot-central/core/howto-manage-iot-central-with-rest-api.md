---
title: Use la API de REST para administrar y supervisar las aplicaciones de IoT Central | Microsoft Docs
description: En este artículo se describe cómo crear y administrar aplicaciones de IoT Central con la API de REST. La API de REST también le permite agregar una identidad administrada asignada por el sistema a la aplicación.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/25/2021
ms.topic: how-to
ms.openlocfilehash: a4df2346346d048bcd4e01e898636c1668720c4c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093741"
---
# <a name="use-the-rest-api-to-create-and-manage-iot-central-applications"></a>Uso de la API de REST para crear y administrar las aplicaciones de IoT Central

Puede usar la [API de REST de plano de control](/rest/api/iotcentral/2021-06-01controlplane/apps) para crear y administrar trabajos en las aplicaciones de IoT Central. También puede usar la API de REST para agregar una identidad administrada a la aplicación.

Para usar esta API, necesita un token de portador para el recurso `management.azure.com`. Para obtener un token de portador, puede usar la CLI de Azure:

```azurecli
az account get-access-token --resource https://management.azure.com
```

## <a name="list-your-applications"></a>Lista de las aplicaciones

Para obtener una lista de las aplicaciones de IoT Central en una suscripción:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.IoTCentral/iotApps?api-version=2021-06-01
```

Para obtener una lista de las aplicaciones de IoT Central en un grupo de recursos:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.IoTCentral/iotApps?api-version=2021-06-01
```

Puede recuperar los detalles de una aplicación individual:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.IoTCentral/iotApps/{applicationName}?api-version=2021-06-01
```

## <a name="create-an-iot-central-application"></a>Creación de una aplicación de IoT Central

Para crear una aplicación de IoT Central con una [identidad administrada](../../active-directory/managed-identities-azure-resources/overview.md) asignada por el sistema:

```http
PUT https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

La carga siguiente muestra la configuración de la nueva aplicación, incluida la identidad administrada:

```json
{
  "location": "eastus",
  "sku": {
    "name": "ST2"
  },
  "properties": {
    "displayName": "Contoso IoT Central App",
    "subdomain": "my-iot-central-app",
    "template": "iotc-pnp-preview@1.0.0"
  },
  "identity": {
    "type": "SystemAssigned"
  }
}
```

## <a name="modify-an-iot-central-application"></a>Modificación de una aplicación de IoT Central

Puede modificar una aplicación de IoT Central existente. En el ejemplo siguiente se muestra cómo cambiar el nombre para mostrar y habilitar la identidad administrada asignada por el sistema:

```http
PATCH https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

Use la carga siguiente para cambiar el nombre para mostrar y habilitar la identidad administrada asignada por el sistema:

```json
{
  "properties": {
    "displayName": "Contoso IoT Central App"
  },
  "identity": {
    "type": "SystemAssigned"
  }
}
```

> [!NOTE]
> Solo puede agregar una identidad administrada a una aplicación de IoT Central que se creó en una región. Todas las aplicaciones nuevas se crean en una región. Para más información, consulte [Actualizaciones](https://azure.microsoft.com/updates/azure-iot-central-new-and-updated-features-august-2021/).

## <a name="delete-an-iot-central-application"></a>Eliminación de una aplicación de IoT Central

Para eliminar una aplicación de IoT Central, use:

```http
DELETE https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a crear y administrar aplicaciones de Azure IoT Central desde la API de REST, le sugerimos el paso siguiente:

> [!div class="nextstepaction"]
> [Uso de la API REST de IoT Central para administrar usuarios y roles](howto-manage-users-roles-with-rest-api.md)