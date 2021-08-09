---
title: Ámbito en tipos de recursos de extensión (Bicep)
description: Aquí se describe cómo utilizar la propiedad del ámbito al implementar tipos de recursos de extensión con Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: 59b6576dfb1bd5e0ac4f56e6a59b6ea4d5c4b5f4
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "111027245"
---
# <a name="set-scope-for-extension-resources-in-bicep"></a>Establecimiento del ámbito de los recursos de extensión en Bicep

Un recurso de extensión es un recurso que modifica otro recurso. Por ejemplo, puede asignar un rol a un recurso. La asignación de roles es un tipo de recurso de extensión.

Para obtener una lista completa de los tipos de recursos de extensión, consulte los [Tipos de recursos que amplían las capacidades de otros recursos](../management/extension-resource-types.md).

En este artículo se muestra cómo establecer el ámbito de un tipo de recurso de extensión cuando se implementa con un archivo de Bicep. Asimismo, describe la propiedad de ámbito que está disponible para los recursos de extensión cuando esta se aplica a un recurso.

> [!NOTE]
> La propiedad scope solo está disponible para los tipos de recursos de extensión. Para especificar un ámbito diferente para un tipo de recurso que no sea un tipo de extensión, use un [módulo](modules.md).

## <a name="apply-at-deployment-scope"></a>Aplicación en el ámbito de implementación

Para aplicar un tipo de recurso de extensión en el ámbito de implementación de destino, agregue el recurso a la plantilla, tal como haría con cualquier tipo de recurso. Los ámbitos disponibles son el [grupo de recursos](deploy-to-resource-group.md), la [suscripción](deploy-to-subscription.md), el [grupo de administración](deploy-to-management-group.md) y el [inquilino](deploy-to-tenant.md). El ámbito de implementación debe admitir el tipo de recurso.

En la plantilla siguiente se implementa un bloqueo.

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

En el ejemplo siguiente se asigna un rol.

```bicep
targetScope = 'subscription'

@description('The principal to assign the role to')
param principalId string

@allowed([
  'Owner'
  'Contributor'
  'Reader'
])
@description('Built-in role to assign')
param builtInRoleType string

@description('A new GUID used to identify the role assignment')
param roleNameGuid string = newGuid()

var role = {
  Owner: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635'
  Contributor: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c'
  Reader: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7'
}

resource roleAssignSub 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: role[builtInRoleType]
    principalId: principalId
  }
}
```

## <a name="apply-to-resource"></a>Aplicación al recurso

Para aplicar un recurso de extensión a un recurso, use la propiedad `scope`. Establezca la propiedad de ámbito en el nombre del recurso al que va a agregar la extensión. La propiedad de ámbito es una propiedad raíz del tipo de recurso de extensión.

En el ejemplo siguiente se crea una cuenta de almacenamiento y se aplica un rol a la misma.

```bicep
@description('The principal to assign the role to')
param principalId string

@allowed([
  'Owner'
  'Contributor'
  'Reader'
])
@description('Built-in role to assign')
param builtInRoleType string

@description('A new GUID used to identify the role assignment')
param roleNameGuid string = newGuid()
param location string = resourceGroup().location

var role = {
  Owner: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635'
  Contributor: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c'
  Reader: '/subscriptions/${subscription().subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7'
}
var uniqueStorageName = 'storage${uniqueString(resourceGroup().id)}'

resource storageName 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}

resource roleAssignStorage 'Microsoft.Authorization/roleAssignments@2020-04-01-preview' = {
  name: roleNameGuid
  properties: {
    roleDefinitionId: role[builtInRoleType]
    principalId: principalId
  }
  scope: storageName
  dependsOn: [
    storageName
  ]
}
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre la implementación en ámbitos, consulte:

* [Implementaciones de grupos de recursos](deploy-to-resource-group.md)
* [Implementaciones de suscripciones](deploy-to-subscription.md)
* [Implementaciones de grupos de administración](deploy-to-management-group.md)
* [Implementaciones de inquilinos](deploy-to-tenant.md)