---
title: 'Funciones de Bicep: scopes'
description: Describe las funciones que se usarán en un archivo de Bicep para recuperar valores sobre la implementación.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: ee7ea8efbb19048ff5aa6b6ead2bf9d2495ad23a
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360240"
---
# <a name="scope-functions-for-bicep"></a>Funciones de ámbito para Bicep

En este artículo se describen las funciones de Bicep para obtener valores de ámbito.

## <a name="managementgroup"></a>managementGroup

`managementGroup()`

`managementGroup(name)`

Devuelve un objeto utilizado para establecer el ámbito en un grupo de administración.

Espacio de nombres: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Comentarios

`managementGroup()` solo se puede usar en [implementaciones de un grupo de administración](deploy-to-management-group.md). Devuelve el grupo de administración actual para la operación de implementación.

`managementGroup(name)` se puede usar para cualquier ámbito de implementación.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| name |No |string |El identificador único en el que se implementará el grupo de administración. No use el nombre para mostrar del grupo de administración. Si no proporciona un valor, se devuelve el grupo de administración actual. |

### <a name="return-value"></a>Valor devuelto

Un objeto que se usa para establecer la propiedad `scope` en un [módulo](modules.md#configure-module-scopes) o en un [tipo de recurso de extensión](scope-extension-resources.md).

### <a name="management-group-example"></a>Ejemplo de grupo de administración

En el ejemplo siguiente se establece el ámbito de un módulo en un grupo de administración.

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
``` 

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

`resourceGroup(resourceGroupName)`

`resourceGroup(subscriptionId, resourceGroupName)`

Devuelve un objeto utilizado para establecer el ámbito en un grupo de recursos.

Or

Devuelve un objeto que representa el grupo de recursos actual.

Espacio de nombres: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Comentarios

La función resourceGroup tiene dos usos distintos. Un uso es establecer el ámbito en un [módulo](modules.md#configure-module-scopes) o en un [tipo de recurso de extensión](scope-extension-resources.md). El otro uso es para obtener detalles sobre el grupo de recursos actual. La colocación de la función determina su uso. Cuando se usa para establecer la propiedad `scope`, devuelve un objeto de ámbito.

`resourceGroup()` se puede usar para establecer el ámbito u obtener detalles sobre el grupo de recursos.

`resourceGroup(resourceGroupName)` y `resourceGroup(subscriptionId, resourceGroupName)` solo se puede usar para establecer el ámbito.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| resourceGroupName |No |string | El nombre del grupo de recursos en el que se va a realizar la implementación. Si no proporciona un valor, se devuelve el grupo de recursos actual. |
| subscriptionId |No |string |El identificador único de la suscripción en la que se va a implementar. Si no proporciona un valor, se devuelve la suscripción actual. |

### <a name="return-value"></a>Valor devuelto

Cuando se usa para establecer el ámbito, la función devuelve un objeto que es válido para la propiedad `scope` en un módulo o tipo de recurso de extensión.

Cuando se usa para obtener detalles sobre el grupo de recursos, la función devuelve el formato siguiente:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

La propiedad **managedBy** solo se devuelve para los grupos de recursos que contienen recursos administrados por otro servicio. En los casos de Managed Applications, Databricks y AKS, el valor de la propiedad es el identificador del recurso que realiza la administración.

### <a name="resource-group-example"></a>Ejemplo de grupo de recursos

En el ejemplo siguiente se establece el ámbito de un módulo en un grupo de recursos.

```bicep
param resourceGroupName string

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  scope: resourceGroup(resourceGroupName)
}
```

En el ejemplo siguiente se devuelven las propiedades del grupo de recursos.

```bicep
output resourceGroupOutput object = resourceGroup()
```

Devuelve un objeto con el formato siguiente:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Un uso común de la función resourceGroup es crear recursos en la misma ubicación que el grupo de recursos. En el ejemplo siguiente se utiliza la ubicación del grupo de recursos para un valor de parámetro predeterminado.

```bicep
param location string = resourceGroup().location
```

También puede usar la función resourceGroup para aplicar etiquetas del grupo de recursos a un recurso. Para más información, consulte [Aplicación de etiquetas de un grupo de recursos](../management/tag-resources.md#apply-tags-from-resource-group).

## <a name="subscription"></a>subscription

`subscription()`

`subscription(subscriptionId)`

Devuelve un objeto utilizado para establecer el ámbito en una suscripción.

Or

Devuelve detalles sobre la suscripción para la implementación actual.

Espacio de nombres: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Comentarios

La función de suscripción tiene dos usos distintos. Un uso es establecer el ámbito en un [módulo](modules.md#configure-module-scopes) o en un [tipo de recurso de extensión](scope-extension-resources.md). El otro uso es para obtener detalles sobre la suscripción actual. La colocación de la función determina su uso. Cuando se usa para establecer la propiedad `scope`, devuelve un objeto de ámbito.

`subscription(subscriptionId)` solo se puede usar para establecer el ámbito.

`subscription()` se puede usar para establecer el ámbito u obtener detalles sobre la suscripción.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| subscriptionId |No |string |El identificador único de la suscripción en la que se va a implementar. Si no proporciona un valor, se devuelve la suscripción actual. |

### <a name="return-value"></a>Valor devuelto

Cuando se usa para establecer el ámbito, la función devuelve un objeto que es válido para la propiedad `scope` en un módulo o tipo de recurso de extensión.

Cuando se usa para obtener detalles sobre la suscripción, la función devuelve el formato siguiente:

```json
{
  "id": "/subscriptions/{subscription-id}",
  "subscriptionId": "{subscription-id}",
  "tenantId": "{tenant-id}",
  "displayName": "{name-of-subscription}"
}
```

### <a name="subscription-example"></a>Ejemplo de suscripción

En el ejemplo siguiente se establece el ámbito de un módulo en la suscripción.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToSub'
  scope: subscription()
}
```

En el ejemplo siguiente se devuelven los detalles de una suscripción.

```bicep
output subscriptionOutput object = subscription()
```

## <a name="tenant"></a>tenant

`tenant()`

Devuelve un objeto utilizado para establecer el ámbito en el inquilino.

Espacio de nombres: [az](bicep-functions.md#namespaces-for-functions).

### <a name="remarks"></a>Comentarios

`tenant()` se puede usar con cualquier ámbito de implementación. Siempre devuelve el inquilino actual.

### <a name="return-value"></a>Valor devuelto

Un objeto que se usa para establecer la propiedad `scope` en un [módulo](modules.md#configure-module-scopes) o en un [tipo de recurso de extensión](scope-extension-resources.md).

### <a name="tenant-example"></a>Ejemplo de inquilino

En el ejemplo siguiente se muestra un módulo que se implementa en el inquilino.

```bicep
module exampleModule 'module.bicep' = {
  name: 'deployToTenant'
  scope: tenant()
}
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los ámbitos de implementación, consulte:

* [Implementaciones de grupos de recursos](deploy-to-resource-group.md)
* [Implementaciones de suscripciones](deploy-to-subscription.md)
* [Implementaciones de grupos de administración](deploy-to-management-group.md)
* [Implementaciones de inquilinos](deploy-to-tenant.md)
