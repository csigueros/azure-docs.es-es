---
title: Uso de Bicep para implementar recursos en un grupo de administración
description: Describe cómo crear un archivo de Bicep que implementa recursos en el ámbito del grupo de administración.
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 58fbed44045a90f4f344117fd76f7de8b0493771
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/03/2021
ms.locfileid: "111372200"
---
# <a name="management-group-deployments-with-bicep-files"></a>Implementaciones de grupos de administración con archivos de Bicep

En este artículo se describe cómo establecer el ámbito con Bicep al implementar en un grupo de administración.

A medida que la organización madura, puede implementar un archivo de Bicep para crear recursos en el nivel de grupo de administración. Por ejemplo, es posible que deba definir y asignar [directivas](../../governance/policy/overview.md) o el [control de acceso basado en rol de Azure (RBAC de Azure)](../../role-based-access-control/overview.md) para un grupo de administración. Con las plantillas de nivel de grupo de administración, puede aplicar directivas y asignar roles mediante declaración en el nivel de grupo de administración.

## <a name="supported-resources"></a>Recursos compatibles

No todos los tipos de recursos se pueden implementar en el nivel de grupo de administración. En esta sección se enumeran los tipos de recursos que se admiten.

Para Azure Blueprints, use:

* [artifacts](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [blueprints](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [versions](/azure/templates/microsoft.blueprint/blueprints/versions)

En Azure Policy, use:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [remediations](/azure/templates/microsoft.policyinsights/remediations)

Para el control de acceso basado en rol de Azure (Azure RBAC), use:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Para plantillas anidadas que se implementan en suscripciones o grupos de recursos, use:

* [deployments](/azure/templates/microsoft.resources/deployments)

Para administrar los recursos, use:

* [etiquetas](/azure/templates/microsoft.resources/tags)

Los grupos de administración son recursos de nivel de inquilino. Sin embargo, puede crear grupos de administración en una implementación de tales grupos estableciendo el ámbito del nuevo grupo en el inquilino. Consulte [Grupo de administración](#management-group).

## <a name="set-scope"></a>Ámbito de conjunto

Para establecer el ámbito en el grupo de administración, use:

```bicep
targetScope = 'managementGroup'
```

## <a name="deployment-commands"></a>Comandos de implementación

Para implementar en un grupo de administración, use los comandos de implementación de grupos de administración.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para la CLI de Azure, use [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

En el caso de Azure PowerShell, use [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

---

Para obtener información más detallada sobre los comandos de implementación y las opciones para implementar plantillas de Resource Manager, consulte:

* [Implementación de recursos con plantillas de ARM y la CLI de Azure](deploy-cli.md)
* [Implementación de recursos con las plantillas de ARM y Azure PowerShell](deploy-powershell.md)
* [Implementación de plantillas de Resource Manager desde Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-location-and-name"></a>Ubicación y nombre de la implementación

En el caso de las implementaciones de nivel de grupo de administración, debe proporcionar una ubicación para la implementación. La ubicación de la implementación es independiente de la ubicación de los recursos que se implementan. La ubicación de implementación especifica dónde se almacenarán los datos de la implementación. Las implementaciones de [suscripción](deploy-to-subscription.md) e [inquilino](deploy-to-tenant.md) también requieren una ubicación. En las implementaciones de [grupo de recursos](deploy-to-resource-group.md), la ubicación del grupo de recursos se usa para almacenar los datos de implementación.

Puede proporcionar un nombre para la implementación o usar el nombre de implementación predeterminado. El nombre predeterminado es el nombre del archivo de plantilla. Por ejemplo, la implementación de una plantilla denominada _main.bicep_ crea un nombre de implementación predeterminado de **main**.

Para cada nombre de implementación, la ubicación es inmutable. No se puede crear una implementación en una ubicación si ya existe una implementación con el mismo nombre en otra ubicación. Por ejemplo, si crea una implementación de grupo de administración con el nombre **deployment1** en **centralus**, no podrá crear otra implementación con el nombre **deployment1**, sino una ubicación de **westus**. Si recibe el código de error `InvalidDeploymentLocation`, use un nombre diferente o utilice la ubicación de la implementación anterior que tenía ese mismo nombre.

## <a name="deployment-scopes"></a>Ámbitos de implementación

Al implementar en un grupo de administración, puede implementar los recursos en:

* el grupo de administración de destino de la operación
* otro grupo de administración en el inquilino
* suscripciones en el grupo de administración
* grupos de recursos en el grupo de administración
* el inquilino del grupo de recursos

Un [recurso de extensión](scope-extension-resources.md) se puede limitar a un destino distinto del destino de implementación.

El usuario que implementa la plantilla debe tener acceso al ámbito especificado.

### <a name="scope-to-management-group"></a>Ámbito del grupo de administración

Para implementar recursos en el grupo de administración de destino, agregue esos recursos con la palabra clave `resource`.

```bicep
targetScope = 'managementGroup'

// policy definition created in the management group
resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  ...
}
```

Para establecer como destino otro grupo de administración, agregue un módulo. Use la [función managementGroup](bicep-functions-scope.md#managementgroup) para establecer la propiedad `scope`. Proporcione el nombre del grupo de administración.

```bicep
targetScope = 'managementGroup'

param otherManagementGroupName string

// module deployed at management group level but in a different management group
module exampleModule 'module.bicep' = {
  name: 'deployToDifferntMG'
  scope: managementGroup(otherManagementGroupName)
}
```

### <a name="scope-to-subscription"></a>Ámbito de la suscripción

También puede establecer como destino suscripciones con un grupo de administración. El usuario que implementa la plantilla debe tener acceso al ámbito especificado.

Para establecer como destino una suscripción dentro del grupo de administración, agregue un módulo. Use la [función de suscripción](bicep-functions-scope.md#subscription) para establecer la propiedad `scope`. Proporcione el identificador de la suscripción.

```bicep
targetScope = 'managementGroup'

param subscriptionID string

// module deployed to subscription in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription(subscriptionID)
}
```

### <a name="scope-to-resource-group"></a>Ámbito del grupo de recursos

También puede dirigirse a los grupos de recursos dentro del grupo de administración. El usuario que implementa la plantilla debe tener acceso al ámbito especificado.

Para establecer como destino un grupo de recursos dentro del grupo de administración, agregue un módulo. Use la [función resourceGroup](bicep-functions-scope.md#resourcegroup) para establecer la propiedad `scope`.  Proporcione el identificador de suscripción y el nombre del grupo de recursos.

```bicep
targetScope = 'managementGroup'

param subscriptionID string
param resourceGroupName string

// module deployed to resource group in the management group
module exampleModule 'module.bicep' = {
  name: 'deployToRG'
  scope: resourceGroup(subscriptionID, resourceGroupName)
}
```

### <a name="scope-to-tenant"></a>Ámbito del inquilino

Para crear recursos en el inquilino, agregue un módulo. Use la [función tenant](bicep-functions-scope.md#tenant) para establecer su propiedad `scope`. El usuario que implementa la plantilla debe tener el [acceso necesario para realizar implementaciones en el inquilino](deploy-to-tenant.md#required-access).

```bicep
targetScope = 'managementGroup'

// module deployed at tenant level
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

O bien, puede establecer el ámbito en `/` para algunos tipos de recursos, como los grupos de administración. La creación de un grupo de administración se describe en la sección siguiente.

## <a name="management-group"></a>Grupo de administración

Para crear un grupo de administración en una implementación de grupos de administración, debe establecer el ámbito en `/` para el grupo.

En el ejemplo siguiente se crea un grupo de administración en el grupo de administración raíz.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {}
}

output newManagementGroup string = mgName
```

En el ejemplo siguiente se crea un grupo de administración en el grupo de administración especificado como primario.

```bicep
targetScope = 'managementGroup'

param mgName string = 'mg-${uniqueString(newGuid())}'
param parentMGName string

resource newMG 'Microsoft.Management/managementGroups@2020-05-01' = {
  scope: tenant()
  name: mgName
  properties: {
    details: {
      parent: {
        id: parentMG.id
      }
    }
  }
}

resource parentMG 'Microsoft.Management/managementGroups@2020-05-01' existing = {
  name: parentMGName
  scope: tenant()
}

output newManagementGroup string = mgName
```

## <a name="subscriptions"></a>Suscripciones

Para usar una plantilla de ARM para crear una nueva suscripción de Azure en un grupo de administración, consulte:

* [Creación de suscripciones de Contrato Enterprise de Azure mediante programación con las API más recientes](../../cost-management-billing/manage/programmatically-create-subscription-enterprise-agreement.md)
* [Creación de suscripciones de Azure mediante programación para el Contrato de cliente de Microsoft con las API más recientes](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-customer-agreement.md)
* [Creación de suscripciones de Azure mediante programación para Microsoft Partner Agreement con las API más recientes](../../cost-management-billing/manage/programmatically-create-subscription-microsoft-partner-agreement.md)

Para implementar una plantilla que mueve una suscripción de Azure existente a un nuevo grupo de administración, consulte [Traslado de las suscripciones de la plantilla de ARM](../../governance/management-groups/manage.md#move-subscriptions-in-arm-template).

## <a name="azure-policy"></a>Azure Policy

Las definiciones de directivas personalizadas que se implementan en un grupo de administración son extensiones del grupo de administración. Para obtener el identificador de una definición de directiva personalizada, use la función [extensionResourceId()](./bicep-functions-resource.md#extensionresourceid). Las definiciones de directivas integradas son recursos del nivel de inquilino. Para obtener el identificador de una definición de directiva integrada, use la función [tenantResourceId](./bicep-functions-resource.md#tenantresourceid).

En el ejemplo siguiente se muestra cómo [definir](../../governance/policy/concepts/definition-structure.md) una directiva en el nivel de grupo de administración y cómo asignarla.

```bicep
targetScope = 'managementGroup'

@description('An array of the allowed locations, all other locations will be denied by the created policy.')
param allowedLocations array = [
  'australiaeast'
  'australiasoutheast'
  'australiacentral'
]

resource policyDefinition 'Microsoft.Authorization/policyDefinitions@2019-09-01' = {
  name: 'locationRestriction'
  properties: {
    policyType: 'Custom'
    mode: 'All'
    parameters: {}
    policyRule: {
      if: {
        not: {
          field: 'location'
          in: allowedLocations
        }
      }
      then: {
        effect: 'deny'
      }
    }
  }
}

resource policyAssignment 'Microsoft.Authorization/policyAssignments@2019-09-01' = {
  name: 'locationAssignment'
  properties: {
    policyDefinitionId: policyDefinition.id
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre otros ámbitos, consulte:

* [Implementaciones de grupos de recursos](deploy-to-resource-group.md)
* [Implementaciones de suscripciones](deploy-to-subscription.md)
* [Implementaciones de inquilinos](deploy-to-tenant.md)
