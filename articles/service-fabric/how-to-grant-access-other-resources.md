---
title: Concesión de acceso de una aplicación a otros recursos de Azure
description: En este artículo se explica el proceso para que una aplicación de Service Fabric habilitada para identidades administradas tenga acceso a otros recursos de Azure que admiten la autenticación basada en Azure Active Directory.
ms.topic: article
ms.date: 12/09/2019
ms.custom: subject-rbac-steps
ms.openlocfilehash: 7f49a3f97862c3a141ea9376d0ffc9bf510d3e6f
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110782968"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources"></a>Concesión de acceso a recursos de Azure para la identidad administrada de una aplicación de Service Fabric

Para que la aplicación pueda usar su identidad administrada a fin de acceder a otros recursos, es necesario conceder permisos a dicha identidad en el recurso de Azure protegido al que se va a acceder. La concesión de permisos suele ser una acción de administración en el "plano de control" del servicio de Azure propietario del recurso protegido enrutado a través de Azure Resource Manager, que aplicará cualquier comprobación aplicable de acceso basado en roles.

La secuencia exacta de pasos dependerá del tipo de recurso de Azure al que se acceda, así como del lenguaje o del cliente que se use para conceder permisos. En el resto del artículo se da por hecho que se ha asignado a la aplicación una identidad asignada por el usuario y se incluyen varios ejemplos típicos para mayor comodidad, pero esto no constituye una referencia exhaustiva sobre el tema. Consulte la documentación de los servicios de Azure correspondientes para obtener instrucciones actualizadas sobre la concesión de permisos.  

## <a name="granting-access-to-azure-storage"></a>Concesión de acceso a Azure Storage
Puede usar la identidad administrada de la aplicación de Service Fabric (en este caso, asignada por el usuario) para recuperar los datos de un blob de Azure Storage. Conceda a la identidad los permisos necesarios para la cuenta de almacenamiento mediante la asignación del rol de [Lector de datos de Storage Blob](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) a la identidad administrada de la aplicación en el ámbito *resource-group*.

Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md).


## <a name="granting-access-to-azure-key-vault"></a>Concesión de acceso a Azure Key Vault
De forma similar al proceso para acceder al almacenamiento, puede aprovechar la identidad administrada de una aplicación de Service Fabric para acceder a una instancia de Azure Key Vault. Los pasos para conceder acceso en Azure Portal son semejantes a los indicados anteriormente y no se repetirán aquí. Consulte la imagen siguiente para ver las diferencias.

![Directiva de acceso de Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

En el ejemplo siguiente se muestra cómo conceder acceso a un almacén mediante la implementación de una plantilla; agregue el los fragmentos de código siguientes como otra entrada debajo del elemento `resources` de la plantilla. En el ejemplo se muestra cómo conceder acceso tanto a los tipos de identidad asignados por el usuario como a los asignados por el sistema, respectivamente (elija el que corresponda).

```json
    # under 'variables':
  "variables": {
        "userAssignedIdentityResourceId" : "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]",
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```
Y para las identidades administradas asignadas por el usuario:
```json
    # under 'variables':
  "variables": {
        "sfAppSystemAssignedIdentityResourceId": "[concat(resourceId('Microsoft.ServiceFabric/clusters/applications/', parameters('clusterName'), parameters('applicationName')), '/providers/Microsoft.ManagedIdentity/Identities/default')]"
    }
    # under 'resources':
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
            {
                    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
                    "tenantId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('sfAppSystemAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('sfAppSystemAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "secrets": [
                            "get",
                            "list"
                        ],
                        "certificates": 
                        [
                            "get", 
                            "list"
                        ]
                    }
            },
        ]
        }
    }
```

Para obtener más información, consulte [Almacenes: actualización de la directiva de acceso](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Pasos siguientes
* [Implementación de una aplicación de Azure Service Fabric con una identidad administrada asignada por el sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Implementación de una aplicación de Azure Service Fabric con una identidad administrada asignada por el usuario](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
