---
title: Uso de Bicep para implementar recursos en la suscripción
description: Describe cómo crear un archivo de Bicep que implementa recursos en el ámbito de la suscripción de Azure. Muestra cómo crear un grupo de recursos.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: df9938e209e163b19e666a26dec4176e83fc79e6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793747"
---
# <a name="subscription-deployments-with-bicep-files"></a>Implementaciones de suscripción con archivos de Bicep

En este artículo se describe cómo establecer el ámbito con Bicep al implementar recursos en una suscripción.

Para simplificar la administración de recursos, puede implementar recursos en el nivel de la suscripción de Azure. Por ejemplo, puede implementar [directivas](../../governance/policy/overview.md) y el [control de acceso basado en rol (RBAC de Azure)](../../role-based-access-control/overview.md) en su suscripción, y se aplicarán a ella en su totalidad. También puede crear grupos de recursos dentro de la suscripción e implementar recursos en grupos de recursos de la suscripción.

> [!NOTE]
> Puede implementar en 800 grupos de recursos distintos en una implementación de nivel de suscripción.

### <a name="microsoft-learn"></a>Microsoft Learn

Para obtener más información sobre los ámbitos de implementación y para obtener instrucciones prácticas, consulte [Implementación de recursos en suscripciones, grupos de administración e inquilinos mediante Bicep](/learn/modules/deploy-resources-scopes-bicep/) en **Microsoft Learn**.

## <a name="supported-resources"></a>Recursos compatibles

No todos los tipos de recursos se pueden implementar en el nivel de suscripción. En esta sección se enumeran los tipos de recursos que se admiten.

Para Azure Blueprints, use:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions (Blueprints)](/azure/templates/microsoft.blueprint/blueprints/versions)

Para las directivas de Azure, use:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Para el control de acceso basado en rol de Azure (Azure RBAC), use:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Para plantillas anidadas que se implementan en grupos de recursos, use:

* [deployments](/azure/templates/microsoft.resources/deployments)

Para crear grupos de recursos, use:

* [resourceGroups](/azure/templates/microsoft.resources/resourcegroups)

Para administrar su suscripción, use:

* [Configuraciones de Advisor](/azure/templates/microsoft.advisor/configurations)
* [budgets](/azure/templates/microsoft.consumption/budgets)
* [Perfil de Change Analysis](/azure/templates/microsoft.changeanalysis/profile)
* [supportPlanTypes](/azure/templates/microsoft.addons/supportproviders/supportplantypes)
* [etiquetas](/azure/templates/microsoft.resources/tags)

Otros tipos admitidos incluyen:

* [scopeAssignments](/azure/templates/microsoft.managednetwork/scopeassignments)
* [eventSubscriptions](/azure/templates/microsoft.eventgrid/eventsubscriptions)
* [peerAsns](/azure/templates/microsoft.peering/2019-09-01-preview/peerasns)

## <a name="set-scope"></a>Ámbito de conjunto

Para establecer el ámbito en la suscripción, use:

```bicep
targetScope = 'subscription'
```

## <a name="deployment-commands"></a>Comandos de implementación

Para implementar en una suscripción, use los comandos de implementación de nivel de suscripción.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para la CLI de Azure, use [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create). El ejemplo siguiente implementa una plantilla para crear un grupo de recursos:

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-file main.bicep \
  --parameters rgName=demoResourceGroup rgLocation=centralus
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para el comando de implementación de PowerShell, use [New-AzDeployment](/powershell/module/az.resources/new-azdeployment) o su alias `New-AzSubscriptionDeployment`. El ejemplo siguiente implementa una plantilla para crear un grupo de recursos:

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateFile main.bicep `
  -rgName demoResourceGroup `
  -rgLocation centralus
```

---

Para obtener información más detallada sobre los comandos y las opciones de implementación para la implementación de plantillas de Resource Manager, vea:

* [Implementación de recursos con plantillas de ARM y la CLI de Azure](deploy-cli.md)
* [Implementación de recursos con las plantillas de ARM y Azure PowerShell](deploy-powershell.md)
* [Implementación de plantillas de Resource Manager desde Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Ubicación y nombre de la implementación

En el caso de las implementaciones de nivel de suscripción, debe proporcionar una ubicación para la implementación. La ubicación de la implementación es independiente de la ubicación de los recursos que se implementan. La ubicación de implementación especifica dónde se almacenarán los datos de la implementación. Las implementaciones de [grupo de administración](deploy-to-management-group.md) e [inquilino](deploy-to-tenant.md) también requieren una ubicación. En las implementaciones de [grupo de recursos](deploy-to-resource-group.md), la ubicación del grupo de recursos se usa para almacenar los datos de implementación.

Puede proporcionar un nombre para la implementación o usar el nombre de implementación predeterminado. El nombre predeterminado es el nombre del archivo de plantilla. Por ejemplo, la implementación de una plantilla denominada _main.json_ crea un nombre de implementación predeterminado de **main**.

Para cada nombre de implementación, la ubicación es inmutable. No se puede crear una implementación en una ubicación si ya existe una implementación con el mismo nombre en otra ubicación. Por ejemplo, si crea una implementación de suscripción con el nombre **deployment1** en **centralus**, no podrá crear otra implementación con el nombre **deployment1**, sino una ubicación de **westus**. Si recibe el código de error `InvalidDeploymentLocation`, use un nombre diferente o utilice la ubicación de la implementación anterior que tenía ese mismo nombre.

## <a name="deployment-scopes"></a>Ámbitos de implementación

Al implementar en una suscripción, puede implementar los recursos en:

* la suscripción de destino de la operación
* cualquier suscripción en el inquilino
* grupos de recursos en la suscripción o en otras
* inquilino para la suscripción

Un [recurso de extensión](scope-extension-resources.md) se puede aplicar al ámbito de un destino distinto del destino de implementación.

El usuario que implementa la plantilla debe tener acceso al ámbito especificado.

### <a name="scope-to-subscription"></a>Ámbito de la suscripción

Para implementar recursos en la suscripción de destino, agregue esos recursos con la palabra clave `resource`.

```bicep
targetScope = 'subscription'

// resource group created in target subscription
resource exampleResource 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  ...
}
```

Para obtener ejemplos de cómo implementar en la suscripción, vea [Creación de grupos de recursos](#create-resource-groups) y [Asignación de definición de directiva](#assign-policy-definition).

Para implementar recursos en una suscripción diferente de la suscripción de la operación, agregue un [módulo](modules.md). Use la [función de suscripción](bicep-functions-scope.md#subscription) para establecer la propiedad `scope`. Proporcione la propiedad `subscriptionId` en el id. de la suscripción en la que quiere realizar la implementación.

```bicep
targetScope = 'subscription'

param otherSubscriptionID string

// module deployed at subscription level but in a different subscription
module exampleModule 'module.bicep' = {
  name: 'deployToDifferentSub'
  scope: subscription(otherSubscriptionID)
}
```

### <a name="scope-to-resource-group"></a>Ámbito del grupo de recursos

Para implementar recursos en un grupo de recursos dentro de la suscripción, agregue un módulo y establezca su propiedad `scope`. Si el grupo de recursos ya existe, use la [función resourceGroup](bicep-functions-scope.md#resourcegroup) para establecer el valor de ámbito. Proporcione el nombre del grupo de recursos.

```bicep
targetScope = 'subscription'

param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

Si el grupo de recursos se crea en el mismo archivo de Bicep, use el nombre simbólico del grupo de recursos para establecer el valor de ámbito. Para un ejemplo de cómo establecer el ámbito en el nombre simbólico, consulte [Creación del grupo de recursos y los recursos](#create-resource-group-and-resources).

### <a name="scope-to-tenant"></a>Ámbito del inquilino

Para crear recursos en el inquilino, agregue un módulo. Use la [función tenant](bicep-functions-scope.md#tenant) para establecer su propiedad `scope`.

El usuario que implementa la plantilla debe tener el [acceso necesario para realizar implementaciones en el inquilino](deploy-to-tenant.md#required-access).

En el ejemplo siguiente se incluye un módulo que se implementa en el inquilino.

```bicep
targetScope = 'subscription'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

En lugar de usar un módulo, puede establecer el ámbito en `tenant()` para algunos tipos de recursos. En el ejemplo siguiente se implementa un grupo de administración en el inquilino.

```bicep
targetScope = 'subscription'

param mgName string = 'mg-${uniqueString(newGuid())}'

// management group created at tenant
resource managementGroup 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output output string = mgName
```

Para más información, consulte [Grupo de administración](deploy-to-management-group.md#management-group).

## <a name="resource-groups"></a>Grupos de recursos

### <a name="create-resource-groups"></a>Crear grupos de recursos

Para crear un grupo de recursos, defina un recurso [Microsoft.Resources/resourceGroups](/azure/templates/microsoft.resources/allversions) con un nombre y una ubicación para el grupo de recursos.

En el ejemplo siguiente se crea un grupo de recursos vacío.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}
```

### <a name="create-resource-group-and-resources"></a>Creación del grupo de recursos y los recursos

Para crear el grupo de recursos e implementar recursos en él, agregue un módulo. El módulo incluye los recursos que se implementarán en el grupo de recursos. Establezca el ámbito del módulo en el nombre simbólico del grupo de recursos que cree. Puede implementar en hasta 800 grupos de recursos.

En el ejemplo siguiente se crea un grupo de recursos y se implementa una cuenta de almacenamiento en el grupo de recursos. Observe que la propiedad `scope` del módulo está establecida en `newRG`, que es el nombre simbólico del grupo de recursos que se está creando.

```bicep
targetScope='subscription'

param resourceGroupName string
param resourceGroupLocation string
param storageName string
param storageLocation string

resource newRG 'Microsoft.Resources/resourceGroups@2021-01-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
}

module storageAcct 'storage.bicep' = {
  name: 'storageModule'
  scope: newRG
  params: {
    storageLocation: storageLocation
    storageName: storageName
  }
}
```

El módulo usa un archivo de Bicep denominado **storage.bicep** con el contenido siguiente:

```bicep
param storageLocation string
param storageName string

resource storageAcct 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
  location: storageLocation
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}
```

## <a name="azure-policy"></a>Azure Policy

### <a name="assign-policy-definition"></a>Asignación de una definición de directiva

En el ejemplo siguiente se asigna una definición de directiva existente a la suscripción. Si la definición de directiva toma parámetros, proporciónelos como un objeto. Si la definición de directiva no toma parámetros, use el objeto vacío predeterminado.

```bicep
targetScope = 'subscription'

param policyDefinitionID string
param policyName string
param policyParameters object = {}

resource policyAssign 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: policyName
  properties: {
    policyDefinitionId: policyDefinitionID
    parameters: policyParameters
  }
}
```

### <a name="create-and-assign-policy-definitions"></a>Creación y asignación de definiciones de directiva

Puede [definir](../../governance/policy/concepts/definition-structure.md) y asignar una definición de directiva en el mismo archivo de Bicep.

```bicep
targetScope = 'subscription'

resource locationPolicy 'Microsoft.Authorization/policyDefinitions@2020-09-01' = {
  name: 'locationpolicy'
  properties: {
    policyType: 'Custom'
    parameters: {}
    policyRule: {
      if: {
        field: 'location'
        equals: 'northeurope'
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource locationRestrict 'Microsoft.Authorization/policyAssignments@2020-09-01' = {
  name: 'allowedLocation'
  properties: {
    policyDefinitionId: locationPolicy.id
  }
}
```

## <a name="access-control"></a>Control de acceso

Para más información sobre la asignación de roles, consulte [Incorporación de asignaciones de roles mediante plantillas de Azure Resource Manager](../../role-based-access-control/role-assignments-template.md).

En el ejemplo siguiente, se crea un grupo de recursos, se le aplica un bloqueo y se asigna un rol a una entidad de seguridad.

```bicep
targetScope = 'subscription'

@description('Name of the resourceGroup to create')
param resourceGroupName string

@description('Location for the resourceGroup')
param resourceGroupLocation string

@description('principalId of the user that will be given contributor access to the resourceGroup')
param principalId string

@description('roleDefinition to apply to the resourceGroup - default is contributor')
param roleDefinitionId string = 'b24988ac-6180-42a0-ab88-20f7382dd24c'

@description('Unique name for the roleAssignment in the format of a guid')
param roleAssignmentName string = guid(principalId, roleDefinitionId, resourceGroupName)

var roleID = '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/${roleDefinitionId}'

resource newResourceGroup 'Microsoft.Resources/resourceGroups@2019-10-01' = {
  name: resourceGroupName
  location: resourceGroupLocation
  properties: {}
}

module applyLock 'lock.bicep' = {
  name: 'applyLock'
  scope: newResourceGroup
}

module assignRole 'role.bicep' = {
  name: 'assignRBACRole'
  scope: newResourceGroup
  params: {
    principalId: principalId
    roleNameGuid: roleAssignmentName
    roleDefinitionId: roleID
  }
}
```

En el ejemplo siguiente se muestra el módulo para aplicar el bloqueo:

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

En el ejemplo siguiente se muestra el módulo para asignar el rol:

```bicep
@description('The principal to assign the role to')
param principalId string

@description('A GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

param roleDefinitionId string

resource roleNameGuid_resource 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: roleDefinitionId
    principalId: principalId
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre otros ámbitos, consulte:

* [Implementaciones de grupos de recursos](deploy-to-resource-group.md)
* [Implementaciones de grupos de administración](deploy-to-management-group.md)
* [Implementaciones de inquilinos](deploy-to-tenant.md)
