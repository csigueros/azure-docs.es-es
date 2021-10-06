---
title: Módulos de Bicep
description: Describe cómo definir y utilizar un módulo, y cómo usar ámbitos de módulo.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/14/2021
ms.openlocfilehash: 53bc8d80f1954694b8bdb262cdec25bb4506b221
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672844"
---
# <a name="use-bicep-modules"></a>Uso de módulos de Bicep

Bicep permite dividir una solución compleja en módulos. Un módulo de Bicep es simplemente un archivo de Bicep que se implementa desde otro archivo de Bicep. Puede encapsular detalles complejos de la declaración de recursos en un módulo, lo que mejora la legibilidad de los archivos que usan el módulo. Puede volver a usar estos módulos y compartirlos con otras personas. Los módulos de Bicep se convierten en una sola plantilla de Azure Resource Manager con [plantillas anidadas](../templates/linked-templates.md#nested-template) para la implementación.

En este artículo se describe cómo definir y consumir módulos.

Para ver un tutorial, consulte [Implementación de recursos de Azure mediante plantillas de Bicep](/learn/modules/deploy-azure-resources-by-using-bicep-templates/).

## <a name="define-modules"></a>Definición de los módulos

Cada archivo de Bicep se puede usar como un módulo. Un módulo solo expone parámetros y salidas como un contrato a otros archivos de Bicep. Los parámetros y las salidas son opcionales.

El siguiente archivo de Bicep se puede implementar directamente para crear una cuenta de almacenamiento o usarse como un módulo.  En la sección siguiente se muestra cómo usar módulos:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

La salida se usa para pasar valores a los archivos de Bicep primarios.

## <a name="consume-modules"></a>Uso de módulos

Utilice la palabra clave _module_ para usar un módulo. El siguiente archivo de Bicep implementa el recurso definido en el archivo de módulo al que se hace referencia:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule 'storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **modulo**: palabra clave.
- **nombre simbólico** (stgModule): identificador del módulo.
- **archivo de módulo**: se debe hacer referencia a los archivos de módulo mediante rutas de acceso relativas. Todas las rutas de acceso de Bicep deben especificarse mediante el separador de directorios de barra diagonal (/), con el fin de garantizar una compilación coherente entre plataformas. El carácter de barra diagonal inversa de Windows (\\) no se admite. Las rutas de acceso pueden contener espacios.
- La propiedad **_name_** (storageDeploy) es necesaria cuando se utiliza un módulo. Cuando Bicep genera la plantilla IL, este campo se usa como el nombre del recurso de implementación anidado, que se genera para el módulo:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```
- La propiedad **_params_** contiene los parámetros que se pasan al archivo de módulo. Estos parámetros coinciden con los definidos en el archivo Bicep.

Al igual que los recursos, los módulos se implementan en paralelo a menos que dependan de otros módulos o implementaciones de recursos. Para obtener más información sobre las dependencias, vea [Establecer dependencias de recursos](resource-declaration.md#set-resource-dependencies).

Para obtener un valor de salida de un módulo, recupere el valor de la propiedad con la siguiente sintaxis: `stgModule.outputs.storageEndpoint`, donde `stgModule` es el identificador del módulo.

Puede implementar un módulo de manera condicional. Use la misma sintaxis **if** que utilizaría al [implementar un recurso de manera condicional](conditional-resource-deployment.md).

```bicep
param deployZone bool

module dnsZone 'dnszones.bicep' = if (deployZone) {
  name: 'myZoneModule'
}
```

Puede implementar un módulo varias veces mediante bucles. Para obtener más información, vea [Iteración de módulos en Bicep](loop-modules.md).

## <a name="configure-module-scopes"></a>Configuración de ámbitos de módulo

Al declarar un módulo, puede establecer un ámbito para el módulo que sea diferente del ámbito del archivo de Bicep que lo contiene. Use la propiedad `scope` para establecer el ámbito del módulo. Cuando no se proporciona la propiedad scope, el módulo se implementa en el ámbito de destino del elemento primario.

En el siguiente archivo de Bicep se muestra cómo crear un grupo de recursos e implementar un módulo en el grupo de recursos:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

En el ejemplo siguiente la implementación se realiza en grupos de recursos existentes.

```bicep
targetScope = 'subscription'

resource firstRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup1'
}

resource secondRG 'Microsoft.Resources/resourceGroups@2021-04-01' existing = {
  name: 'demogroup2'
}

module storage1 'storageAccount.bicep' = {
  name: 'westusdeploy'
  scope: firstRG
  params: {
    storagePrefix: 'stg1'
    location: 'westus'
  }
}

module storage2 'storageAccount.bicep' = {
  name: 'eastusdeploy'
  scope: secondRG
  params: {
    storagePrefix: 'stg2'
    location: 'eastus'
  }
}
```

La propiedad scope se debe establecer en un objeto de ámbito válido. Si el archivo Bicep implementa un grupo de recursos, una suscripción o un grupo de administración, puede establecer el ámbito de un módulo en el nombre simbólico de ese recurso. O bien, puede usar las funciones de ámbito para obtener un ámbito válido. 

Estas funciones son las siguientes:

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [suscripción](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [tenant](bicep-functions-scope.md#tenant)

En el ejemplo siguiente se usa la función `managementGroup` para establecer el ámbito.

```bicep
param managementGroupName string

module  'module.bicep' = {
  name: 'deployToMG'
  scope: managementGroup(managementGroupName)
}
```

## <a name="next-steps"></a>Pasos siguientes

- Para pasar un valor confidencial a un módulo, use la función [getSecret](bicep-functions-resource.md#getsecret).
- Puede implementar un módulo varias veces mediante bucles. Para obtener más información, vea [Iteración de módulos en Bicep](loop-modules.md).
