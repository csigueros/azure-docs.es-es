---
title: Permisos de Azure RBAC para Azure Private Link
description: Empiece a conocer los permisos de Azure RBAC necesarios para implementar un punto de conexión privado y un servicio de vínculo privado.
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 5/25/2021
ms.custom: template-concept
ms.openlocfilehash: 50b648c5419b995f9c808288895af49b12f29b86
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178319"
---
# <a name="azure-rbac-permissions-for-azure-private-link"></a>Permisos de Azure RBAC para Azure Private Link

La administración del acceso de los recursos en la nube es una función importantísima para cualquier organización. El control de acceso basado en rol de Azure (Azure RBAC) administra el acceso a los recursos de Azure y las operaciones de estos recursos.

Para implementar un punto de conexión privado o un servicio de vínculo privado, un usuario debe tener asignado un rol integrado como: 

* [Propietario](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#owner)
* [Colaborador](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#contributor)
* [Colaborador de red](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json#network-contributor)

Puede proporcionar acceso más pormenorizado mediante la creación de un [rol personalizado](../role-based-access-control/custom-roles.md?toc=%2fazure%2fprivate-link%2ftoc.json) con los permisos descritos en las secciones siguientes.

> [!IMPORTANT]
> En este artículo se enumeran los permisos específicos para crear un punto de conexión privado o un servicio de vínculo privado. Asegúrese de agregar los permisos específicos relacionados con el servicio al que desea conceder acceso a través de un vínculo privado, como Rol de colaborador de Microsoft.SQL para Azure SQL. Para más información sobre los roles integrados, consulte [Control de acceso basado en rol](../role-based-access-control/built-in-roles.md).

Microsoft.Network y el proveedor de recursos específico que va a implementar, por ejemplo, Microsoft.Sql, deben estar registrados en el nivel de suscripción:

![imagen](https://user-images.githubusercontent.com/20302679/129105527-b946eee9-038a-46ef-b446-be371eb23ca9.png)

## <a name="private-endpoint"></a>Punto de conexión privado

En esta sección se enumeran los permisos granulares necesarios para implementar un punto de conexión privado.

| Acción                                                              | Descripción                                                                      |
| ---------                                                           | -------------                                                                 |
| Microsoft.Resources/deployments/*                                   | Creación y administración de una implementación                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | Leer los recursos del grupo de recursos.                                     |
| Microsoft.Network/virtualNetworks/read                              | Leer la definición de red virtual.                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | Leer una definición de subred de red virtual.                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | Crea una subred de red virtual o actualiza una que ya existe|
| Microsoft.Network/virtualNetworks/subnets/join/action               | Se une a una red virtual                                                       |
| Microsoft.Network/privateEndpoints/read                             | Leer un recurso de punto de conexión privado.                                             |
| Microsoft.Network/privateEndpoints/write                            | Crear un punto de conexión privado o actualizar uno ya existente.       |
| Microsoft.Network/locations/availablePrivateEndpointTypes/read      | Leer los recursos de punto de conexión privado disponibles.                                     |

Este es el formato JSON de los permisos anteriores. Introduzca sus propios valores de roleName, description y assignableScopes:

```JSON
{
 "properties": {
   "roleName": "Role Name",
   "description": "Description",
   "assignableScopes": [
     "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
   ],
   "permissions": [
     {
       "actions": [
         "Microsoft.Resources/deployments/*",
         "Microsoft.Resources/subscriptions/resourceGroups/read",
         "Microsoft.Network/virtualNetworks/read",
         "Microsoft.Network/virtualNetworks/subnets/read",
         "Microsoft.Network/virtualNetworks/subnets/write",
         "Microsoft.Network/virtualNetworks/subnets/join/action",
         "Microsoft.Network/privateEndpoints/read",
         "Microsoft.Network/privateEndpoints/write",
         "Microsoft.Network/locations/availablePrivateEndpointTypes/read"
       ],
       "notActions": [],
       "dataActions": [],
       "notDataActions": []
     }
   ]
 }
}
```

## <a name="private-link-service"></a>Servicio Private Link

En esta sección se enumeran los permisos granulares necesarios para implementar un servicio de punto de conexión privado.

| Acción | Descripción   |
| --------- | ------------- |
| Microsoft.Resources/deployments/*                                   | Creación y administración de una implementación                                                |
| Microsoft.Resources/subscriptions/resourcegroups/resources/read     | Leer los recursos del grupo de recursos.                                     |
| Microsoft.Network/virtualNetworks/read                              | Leer la definición de red virtual.                                            |
| Microsoft.Network/virtualNetworks/subnets/read                      | Leer una definición de subred de red virtual.                                      |
| Microsoft.Network/virtualNetworks/subnets/write                     | Crea una subred de red virtual o actualiza una que ya existe|
| Microsoft.Network/privateLinkServices/read | Leer un recurso de servicio de vínculo privado.|
| Microsoft.Network/privateLinkServices/write | Crear un servicio de vínculo privado o actualizar uno ya existente.|
| Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Leer una definición de conexión de punto de conexión privado. |
| Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Crear una conexión de punto de conexión privado o actualizar una ya existente.|
| Microsoft.Network/networkSecurityGroups/join/action | Se une a un grupo de seguridad de red |
| Microsoft.Network/loadBalancers/read | Leer una definición de equilibrador de carga. |
| Microsoft.Network/loadBalancers/write | Crea un equilibrador de carga o actualiza uno que ya existe |

```JSON
{
  "properties": {
    "roleName": "Role Name",
    "description": "Description",
    "assignableScopes": [
      "/subscriptions/SubscriptionID/resourceGroups/ResourceGroupName"
    ],
    "permissions": [
      {
        "actions": [
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Network/virtualNetworks/read",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/write",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/privateLinkServices/read",
          "Microsoft.Network/privateLinkServices/write",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/read",
          "Microsoft.Network/privateLinkServices/privateEndpointConnections/write",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Network/loadBalancers/read",
          "Microsoft.Network/loadBalancers/write"
        ],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los servicios de punto de conexión privado y vínculo privado en Azure Private Link, consulte:

- [¿Qué es un punto de conexión privado de Azure?](private-endpoint-overview.md)
- [¿Qué es el servicio Azure Private Link?](private-link-service-overview.md)
