---
title: Descripción del ámbito de RBAC de Azure
description: Obtenga información sobre el ámbito del control de acceso basado en rol de Azure (RBAC de Azure) y cómo determinar el ámbito de un recurso.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 08/09/2021
ms.author: rolyon
ms.openlocfilehash: 3fc3d614ef26235325e0b9a9e8fee68d2bf919a5
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862583"
---
# <a name="understand-scope-for-azure-rbac"></a>Descripción del ámbito de RBAC de Azure

*Ámbito* es el conjunto de recursos al que se aplica el acceso. Al asignar un rol, es importante comprender el ámbito para que pueda conceder a una entidad de seguridad solo el acceso que realmente necesita. Al limitar el ámbito, se limitan los recursos en peligro si la entidad de seguridad llegara a verse comprometida.

## <a name="scope-levels"></a>Niveles de ámbito

En Azure, puede especificar un ámbito en cuatro niveles: [grupo de administración](../governance/management-groups/overview.md), suscripción, [grupo de recursos](../azure-resource-manager/management/overview.md#resource-groups) y recurso. Los ámbitos se estructuran en una relación de elementos primarios y secundarios. Cada nivel de jerarquía hace que el ámbito sea más específico. Puede asignar roles en cualquiera de estos niveles de ámbito. El nivel que seleccione determina el grado de amplitud con que se aplica el rol. Los niveles inferiores heredan los permisos de rol de los niveles superiores. 

![Ámbito de una asignación de roles](./media/scope-overview/rbac-scope-no-label.png)

Los grupos de administración son un nivel de ámbito superior a las suscripciones, pero los grupos de administración admiten jerarquías más complejas. En el siguiente diagrama se muestra un ejemplo de una jerarquía de grupos de administración y suscripciones que puede definir. Para más información sobre los grupos de administración, consulte [¿Qué son los grupos de administración de Azure?](../governance/management-groups/overview.md).

![Jerarquía de grupo de administración y suscripción](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>Formato de ámbito

Si asigna roles mediante la línea de comandos, deberá especificar el ámbito. Para las herramientas de la línea de comandos, el ámbito es una cadena potencialmente larga que identifica el ámbito exacto de la asignación. En Azure Portal, este ámbito suele aparecer como el *identificador del recurso*.

El ámbito se compone de una serie de identificadores separados por el carácter de barra diagonal (/). Esta cadena se puede considerar como la expresión de la siguiente jerarquía, en la que el texto sin los marcadores de posición (`{}`) son identificadores fijos:

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` es el identificador de la suscripción que se va a utilizar (un identificador único).
- `{resourcesGroupName}` es el nombre del grupo de recursos que actúa como contenedor.
- `{providerName}` es el nombre del [proveedor de recursos](../azure-resource-manager/management/azure-services-resource-providers.md) que administra el recurso, y `{resourceType}` y `{resourceSubType*}` identifican niveles adicionales dentro de ese proveedor de recursos.
- `{resourceName}` es la última parte de la cadena que identifica un recurso específico.

Los grupos de administración son un nivel superior a las suscripciones y tienen el ámbito más amplio (menos específico). Las asignaciones de roles en este nivel se aplican a las suscripciones dentro del grupo de administración. El ámbito de un grupo de administración tiene el siguiente formato:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Ejemplos de ámbito

> [!div class="mx-tableFixed"]
> | Ámbito | Ejemplo |
> | --- | --- |
> | Grupo de administración | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Subscription | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Resource group | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Resource | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Determinación del ámbito de un recurso

Es bastante sencillo determinar el ámbito de un grupo de administración, una suscripción o un grupo de recursos. Solo tiene que conocer el nombre y el identificador de la suscripción. Sin embargo, la determinación del ámbito de un recurso requiere un poco más de trabajo. A continuación se indican un par de formas para poder determinar el ámbito de un recurso.

- En Azure Portal, abra el recurso y examine las propiedades. El recurso debe mostrar el **identificador de recurso** donde puede determinar el ámbito. Por ejemplo, estos son los identificadores de recurso para una cuenta de almacenamiento.

    ![Identificadores de recurso para una cuenta de almacenamiento en Azure Portal](./media/scope-overview/scope-resource-id.png)

- Otra manera es usar el Azure Portal para asignar temporalmente un rol en el ámbito del recurso y, a continuación, usar [Azure PowerShell](role-assignments-list-powershell.md) o la [CLI de Azure](role-assignments-list-cli.md) para mostrar la asignación de roles. En la salida, el ámbito se mostrará como una propiedad.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="scope-and-arm-templates"></a>Ámbito y plantillas de ARM

Una asignación de roles es un tipo especial de Azure Resource Manager que se denomina *recurso de extensión*. Un recurso de extensión es un recurso que se agrega a las capacidades de otro recurso. Siempre existe como extensión (como un elemento secundario) de otro recurso. Por ejemplo, una asignación de roles en el ámbito de la suscripción es un recurso de extensión de la suscripción. El nombre de una asignación de roles siempre es el nombre del recurso que se va a extender más `/Microsoft.Authorization/roleAssignments/{roleAssignmentId}`. Al asignar roles mediante una plantilla de Azure Resource Manager (plantilla de ARM), no suele ser necesario proporcionar el ámbito. El motivo es que el campo de ámbito siempre termina siendo el Id. del recurso que se va a extender. El ámbito se puede determinar a partir del Id. de la propia asignación de roles. En la tabla siguiente se muestran ejemplos de un Id. de asignación de roles y el ámbito correspondiente:

> [!div class="mx-tableFixed"]
> | Id. de la asignación de roles | Ámbito |
> | --- | --- |
> | `/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}` | `/subscriptions/{subscriptionId}` |
> | `/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}` | `/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg` |

Para obtener más información sobre el ámbito y las plantillas de ARM, consulte [Asignación de roles de Azure mediante plantillas de Azure Resource Manager](role-assignments-template.md). Para obtener una lista completa de los tipos de recursos de extensión, consulte los [Tipos de recursos que amplían las capacidades de otros recursos](../azure-resource-manager/management/extension-resource-types.md).

## <a name="next-steps"></a>Pasos siguientes

- [Pasos para asignar un rol de Azure](role-assignments-steps.md)
- [Proveedores de recursos para servicios de Azure](../azure-resource-manager/management/azure-services-resource-providers.md)
- [¿Qué son los grupos de administración de Azure?](../governance/management-groups/overview.md)
