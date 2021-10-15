---
title: 'Creación o actualización de roles personalizados de Azure con la API REST: RBAC de Azure'
description: Aprenda a enumerar, crear, actualizar o eliminar roles personalizados de Azure mediante la API REST y el control de acceso basado en roles de Azure (RBAC de Azure).
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: how-to
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5c7e816f15400a28e8b10f4aea7a2315c89048be
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352915"
---
# <a name="create-or-update-azure-custom-roles-using-the-rest-api"></a>Creación o actualización de roles personalizados para los recursos de Azure con la API REST

> [!IMPORTANT]
> La adición de un grupo de administración a `AssignableScopes` está actualmente en versión preliminar.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Si los [roles integrados de Azure](built-in-roles.md) no cumplen las necesidades específicas de su organización, puede crear los suyos propios. En este artículo se describe cómo mostrar, crear, actualizar o eliminar roles personalizados con la API REST.

## <a name="list-custom-roles"></a>Lista de roles personalizados

Para enumerar todos los roles personalizados de un directorio, use la API REST [Definiciones de roles: lista](/rest/api/authorization/roledefinitions/list).

1. Empiece con la solicitud siguiente:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. Reemplace *{filter}* por el tipo de rol.

    > [!div class="mx-tableFixed"]
    > | Filter | Descripción |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtro basado en el tipo CustomRole |

## <a name="list-custom-roles-at-a-scope"></a>Enumeración de roles personalizados en un ámbito

Para enumerar los roles personalizados en un ámbito, use la API REST [Definiciones de roles: lista](/rest/api/authorization/roledefinitions/list).

1. Empiece con la solicitud siguiente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. En el identificador URI, reemplace *{scope}* por el ámbito cuya lista de roles quiere obtener.

    > [!div class="mx-tableFixed"]
    > | Ámbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resource |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de administración |

1. Reemplace *{filter}* por el tipo de rol.

    > [!div class="mx-tableFixed"]
    > | Filter | Descripción |
    > | --- | --- |
    > | `$filter=type+eq+'CustomRole'` | Filtro basado en el tipo CustomRole |

## <a name="list-a-custom-role-definition-by-name"></a>Enumeración de una definición de rol personalizado por nombre

Para obtener información sobre el rol personalizado por su nombre para mostrar, use la API REST [Definiciones de roles: lista: obtener](/rest/api/authorization/roledefinitions/get).

1. Empiece con la solicitud siguiente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?api-version=2015-07-01&$filter={filter}
    ```

1. En el identificador URI, reemplace *{scope}* por el ámbito cuya lista de roles quiere obtener.

    > [!div class="mx-tableFixed"]
    > | Ámbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resource |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de administración |

1. Reemplace *{filter}* con el nombre para mostrar para el rol.

    > [!div class="mx-tableFixed"]
    > | Filter | Descripción |
    > | --- | --- |
    > | `$filter=roleName+eq+'{roleDisplayName}'` | Use la forma con codificación URL del nombre para mostrar exacto del rol. Por ejemplo: `$filter=roleName+eq+'Virtual%20Machine%20Contributor'` |

## <a name="list-a-custom-role-definition-by-id"></a>Enumeración de una definición de rol personalizado por identificador

Para obtener información sobre un rol personalizado por su identificador único, use la API REST [Definiciones de roles: obtener](/rest/api/authorization/roledefinitions/get).

1. Use la API de REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) para obtener el identificador GUID para el rol.

1. Empiece con la solicitud siguiente:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. En el identificador URI, reemplace *{scope}* por el ámbito cuya lista de roles quiere obtener.

    > [!div class="mx-tableFixed"]
    > | Ámbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}/providers/Microsoft.Web/sites/{site1}` | Resource |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de administración |

1. Reemplace *{roleDefinitionId}* por el identificador GUID de la definición de roles.

## <a name="create-a-custom-role"></a>Crear un rol personalizado

Para crear un rol personalizado, use la API de REST [Role Definitions - Create Or Update](/rest/api/authorization/roledefinitions/createorupdate). Para llamar a esta API, debe haber iniciado sesión con un usuario que tenga asignado un rol que, a su vez, tenga el permiso `Microsoft.Authorization/roleDefinitions/write` en todos los `assignableScopes`. Entre los roles integrados, solamente [Propietario](built-in-roles.md#owner) y [Administrador de acceso de usuario](built-in-roles.md#user-access-administrator) incluyen este permiso.

1. Revise la lista de [operaciones de proveedores de recursos](resource-provider-operations.md) que están disponibles para crear los permisos para su rol personalizado.

1. Use una herramienta de GUID para generar un identificador único que se use para el identificador del rol personalizado. El identificador tiene el formato: `00000000-0000-0000-0000-000000000000`

1. Empiece con la solicitud y el cuerpo siguientes:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Dentro del URI, reemplace *{scope}* por el primer elemento `assignableScopes` del rol personalizado.

    > [!div class="mx-tableFixed"]
    > | Ámbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de administración |

1. Reemplace *{roleDefinitionId}* por el identificador GUID del rol personalizado.

1. En el cuerpo de la solicitud, reemplace *{roleDefinitionId}* por el identificador GUID.

1. Si `assignableScopes` es una suscripción o un grupo de recursos, reemplace las instancias *{subscriptionId}* o *{resourceGroup}* por sus identificadores.

1. Si `assignableScopes` es un grupo de administración, reemplace la instancia *{groupId}* por el identificador del grupo de administración. La adición de un grupo de administración a `assignableScopes` está actualmente en versión preliminar.

1. En la propiedad `actions`, agregue las acciones que permite realizar el rol.

1. En la propiedad `notActions`, agregue las acciones excluidas de las `actions` permitidas.

1. En las propiedades `roleName` y `description`, especifique un nombre de rol exclusivo y una descripción. Para más información sobre las propiedades, consulte [Roles personalizados de Azure](custom-roles.md).

    A continuación se muestra un ejemplo de cuerpo de la solicitud:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="update-a-custom-role"></a>Actualización de un rol personalizado

Para actualizar un rol personalizado, use la API de REST [Role Definitions - Create Or Update](/rest/api/authorization/roledefinitions/createorupdate). Para llamar a esta API, debe haber iniciado sesión con un usuario que tenga asignado un rol que, a su vez, tenga el permiso `Microsoft.Authorization/roleDefinitions/write` en todos los `assignableScopes`. Entre los roles integrados, solamente [Propietario](built-in-roles.md#owner) y [Administrador de acceso de usuario](built-in-roles.md#user-access-administrator) incluyen este permiso.

1. Para obtener información sobre el rol personalizado, use las API de REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) o [Role Definitions - Get](/rest/api/authorization/roledefinitions/get). Para más información, consulte la sección [Lista de roles personalizados](#list-custom-roles) anterior.

1. Empiece con la solicitud siguiente:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Dentro del URI, reemplace *{scope}* por el primer elemento `assignableScopes` del rol personalizado.

    > [!div class="mx-tableFixed"]
    > | Ámbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de administración |

1. Reemplace *{roleDefinitionId}* por el identificador GUID del rol personalizado.

1. Según la información sobre el rol personalizado, cree un cuerpo de la solicitud con el formato siguiente:

    ```json
    {
      "name": "{roleDefinitionId}",
      "properties": {
        "roleName": "",
        "description": "",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
    
            ],
            "notActions": [
    
            ]
          }
        ],
        "assignableScopes": [
          "/subscriptions/{subscriptionId1}",
          "/subscriptions/{subscriptionId2}",
          "/subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}",
          "/subscriptions/{subscriptionId2}/resourceGroups/{resourceGroup2}",
          "/providers/Microsoft.Management/managementGroups/{groupId1}"
        ]
      }
    }
    ```

1. Actualice el cuerpo de la solicitud con los cambios que desee hacer en el rol personalizado.

    A continuación se muestra un ejemplo de cuerpo de la solicitud con la nueva acción de configuración de diagnóstico agregada:

    ```json
    {
      "name": "88888888-8888-8888-8888-888888888888",
      "properties": {
        "roleName": "Virtual Machine Operator",
        "description": "Can monitor and restart virtual machines.",
        "type": "CustomRole",
        "permissions": [
          {
            "actions": [
              "Microsoft.Storage/*/read",
              "Microsoft.Network/*/read",
              "Microsoft.Compute/*/read",
              "Microsoft.Compute/virtualMachines/start/action",
              "Microsoft.Compute/virtualMachines/restart/action",
              "Microsoft.Authorization/*/read",
              "Microsoft.ResourceHealth/availabilityStatuses/read",
              "Microsoft.Resources/subscriptions/resourceGroups/read",
              "Microsoft.Insights/alertRules/*",
              "Microsoft.Insights/diagnosticSettings/*",
              "Microsoft.Support/*"
            ],
            "notActions": []
          }
        ],
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000",
          "/providers/Microsoft.Management/managementGroups/marketing-group"
        ]
      }
    }
    ```

## <a name="delete-a-custom-role"></a>Eliminación de un rol personalizado

Para eliminar un rol personalizado, use la API de REST [Role Definitions - Delete](/rest/api/authorization/roledefinitions/delete). Para llamar a esta API, debe haber iniciado sesión con un usuario que tenga asignado un rol que, a su vez, tenga el permiso `Microsoft.Authorization/roleDefinitions/delete` en todos los `assignableScopes`. Entre los roles integrados, solamente [Propietario](built-in-roles.md#owner) y [Administrador de acceso de usuario](built-in-roles.md#user-access-administrator) incluyen este permiso.

1. Para obtener el identificador GUID del rol personalizado, use las API de REST [Role Definitions - List](/rest/api/authorization/roledefinitions/list) o [Role Definitions - Get](/rest/api/authorization/roledefinitions/get). Para más información, consulte la sección [Lista de roles personalizados](#list-custom-roles) anterior.

1. Empiece con la solicitud siguiente:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. En el identificador URI, reemplace *{scope}* por el ámbito que desea eliminar para el rol personalizado.

    > [!div class="mx-tableFixed"]
    > | Ámbito | Tipo |
    > | --- | --- |
    > | `subscriptions/{subscriptionId1}` | Subscription |
    > | `subscriptions/{subscriptionId1}/resourceGroups/{resourceGroup1}` | Resource group |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Grupo de administración |

1. Reemplace *{roleDefinitionId}* por el identificador GUID del rol personalizado.

## <a name="next-steps"></a>Pasos siguientes

- [Roles personalizados en los recursos de Azure](custom-roles.md)
- [Asignación de roles de Azure mediante la API REST](role-assignments-rest.md)
- [Azure REST API Reference](/rest/api/azure/) (Referencia de API de REST en Azure)
