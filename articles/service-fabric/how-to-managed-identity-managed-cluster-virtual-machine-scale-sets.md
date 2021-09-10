---
title: Incorporación de una identidad administrada a un tipo de nodo de clúster administrado de Service Fabric
description: En este artículo se muestra cómo agregar una identidad administrada a un tipo de nodo de clúster administrado de Service Fabric
ms.topic: how-to
ms.date: 5/10/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6cf2d65fe90656fe3025e438a57ea60fe17abd0d
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112453106"
---
# <a name="add-a-managed-identity-to-a-service-fabric-managed-cluster-node-type"></a>Incorporación de una identidad administrada a un tipo de nodo de clúster administrado de Service Fabric

Cada tipo de nodo de un clúster administrado de Service Fabric se encuentra respaldado por un conjunto de escalado de máquinas virtuales. Para permitir el uso de identidades administradas con un tipo de nodo de clúster administrado, se ha agregado una propiedad `vmManagedIdentity` a las definiciones de tipo de nodo que contienen una lista de identidades que se pueden usar, `userAssignedIdentities`. La funcionalidad refleja cómo se pueden usar las identidades administradas en los clústeres no administrados, como el uso de una identidad administrada con la [extensión del conjunto de escalado de máquinas virtuales de Azure Key Vault](../virtual-machines/extensions/key-vault-windows.md).

Para ver un ejemplo de una implementación de clústeres administrados de Service Fabric que usa una identidad administrada en un tipo de nodo, consulte [estas plantillas](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-MI). El ejemplo tiene dos plantillas:

1. **Asignación de identidad administrada y rol**: plantilla para crear la asignación de identidad administrada y rol para permitir que Service Fabric RP asigne la identidad al conjunto de escalado de máquinas virtuales del clúster administrado. Esto solo debe implementarse una vez antes de usar la identidad administrada en el recurso de tipo de nodo.

2. **Clúster administrado y tipo de nodo**: plantilla para los recursos de tipo de nodo y clúster administrado de Service Fabric que usan la identidad administrada creada previamente.

> [!NOTE]
> Actualmente, solo se admiten identidades asignadas por el usuario para esta característica.

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar:

* Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.
* Si tiene previsto usar PowerShell, [instale](/cli/azure/install-azure-cli) la CLI de Azure para ejecutar los comandos de referencia de la CLI.

## <a name="1-create-identity-and-role-assignment"></a>1. Creación de la asignación de identidad y roles

### <a name="create-a-user-assigned-managed-identity"></a>Crear una identidad administrada asignada por el usuario

Una identidad administrada asignada por el usuario se puede definir en la sección de recursos de una plantilla de Azure Resource Manager (ARM) para su creación tras la implementación:

```JSON
{ 
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities", 
    "name": "[parameters('userAssignedIdentityName')]", 
    "apiVersion": "2018-11-30", 
    "location": "[resourceGroup().location]"  
},
```

O crearse a través de PowerShell:

```powershell
 New-AzResourceGroup -Name <managedIdentityRGName> -Location <location>
New-AzUserAssignedIdentity -ResourceGroupName <managedIdentityRGName> -Name <userAssignedIdentityName>
```

### <a name="add-a-role-assignment-with-service-fabric-resource-provider"></a>Adición de una asignación de roles con el proveedor de recursos de Service Fabric

Agregue una asignación de roles a la identidad administrada con la aplicación de proveedor de recursos de Service Fabric. Esta asignación permite al proveedor de recursos de Service Fabric asignar la identidad, creada en el paso anterior, al conjunto de escalado de máquinas virtuales del clúster administrado. Esto solo se hace una vez.

Obtenga la entidad de servicio de la aplicación del proveedor de recursos de Service Fabric:

```powershell
Login-AzAccount
Select-AzSubscription -SubscriptionId <SubId>
Get-AzADServicePrincipal -DisplayName "Azure Service Fabric Resource Provider"
```

> [!NOTE]
> Asegúrese de que se encuentra en la suscripción correcta; el id. de la entidad de seguridad cambia si la suscripción está en otro inquilino.

```powershell
ServicePrincipalNames : {74cb6831-0dbb-4be1-8206-fd4df301cdc2}
ApplicationId         : 74cb6831-0dbb-4be1-8206-fd4df301cdc2
ObjectType            : ServicePrincipal
DisplayName           : Azure Service Fabric Resource Provider
Id                    : 00000000-0000-0000-0000-000000000000
```

Use el **id.** de la salida anterior como **principalId**, y el siguiente id. de la definición de roles, como **roleDefinitionId** donde corresponda en la plantilla o en el comando de PowerShell:

|El nombre de la definición de roles|Id. de definición de roles|
|----|-------------------------------------|
|Operador de identidad administrada|f1a07417-d97a-45cb-824c-7a7467783830|


Esta asignación de roles se puede definir en la plantilla de la sección de recursos usando el id. de la entidad de seguridad y el id. de la definición de roles:

```JSON
{
    "type": "Microsoft.Authorization/roleAssignments", 
    "apiVersion": "2020-04-01-preview",
    "name": "[parameters('vmIdentityRoleNameGuid')]",
    "scope": "[concat('Microsoft.ManagedIdentity/userAssignedIdentities', '/', parameters('userAssignedIdentityName'))]",
    "dependsOn": [ 
        "[concat('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
    ], 
    "properties": {
        "roleDefinitionId": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'f1a07417-d97a-45cb-824c-7a7467783830')]",
        "principalId": "00000000-0000-0000-0000-000000000000" 
    } 
}, 
```
> [!NOTE]
> vmIdentityRoleNameGuid debe ser un GUID válido. Si implementa de nuevo la misma plantilla, incluida esta asignación de roles, asegúrese de que el GUID sea el mismo que el usado originalmente o quite este recurso, ya que solo debe crearse una vez.

O bien se puede crear por medio de PowerShell con el id. de la entidad de seguridad y el nombre de la definición de roles:

```powershell
New-AzRoleAssignment -PrincipalId 00000000-0000-0000-0000-000000000000 -RoleDefinitionName "Managed Identity Operator" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userAssignedIdentityName>"
```

### <a name="deploy-managed-identity-and-role-assignment"></a>Implemente la identidad administrada y la asignación de roles.
Ejecute el cmdlet New-AzResourceGroupDeployment para crear la identidad administrada y agregar la asignación de roles:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <managedIdentityRGName> -TemplateFile ".\MangedIdentityAndSfrpRoleAssignment.json" -TemplateParameterFile ".\MangedIdentityAndSfrpRoleAssignment.Parameters.json" -Verbose
```

## <a name="2-assign-identity-to-the-node-type-resource"></a>2. Asignación de identidad al recurso de tipo de nodo

### <a name="add-managed-identity-properties-to-node-type-definition"></a>Adición de las propiedades de identidad administrada a la definición de tipo de nodo

Finalmente, agregue las propiedades `vmManagedIdentity` y `userAssignedIdentities` a la definición de tipo de nodo del clúster administrado con el id. de recurso completo de la identidad creada en el primer paso. Asegúrese de usar **2021-05-01** o posterior para `apiVersion`.

```json

 {
    "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
    "apiVersion": "2021-05-01",
    ...
    "properties": {
        "isPrimary" : true,
        "vmInstanceCount": 5,
        "dataDiskSizeGB": 100,
        "vmSize": "Standard_D2_v2",
        "vmImagePublisher" : "MicrosoftWindowsServer",
        "vmImageOffer" : "WindowsServer",
        "vmImageSku" : "2019-Datacenter",
        "vmImageVersion" : "latest",
        "vmManagedIdentity": {
            "userAssignedIdentities": [
                "[parameters('userAssignedIdentityResourceId')]"
            ]
        }
    }
}
```

### <a name="deploy-the-node-type-resource-assigning-the-identity"></a>Implementación del recurso de tipo de nodo que asigna la identidad

Ejecute el cmdlet New-AzResourceGroupDeployment para implementar la plantilla de clúster administrado de Service Fabric que asigna la identidad administrada al recurso de tipo de nodo.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <sfmcRGName> -TemplateFile ".\SfmcVmMangedIdentity.json" -TemplateParameterFile ".\SfmcVmMangedIdentity.Parameters.json" -Verbose
```

Después de la implementación, la identidad administrada creada se ha agregado al conjunto de escalado de máquinas virtuales del tipo de nodo designado y se puede usar como se espera, al igual que en cualquier clúster no administrado.

## <a name="troubleshooting"></a>Solución de problemas

No se puede agregar correctamente una asignación de roles con el siguiente error en la implementación:

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-error.png" alt-text="Error de implementación de Azure Portal que muestra el cliente con el identificador de objeto o aplicación de SFRP que no tiene permiso para realizar la actividad de administración de identidades":::

En este caso, asegúrese de que la asignación de roles se crea correctamente con el rol "Operador de identidades administradas". La asignación de roles se puede encontrar en Azure Portal, en el control de acceso del recurso de identidad administrada, como se muestra a continuación.

:::image type="content" source="media/how-to-managed-identity-managed-cluster-vmss/role-assignment-portal.png" alt-text="Propiedades de la asignación de roles para el proveedor de recursos de Service Fabric en la identidad administrada asignada por el usuario en Azure Portal":::

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de una aplicación en un clúster administrado de Service Fabric](./tutorial-managed-cluster-deploy-app.md)
