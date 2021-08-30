---
title: Declaración de recursos en Bicep
description: Se describe cómo declarar recursos para implementar en Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: a540a30cd93d9f1dc54f77355f2f6560444131c1
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228565"
---
# <a name="resource-declaration-in-bicep"></a>Declaración de recursos en Bicep

Para implementar un recurso a través de un archivo de Bicep, agregue una declaración de recurso usando la palabra clave `resource`.

## <a name="set-resource-type-and-version"></a>Definición del tipo de recurso y versión

Al agregar un recurso al archivo de Bicep, empiece por establecer el tipo de recurso y la versión de la API. Estos valores determinan las demás propiedades que están disponibles para el recurso.

En el ejemplo siguiente se muestra cómo establecer el tipo de recurso y la versión de la API para una cuenta de almacenamiento. En el ejemplo no se muestra la declaración de recursos completa.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  ...
}
```

Establezca un nombre simbólico para el recurso. En el ejemplo anterior, el nombre simbólico es `stg`. Puede usar cualquier valor como nombre simbólico, pero no puede ser el mismo que otro recurso, parámetro o variable del archivo de Bicep. El nombre simbólico no es lo mismo que el nombre del recurso. El nombre simbólico se usa para hacer referencia al recurso en otras partes del archivo de Bicep.

Bicep no admite `apiProfile`, que está disponible en [código JSON de plantillas de Azure Resource Manager (plantillas de ARM)](../templates/syntax.md).

## <a name="set-resource-name"></a>Definición del nombre del recurso

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

## <a name="set-location"></a>Establezca una ubicación

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

## <a name="set-tags"></a>Definición de etiquetas

Puede aplicar etiquetas a un recurso durante la implementación. Las etiquetas le ayudan a organizar de forma lógica los recursos implementados. Para ver ejemplos de las diferentes maneras de especificar las etiquetas, consulte [Etiquetas de plantillas de ARM](../management/tag-resources.md#arm-templates).

## <a name="set-managed-identities-for-azure-resources"></a>Establecimiento de identidades administradas para recursos de Azure

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

## <a name="set-resource-specific-properties"></a>Definición de propiedades específicas de recursos

Las propiedades anteriores son genéricas para la mayoría de los tipos de recursos. Después de establecer esos valores, tiene que establecer las propiedades que son específicas del tipo de recurso que va a implementar.

Use IntelliSense o la [referencia de plantillas](/azure/templates/) para decidir qué propiedades están disponibles y cuáles son obligatorias. En el ejemplo siguiente se establecen las propiedades restantes para una cuenta de almacenamiento.

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

## <a name="set-resource-dependencies"></a>Establecimiento de dependencias de recursos

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

## <a name="reference-existing-resources"></a>Referencia a recursos existentes

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

Para obtener más información sobre cómo establecer el ámbito, consulte [Funciones de ámbito para Bicep](bicep-functions-scope.md).

En los ejemplos anteriores no se implementa la cuenta de almacenamiento. En su lugar, puede acceder a las propiedades del recurso existente usando el nombre simbólico.

## <a name="next-steps"></a>Pasos siguientes

- Para implementar un recurso de manera condicional, consulte [Implementación condicional en Bicep](./conditional-resource-deployment.md).
