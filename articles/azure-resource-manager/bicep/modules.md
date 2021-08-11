---
title: Módulos de Bicep
description: Describe cómo definir y utilizar un módulo, y cómo usar ámbitos de módulo.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 06/03/2021
ms.openlocfilehash: 85f345cfd7085f34f28e4b219c4f379abff74bff
ms.sourcegitcommit: ef950cf37f65ea7a0f583e246cfbf13f1913eb12
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111421425"
---
# <a name="use-bicep-modules"></a>Uso de módulos de Bicep

Bicep permite dividir una solución compleja en módulos. Un módulo de Bicep es un conjunto de uno o más recursos que se van a implementar juntos. Los módulos abstraen los detalles complejos de la declaración de recursos sin procesar, lo que puede aumentar la legibilidad. Puede volver a usar estos módulos y compartirlos con otras personas. Los módulos de Bicep se transpilan en una sola plantilla de ARM con [plantillas anidadas](../templates/linked-templates.md#nested-template) para su implementación.

Para ver un tutorial, consulte [Implementación de recursos de Azure mediante plantillas de Bicep](/learn/modules/deploy-azure-resources-by-using-bicep-templates/).

## <a name="define-modules"></a>Definición de los módulos

Cada archivo de Bicep puede usarse como un módulo. Un módulo solo expone parámetros y salidas como un contrato a otros archivos de Bicep. Tanto los parámetros como las salidas son opcionales.

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

module stgModule './storageAccount.bicep' = {
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

Para obtener un valor de salida de un módulo, recupere el valor de la propiedad con la siguiente sintaxis: `stgModule.outputs.storageEndpoint`, donde `stgModule` es el identificador del módulo.

## <a name="configure-module-scopes"></a>Configuración de ámbitos de módulo

Al declarar un módulo, puede proporcionar una propiedad _scope_ para establecer el ámbito en el que se implementará el módulo:

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

Se puede omitir la propiedad _scope_ cuando el ámbito de destino del módulo y el ámbito de destino del elemento primario sean el mismo. Cuando no se proporciona la propiedad scope, el módulo se implementa en el ámbito de destino del elemento primario.

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

La propiedad scope se debe establecer en un objeto de ámbito válido. Si el archivo Bicep implementa un grupo de recursos, una suscripción o un grupo de administración, puede establecer el ámbito de un módulo en el nombre simbólico de ese recurso. Este enfoque se muestra en el ejemplo anterior, donde se crea un grupo de recursos y se usa para el ámbito de un módulo.

O bien, puede usar las funciones de ámbito para obtener un ámbito válido. Estas funciones son las siguientes:

- [resourceGroup](bicep-functions-scope.md#resourcegroup)
- [suscripción](bicep-functions-scope.md#subscription)
- [managementGroup](bicep-functions-scope.md#managementgroup)
- [tenant](bicep-functions-scope.md#tenant)

## <a name="next-steps"></a>Pasos siguientes

- Para ver un tutorial, consulte [Implementación de recursos de Azure mediante plantillas de Bicep](/learn/modules/deploy-azure-resources-by-using-bicep-templates/).
