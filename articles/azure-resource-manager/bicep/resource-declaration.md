---
title: Declaración de recursos en Bicep
description: Se describe cómo declarar recursos para implementar en Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 10/25/2021
ms.openlocfilehash: 28f61a3fb3a40cb4db0a06f3c59fe6b07ec7d5bc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074220"
---
# <a name="resource-declaration-in-bicep"></a>Declaración de recursos en Bicep

En este artículo se describe la sintaxis que se usa para agregar un recurso al archivo Bicep.

## <a name="declaration"></a>Declaración

Agregue una declaración de recursos mediante la palabra clave `resource`. Establezca un nombre simbólico para el recurso. El nombre simbólico no es lo mismo que el nombre del recurso. El nombre simbólico se usa para hacer referencia al recurso en otras partes del archivo de Bicep.

```bicep
resource <symbolic-name> '<full-type-name>@<api-version>' = {
  <resource-properties>
}
```

Por lo tanto, una declaración de una cuenta de almacenamiento puede comenzar por:

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  ...
}
```

Los nombres simbólicos distinguen mayúsculas de minúsculas.  Pueden contener letras, números y _, pero no pueden empezar con un número.

Para ver los tipos de recursos y la versión disponibles, consulte [Referencia de recursos de Bicep](/azure/templates/). Bicep no admite `apiProfile`, que está disponible en [código JSON de plantillas de Azure Resource Manager (plantillas de ARM)](../templates/syntax.md).

Para implementar un recurso de forma condicional, use la sintaxis `if`. Para obtener más información, consulte [Implementación condicional en Bicep](conditional-resource-deployment.md).

```bicep
resource <symbolic-name> '<full-type-name>@<api-version>' = if (condition) {
  <resource-properties>
}
```

Para implementar más de una instancia de un recurso, use la sintaxis `for`. Para obtener más información, consulte [Bucles iterativos en Bicep](loops.md).

```bicep
resource <symbolic-name> '<full-type-name>@<api-version>' = [for <item> in <collection>: {
  <properties-to-repeat>
}]
```

También puede usar la sintaxis `for` en las propiedades del recurso para crear una matriz.

```bicep
resource <symbolic-name> '<full-type-name>@<api-version>' = {
  properties: {
    <array-property>: [for <item> in <collection>: <value-to-repeat>]
  }
}
```

## <a name="resource-name"></a>Nombre del recurso

Cada recurso tiene un nombre. Al establecer el nombre del recurso, preste atención a las [reglas y restricciones de los nombres de los recursos](../management/resource-name-rules.md).

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  ...
}
```

Normalmente, el nombre se establece en un parámetro para que pueda pasar valores diferentes durante la implementación.

```bicep
@minLength(3)
@maxLength(24)
param storageAccountName string

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageAccountName
  ...
}
```

## <a name="location"></a>Location

Muchos recursos requieren una ubicación. Puede determinar si el recurso necesita una ubicación a través de IntelliSense o de la [referencia de plantillas](/azure/templates/). En el ejemplo siguiente se agrega un parámetro de ubicación que se usa para la cuenta de almacenamiento.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  ...
}
```

Normalmente, la ubicación se establece en un parámetro para que pueda realizar la implementación en diferentes ubicaciones.

```bicep
param location string = resourceGroup().location

resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: location
  ...
}
```

Se admiten diferentes tipos de recursos en diferentes ubicaciones. Si desea obtener las ubicaciones admitidas para un servicio de Azure, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/).  Para obtener las ubicaciones admitidas para un tipo de recurso, use Azure PowerShell o la CLI de Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="tags"></a>Etiquetas

Puede aplicar etiquetas a un recurso durante la implementación. Las etiquetas le ayudan a organizar de forma lógica los recursos implementados. Para ver ejemplos de las diferentes maneras de especificar las etiquetas, consulte [Etiquetas de plantillas de ARM](../management/tag-resources.md#arm-templates).

## <a name="managed-identities-for-azure-resources"></a>Identidades administradas de recursos de Azure

Algunos recursos admiten [identidades administradas para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md). Dichos recursos tienen un objeto de identidad en el nivel raíz de la declaración de recursos.

Puede usar identidades administradas asignadas por el sistema o por el usuario.

En el siguiente ejemplo se muestra cómo configurar una identidad asignada por el sistema para un clúster de Azure Kubernetes Service.

```bicep
resource aks 'Microsoft.ContainerService/managedClusters@2020-09-01' = {
  name: clusterName
  location: location
  tags: tags
  identity: {
    type: 'SystemAssigned'
  }
```

En el siguiente ejemplo se muestra cómo configurar una identidad asignada por el usuario para una máquina virtual.

```bicep
param userAssignedIdentity string

resource vm 'Microsoft.Compute/virtualMachines@2020-06-01' = {
  name: vmName
  location: location
  identity: {
    type: 'UserAssigned'
    userAssignedIdentities: {
      '${userAssignedIdentity}': {}
    }
  }
```

## <a name="resource-specific-properties"></a>Propiedades específicas de recursos

Las propiedades anteriores son genéricas para la mayoría de los tipos de recursos. Después de establecer esos valores, tiene que establecer las propiedades que son específicas del tipo de recurso que va a implementar.

Use IntelliSense o la [referencia de recursos de Bicep](/azure/templates/) para determinar qué propiedades están disponibles y cuáles son obligatorias. En el ejemplo siguiente se establecen las propiedades restantes para una cuenta de almacenamiento.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'examplestorage'
  location: 'eastus'
  sku: {
    name: 'Standard_LRS'
    tier: 'Standard'
  }
  kind: 'StorageV2'
  properties: {
    accessTier: 'Hot'
  }
}
```

## <a name="dependencies"></a>Dependencias

Al implementar recursos, es posible que tenga que asegurarse de que existen algunos recursos antes de crear otros. Por ejemplo, necesitará un servidor SQL lógico para poder implementar una base de datos. Establezca esta relación marcando un recurso como dependiente de otro recurso. El orden de implementación de los recursos puede verse afectado por dos factores: [dependencia implícita](#implicit-dependency) y [dependencia explícita](#explicit-dependency).

Azure Resource Manager evalúa las dependencias entre recursos y los implementa en su orden dependiente. Cuando no hay recursos dependientes entre sí, Resource Manager los implementa en paralelo. Solo tiene que definir las dependencias de recursos que se implementan en el mismo archivo de Bicep.

### <a name="implicit-dependency"></a>Dependencia implícita

Se crea una dependencia implícita cuando una declaración de recursos hace referencia a otro recurso de la misma implementación. Por ejemplo, a continuación la segunda definición de recurso hace referencia a *dnsZone*:

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'myZone'
  location: 'global'
}

resource otherResource 'Microsoft.Example/examples@2020-06-01' = {
  name: 'exampleResource'
  properties: {
    // get read-only DNS zone property
    nameServers: dnsZone.properties.nameServers
  }
}
```

Un recurso anidado también tiene una dependencia implícita en el recurso que lo contiene.

```bicep
resource myParent 'My.Rp/parentType@2020-01-01' = {
  name: 'myParent'
  location: 'West US'

  // depends on 'myParent' implicitly
  resource myChild 'childType' = {
    name: 'myChild'
  }
}
```

Cuando exista una dependencia implícita, **no agregue otra explícita**.

Para obtener más información sobre los recursos anidados, consulte [Establecimiento del nombre y el tipo de recursos secundarios en Bicep](./child-resource-name-type.md).

### <a name="explicit-dependency"></a>Dependencia explícita

Una dependencia explícita se declara con la propiedad `dependsOn`. La propiedad acepta una matriz de identificadores de recurso, por lo que puede especificar más de una dependencia.

En el siguiente ejemplo se muestra una zona DNS denominada `otherZone` que depende de otra denominada `dnsZone`:

```bicep
resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoeZone1'
  location: 'global'
}

resource otherZone 'Microsoft.Network/dnszones@2018-05-01' = {
  name: 'demoZone2'
  location: 'global'
  dependsOn: [
    dnsZone
  ]
}
```

Si bien puede que se sienta tentado a usar `dependsOn` para asignar relaciones entre los recursos, es importante comprender por qué lo hace. Por ejemplo, para documentar cómo están interconectados los recursos, `dependsOn` no es el enfoque correcto. Después de realizar la implementación, no se pueden consultar los recursos que se definieron en el elemento `dependsOn`. La configuración de dependencias innecesarias reduce el tiempo de implementación porque Resource Manager no puede implementar esos recursos en paralelo.

Aunque a veces se requieren dependencias explícitas, es poco frecuente que sean necesarias. En la mayoría de los casos, puede usar un nombre simbólico para insinuar la dependencia entre recursos. Si necesita establecer una dependencias explícita, tenga en cuenta si hay una manera de eliminarla.

### <a name="visualize-dependencies"></a>Visualización de dependencias

Visual Studio Code proporciona una herramienta para visualizar las dependencias. Abra un archivo de Bicep en Visual Studio Code y, a continuación, seleccione el botón del visualizador en la esquina superior izquierda.  En la siguiente captura de pantalla se muestran las dependencias de una máquina virtual:

:::image type="content" source="./media/resource-declaration/bicep-resource-visualizer.png" alt-text="Captura de pantalla del visualizador de recursos de Bicep de Visual Studio Code":::

## <a name="existing-resources"></a>Recursos existentes

Para hacer referencia a un recurso que esté fuera del archivo de Bicep actual, use la palabra clave `existing` en una declaración de recurso.

Cuando use la palabra clave `existing`, proporcione la propiedad `name` del recurso. En el siguiente ejemplo se obtiene una cuenta de almacenamiento existente en el mismo grupo de recursos que la implementación actual.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

También puede establecer la propiedad `scope` para acceder a un recurso en otro ámbito. En el siguiente ejemplo se hace referencia a una cuenta de almacenamiento existente en otro grupo de recursos.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' existing = {
  name: 'examplestorage'
  scope: resourceGroup(exampleRG)
}

output blobEndpoint string = stg.properties.primaryEndpoints.blob
```

Si intenta hacer referencia a un recurso que no existe, se produce el error `NotFound` y se produce un error en la implementación.

Para obtener más información sobre cómo establecer el ámbito, consulte [Funciones de ámbito para Bicep](bicep-functions-scope.md).

En los ejemplos anteriores no se implementa la cuenta de almacenamiento. En su lugar, puede acceder a las propiedades del recurso existente usando el nombre simbólico.

## <a name="next-steps"></a>Pasos siguientes

- Para implementar un recurso de manera condicional, consulte [Implementación condicional en Bicep](./conditional-resource-deployment.md).
