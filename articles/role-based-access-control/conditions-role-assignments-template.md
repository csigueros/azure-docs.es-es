---
title: 'Adición de condiciones de asignación de roles de Azure mediante plantillas de Azure Resource Manager (versión preliminar): ABAC de Azure'
description: Obtenga información sobre cómo agregar condiciones de control de acceso basado en atributos (ABAC) en las asignaciones de roles de Azure mediante plantillas de Azure Resource Manager y el control de acceso basado en roles de Azure (RBAC de Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: how-to
ms.workload: identity
ms.date: 06/29/2021
ms.author: rolyon
ms.openlocfilehash: 6e64afaab3b367ed807f77e5ebc0214904ed763f
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113095205"
---
# <a name="add-azure-role-assignment-conditions-using-azure-resource-manager-templates-preview"></a>Adición de condiciones de asignación de roles de Azure mediante plantillas de Azure Resource Manager (versión preliminar)

> [!IMPORTANT]
> Las condiciones de asignación de roles de Azure y Azure ABAC se encuentran actualmente en versión preliminar.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Una [condición de asignación de roles de Azure](conditions-overview.md) es una comprobación adicional que puede agregar opcionalmente a la asignación de roles para proporcionar un control de acceso más preciso. Por ejemplo, puede agregar una condición que requiera que un objeto tenga una etiqueta específica para leer el objeto. En este artículo, se describe cómo agregar condiciones para las asignaciones de roles mediante plantillas de Azure Resource Manager.

## <a name="prerequisites"></a>Requisitos previos

Para obtener información sobre los requisitos previos para agregar condiciones de asignación de roles, consulte [Requisitos previos de las condiciones](conditions-prerequisites.md).

## <a name="add-a-condition"></a>Adición de una condición

En la siguiente plantilla, se muestra cómo asignar el rol [Lector de datos de Storage Blob](built-in-roles.md#storage-blob-data-reader) con una condición. La condición comprueba si el nombre del contenedor es igual a "blobs-example-container".

Para usar la plantilla, debe especificar las siguientes entradas:

- El identificador de un usuario, de un grupo, de una identidad administrada o de una aplicación al que se asignará el rol.
- Tipo de entidad de seguridad, como `User`, `Group` o `ServicePrincipal`. Para más información, consulte [Nueva entidad de servicio](role-assignments-template.md#new-service-principal).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "Principal ID to assign the role to"
            }
        },
        "principalType": {
            "type": "string",
            "metadata": {
                "description": "Type of principal"
            }
        },
        "roleAssignmentGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "New GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "StorageBlobDataReader": "[concat(subscription().Id, '/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1')]" // ID for Storage Blob Data Reader role, but can be any valid role ID
    },
    "resources": [
        {
            "name": "[parameters('roleAssignmentGuid')]",
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2020-04-01-preview", // API version to call the role assignment PUT.
            "properties": {
                "roleDefinitionId": "[variables('StorageBlobDataReader')]",
                "principalId": "[parameters('principalId')]",
                "principalType": "[parameters('principalType')]",
                "description": "Role assignment condition created with an ARM template",
                "condition": "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers:name] StringEquals 'blobs-example-container'))", // Role assignment condition
                "conditionVersion": "2.0"
            }
        }
    ]
}
```

El ámbito de la asignación de roles se determina a partir del nivel de la implementación. Los comandos [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) y [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create) son ejemplos de cómo iniciar la implementación en el ámbito de un grupo de recursos.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName example-group -TemplateFile rbac-test.json -principalId $principalId -principalType "User"
```

```azurecli
az deployment group create --resource-group example-group --template-file rbac-test.json --parameters principalId=$principalId principalType="User"
```

## <a name="next-steps"></a>Pasos siguientes

- [Condiciones de asignación de roles de Azure de ejemplo (versión preliminar)](../storage/common/storage-auth-abac-examples.md)
- [Solución de problemas de las condiciones de asignación de roles de Azure (versión preliminar)](conditions-troubleshoot.md)
- [Asignación de roles de Azure mediante plantillas de Azure Resource Manager](role-assignments-template.md)
