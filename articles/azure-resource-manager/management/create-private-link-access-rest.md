---
title: Administración de recursos mediante vínculo privado
description: Restricción del acceso de administración de recursos a vínculo privado
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: eb7e81ef739fdb94bc91c65b079d5b76b22636c9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227313"
---
# <a name="use-rest-api-to-create-private-link-for-managing-azure-resources"></a>Uso de la API REST para crear un vínculo privado para administrar recursos de Azure

En este artículo se explica cómo puede usar [Azure Private Link](../../private-link/index.yml) para restringir el acceso para administrar recursos en las suscripciones.

[!INCLUDE [Create content](../../../includes/resource-manager-create-rmpl.md)]

## <a name="create-resource-management-private-link"></a>Creación de un vínculo privado de administración de recursos

Para crear un vínculo privado de administración de recursos, envíe la solicitud siguiente:

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

En el cuerpo de la solicitud, incluya la ubicación que quiera para el recurso:

```json
{
  "location":"{region}"
}
```

La operación devuelve lo siguiente:

```json
{  
  "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
  "location": "{region}",
  "name": "{rmplName}",
  "properties": {
    "privateEndpointConnections": []
  },
  "resourceGroup": "{rgName}",
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks"
}
```

Anote el identificador que se devuelve para el nuevo vínculo privado de administración de recursos. Lo usará para crear la asociación de vínculo privado.

## <a name="create-private-link-association"></a>Creación de la asociación de vínculo privado

Para crear la asociación de vínculo privado, use lo siguiente:

```http
PUT
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupId}/providers/Microsoft.Authorization/privateLinkAssociations/{GUID}?api-version=2020-05-01 
```

En el cuerpo de la solicitud, incluya:

```json
{
  "properties": {
    "privateLink": "/subscriptions/{subscription-id}/resourceGroups/{rg-name}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}",
    "publicNetworkAccess": "enabled"
  }
}
```

La operación devuelve lo siguiente:

```json
{
  "id": {plaResourceId},
  "name": {plaName},
  "properties": {
    "privateLink": {rmplResourceId},
    "publicNetworkAccess": "Enabled",
    "tenantId": "{tenantId}",
    "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
  },
  "type": "Microsoft.Authorization/privateLinkAssociations"
}
```

## <a name="add-private-endpoint"></a>Agregar un punto de conexión privado

En este artículo se da por supuesto que ya tiene una red virtual. En la subred que se usará para el punto de conexión privado, debe desactivar las directivas de red de punto de conexión privado. Si no ha desactivado las directivas de red de punto de conexión privado, vea [Deshabilitación de directivas de red para puntos de conexión privados](../../private-link/disable-private-endpoint-network-policy.md).

Para crear un punto de conexión privado, use la operación siguiente:

```http
PUT
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/privateEndpoints/{privateEndpointName}?api-version=2020-11-01
```

En el cuerpo de la solicitud, establezca `privateServiceLinkId` en el identificador del vínculo privado de administración de recursos. `groupIds` debe contener `ResourceManagement`. La ubicación del punto de conexión privado debe ser la misma que la ubicación de la subred.

```json
{
  "location": "westus2",
  "properties": {
    "privateLinkServiceConnections": [
      {
        "name": "{connection-name}",
        "properties": {
           "privateLinkServiceId": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{name}",
           "groupIds": [
              "ResourceManagement"
           ]
         }
      }
    ],
    "subnet": {
      "id": "/subscriptions/{subID}/resourceGroups/{rgName}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
    }
  }
}
```

El siguiente paso varía en función de si usa la aprobación automática o manual. Para obtener más información sobre la aprobación, vea [Acceso a un recurso de vínculo privado mediante el flujo de trabajo de aprobación](../../private-link/private-endpoint-overview.md#access-to-a-private-link-resource-using-approval-workflow).

La respuesta incluye el estado de la aprobación.

```json
"privateLinkServiceConnectionState": {
    "actionsRequired": "None",
    "description": "",
    "status": "Approved"
},
```

Si la solicitud se aprueba automáticamente, puede continuar con la sección siguiente. Si la solicitud necesita aprobación manual, espere a que el administrador de red apruebe la conexión de punto de conexión privado.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre los puntos de conexión privados, vea [Azure Private Link](../../private-link/index.yml).