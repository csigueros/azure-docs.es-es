---
title: Uso de Bicep para implementar recursos en el inquilino
description: Se describe cómo implementar recursos en el ámbito de un inquilino en un archivo de Bicep.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: 3f446670cd85daa5b541cc9b5858ab768ac8411d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453415"
---
# <a name="tenant-deployments-with-bicep-file"></a>Implementaciones de inquilino con el archivo Bicep

A medida que crece la organización, es posible que necesite definir y asignar [directivas](../../governance/policy/overview.md) o el [control de acceso basado en rol (RBAC de Azure)](../../role-based-access-control/overview.md) en el inquilino de Azure AD. Con las plantillas de nivel de inquilino, puede aplicar directivas y asignar roles a nivel global de forma declarativa.

## <a name="supported-resources"></a>Recursos compatibles

No todos los tipos de recursos se pueden implementar a nivel de inquilino. En esta sección se enumeran los tipos de recursos que se admiten.

Para el control de acceso basado en rol de Azure (Azure RBAC), use:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

Para plantillas anidadas que se implementan en grupos de administración, suscripciones o grupos de recursos, use:

* [deployments](/azure/templates/microsoft.resources/deployments)

Para crear grupos de administración, use:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

Para crear suscripciones, use:

* [alias](/azure/templates/microsoft.subscription/aliases)

Para la administración de costos, use:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [instructions](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

Para configurar el portal, use:

* [tenantConfigurations](/azure/templates/microsoft.portal/tenantconfigurations)

Las definiciones de directiva integradas son recursos de nivel de inquilino, pero no se pueden implementar definiciones de directiva personalizadas en un inquilino. Para obtener un ejemplo de asignación de una definición de directiva integrada a un recurso, consulte el [ejemplo tenantResourceId](./bicep-functions-resource.md#tenantresourceid).

## <a name="set-scope"></a>Ámbito de conjunto

Para establecer el ámbito en el inquilino, use:

```bicep
targetScope = 'tenant'
```

## <a name="required-access"></a>Acceso necesario

La entidad de seguridad que implementa la plantilla debe tener permisos para crear recursos en el ámbito del inquilino. La entidad de seguridad debe tener permiso para ejecutar las acciones de implementación (`Microsoft.Resources/deployments/*`) y para crear los recursos definidos en la plantilla. Por ejemplo, para crear un grupo de administración, la entidad de seguridad debe tener permiso de colaborador en el ámbito del inquilino. Para crear asignaciones de roles, la entidad de seguridad debe tener permiso de propietario.

El administrador global de Azure Active Directory no tiene permiso para asignar roles de forma automática. Para habilitar las implementaciones de plantilla en el ámbito del inquilino, el administrador global debe realizar los siguientes pasos:

1. Eleve el acceso a la cuenta para que el administrador global pueda asignar roles. Para más información, consulte [Elevación de los privilegios de acceso para administrar todas las suscripciones y los grupos de administración de Azure](../../role-based-access-control/elevate-access-global-admin.md).

1. Asigne el rol de propietario o colaborador a la entidad de seguridad que necesita implementar las plantillas.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

La entidad de seguridad tiene ahora los permisos necesarios para implementar la plantilla.

## <a name="deployment-commands"></a>Comandos de implementación

Los comandos para las implementaciones de inquilino son diferentes de los comandos para las implementaciones de grupos de recursos.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para la CLI de Azure, use [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-file main.bicep
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

En el caso de Azure PowerShell, use [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateFile main.bicep
```

---

Para obtener información más detallada sobre los comandos de implementación y las opciones para implementar plantillas de Resource Manager, consulte:

* [Implementación de recursos con plantillas de ARM y la CLI de Azure](deploy-cli.md)
* [Implementación de recursos con las plantillas de ARM y Azure PowerShell](deploy-powershell.md)
* [Implementación de plantillas de Resource Manager desde Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Ubicación y nombre de la implementación

En el caso de las implementaciones de nivel de inquilino, debe proporcionar una ubicación para la implementación. La ubicación de la implementación es independiente de la ubicación de los recursos que se implementan. La ubicación de implementación especifica dónde se almacenarán los datos de la implementación. Las implementaciones de [suscripción](deploy-to-subscription.md) y [grupo de administración](deploy-to-management-group.md) también requieren una ubicación. En las implementaciones de [grupo de recursos](deploy-to-resource-group.md), la ubicación del grupo de recursos se usa para almacenar los datos de implementación.

Puede proporcionar un nombre para la implementación o usar el nombre de implementación predeterminado. El nombre predeterminado es el nombre del archivo de plantilla. Por ejemplo, la implementación de un archivo denominado _main.bicep_ crea un nombre de implementación predeterminado de **main**.

Para cada nombre de implementación, la ubicación es inmutable. No se puede crear una implementación en una ubicación si ya existe una implementación con el mismo nombre en otra ubicación. Por ejemplo, si crea una implementación de inquilino con el nombre **deployment1** en **centralus**, no podrá crear otra implementación con el nombre **deployment1**, sino una ubicación de **westus**. Si recibe el código de error `InvalidDeploymentLocation`, use un nombre diferente o utilice la ubicación de la implementación anterior que tenía ese mismo nombre.

## <a name="deployment-scopes"></a>Ámbitos de implementación

Al implementar en un inquilino, puede implementar los recursos en:

* el inquilino
* grupos de administración dentro del inquilino
* subscriptions
* grupos de recursos

Un [recurso de extensión](scope-extension-resources.md) se puede limitar a un destino distinto del destino de implementación.

El usuario que implementa la plantilla debe tener acceso al ámbito especificado.

En esta sección se muestra cómo especificar distintos ámbitos. Puede combinar estos distintos ámbitos en una sola plantilla.

### <a name="scope-to-tenant"></a>Ámbito del inquilino

Los recursos definidos dentro del archivo de Bicep se aplican al inquilino.

```bicep
targetScope = 'tenant'

// create resource at tenant
resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  ...
}
```

### <a name="scope-to-management-group"></a>Ámbito del grupo de administración

Para establecer como destino un grupo de administración dentro del inquilino, agregue un [módulo](modules.md). Use la [función managementGroup](bicep-functions-scope.md#managementgroup) para establecer su propiedad `scope`. Proporcione el nombre del grupo de administración.

```bicep
targetScope = 'tenant'

param managementGroupName string

// create resources at management group level
module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

### <a name="scope-to-subscription"></a>Ámbito de la suscripción

Para establecer como destino una suscripción dentro del inquilino, agregue un módulo. Use la [función de suscripción](bicep-functions-scope.md#subscription) para establecer su propiedad `scope`. Proporcione el identificador de la suscripción.

```bicep
targetScope = 'tenant'

param subscriptionID string

// create resources at subscription level
module  'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>Ámbito del grupo de recursos

Para establecer como destino un grupo de recursos dentro del inquilino, agregue un módulo. Use la [función resourceGroup](bicep-functions-scope.md#resourcegroup) para establecer su propiedad `scope`. Proporcione el identificador de suscripción y el nombre del grupo de recursos.

```bicep
targetScope = 'tenant'

param resourceGroupName string
param subscriptionID string

// create resources at resource group level
module  'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

## <a name="create-management-group"></a>Creación de un grupo de administración

La siguiente plantilla crea un grupo de administración.

```bicep
targetScope = 'tenant'
param mgName string = 'mg-${uniqueString(newGuid())}'

resource mgName_resource 'Microsoft.Management/managementGroups@2020-02-01' = {
  name: mgName
  properties: {}
}
```

Si su cuenta no tiene permiso para la implementación en el inquilino, puede crear grupos de administración mediante la implementación en otro ámbito. Para más información, consulte [Grupo de administración](deploy-to-management-group.md#management-group).

## <a name="assign-role"></a>Asignación de un rol

La siguiente plantilla asigna un rol en el ámbito del inquilino.

```bicep
targetScope = 'tenant'

@description('principalId if the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition for the assignment - default is owner')
param roleDefinitionId string = '8e3af657-a8ff-443c-a75c-2fe8c4bcb635'

var roleAssignmentName = guid(principalId, roleDefinitionId)

resource roleAssignment 'Microsoft.Authorization/roleAssignments@2020-03-01-preview' = {
  name: roleAssignmentName
  properties: {
    roleDefinitionId: tenantResourceId('Microsoft.Authorization/roleDefinitions', roleDefinitionId)
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre otros ámbitos, consulte:

* [Implementaciones de grupos de recursos](deploy-to-resource-group.md)
* [Implementaciones de suscripciones](deploy-to-subscription.md)
* [Implementaciones de grupos de administración](deploy-to-management-group.md)
