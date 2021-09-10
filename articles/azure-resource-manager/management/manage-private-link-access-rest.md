---
title: Administración de vínculos privados de administración de recursos
description: Uso de la API REST para administrar vínculos privados de administración de recursos existentes
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 376fa600cce4479a0ed2c04e9d3d7b3fbeb9aeaa
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227315"
---
# <a name="manage-resource-management-private-links-with-rest-api"></a>Administrar vínculos privados de administración de recursos con la API REST

En este artículo se explica cómo trabajar con vínculos privados de administración de recursos existentes. Muestra las operaciones de la API REST para obtener y eliminar los recursos existentes.

Si necesita crear un vínculo privado de administración de recursos, consulte [Uso del portal para crear un vínculo privado para administrar recursos de Azure](create-private-link-access-portal.md) o [Uso de la API REST para crear un vínculo privado para administrar recursos de Azure](create-private-link-access-rest.md).

## <a name="resource-management-private-links"></a>Vínculos privados de administración de recursos

Para obtener un vínculo privado de administración de recursos **específico**, envíe la solicitud siguiente:

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01 
```

La operación devuelve lo siguiente:

```json
{
  "properties": {
    "privateEndpointConnections": []
  },
  "id": {rmplResourceId},
  "name": {rmplName},
  "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
  "location": {region}
}
```

Para **obtener todos** los vínculos privados de administración de recursos de una suscripción, use:

```http
GET
https://management.azure.com/subscriptions/{subscriptionID}/providers/Microsoft.Authorization/resourceManagementPrivateLinks?api-version=2020-05-01
```

La operación devuelve lo siguiente:

```json
[
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  },
  {
    "properties": {
      "privateEndpointConnections": []
    },
    "id": {rmplResourceId},
    "name": {rmplName},
    "type": "Microsoft.Authorization/resourceManagementPrivateLinks",
    "location": {region}
  }
]
```

Para eliminar un vínculo privado de administración de recursos **específico**, use:

```http
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/resourceManagementPrivateLinks/{rmplName}?api-version=2020-05-01
```

La operación devuelve lo siguiente: `Status 200 OK`.

## <a name="private-link-association"></a>Asociación de vínculo privado

Para obtener una asociación de vínculo privado **específica** para un grupo de administración, use:

```http
GET
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations?api-version=2020-05-01 
```

La operación devuelve lo siguiente:

```json
{
  "value": [
    {
      "properties": {
        "privateLink": {rmplResourceID},
        "tenantId": {tenantId},
        "scope": "/providers/Microsoft.Management/managementGroups/{managementGroupId}"
      },
      "id": {plaResourceId},
      "type": "Microsoft.Authorization/privateLinkAssociations",
      "name": {plaName}
    }
  ]
}
```

Para **eliminar** una asociación de vínculo privado, use:

```http
DELETE 
https://management.azure.com/providers/Microsoft.Management/managementGroups/{managementGroupID}/providers/Microsoft.Authorization/privateLinkAssociations/{plaID}?api-version=2020-05-01
```

La operación devuelve lo siguiente: `Status 200 OK`.

## <a name="private-endpoints"></a>Puntos de conexión privados

Para **obtener todos** los puntos de conexión privados de una suscripción, use:

```http
GET 
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Network/privateEndpoints?api-version=2020-04-01
```

La operación devuelve lo siguiente:

```json
{
  "value": [
    {
      "name": {privateEndpointName},
      "id": {privateEndpointResourceId},
      "etag": {etag},
      "type": "Microsoft.Network/privateEndpoints",
      "location": {region},
      "properties": {
        "provisioningState": "Updating",
        "resourceGuid": {GUID},
        "privateLinkServiceConnections": [
          {
            "name": {connectionName},
            "id": {connectionResourceId},
            "etag": {etag},
            "properties": {
              "provisioningState": "Succeeded",
              "privateLinkServiceId": {rmplResourceId},
              "groupIds": [
                "ResourceManagement"
              ],
              "privateLinkServiceConnectionState": {
                "status": "Approved",
                "description": "",
                "actionsRequired": "None"
              }
            },
            "type": "Microsoft.Network/privateEndpoints/privateLinkServiceConnections"
          }
        ],
        "manualPrivateLinkServiceConnections": [],
        "subnet": {
          "id": {subnetResourceId}
        },
        "networkInterfaces": [
          {
            "id": {networkInterfaceResourceId}
          }
        ],
        "customDnsConfigs": [
          {
            "fqdn": "management.azure.com",
            "ipAddresses": [
              "10.0.0.4"
            ]
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre los vínculos privados, vea [Azure Private Link](../../private-link/index.yml).
* Para crear un vínculo privado de administración de recursos, consulte [Uso del portal para crear un vínculo privado para administrar recursos de Azure](create-private-link-access-portal.md) o [Uso de la API REST para crear un vínculo privado para administrar recursos de Azure](create-private-link-access-rest.md).