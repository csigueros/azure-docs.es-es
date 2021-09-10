---
title: Operadores de descriptores de acceso de Bicep
description: Describe el operador de acceso a recursos y el operador de acceso a propiedades de Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 08/30/2021
ms.openlocfilehash: b5eebb9b5dd6d39ae790b8fda7133e94ecd0cdb5
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224288"
---
# <a name="bicep-accessor-operators"></a>Operadores de descriptores de acceso de Bicep

Los operadores de descriptor de acceso se usan para acceder a recursos secundarios y propiedades en objetos, y a elementos de una matriz. También puede usar el descriptor de acceso de propiedades para usar algunas funciones.

| Operator | Nombre |
| ---- | ---- |
| `[]` | [Descriptor de acceso de índice](#index-accessor) |
| `.`  | [Descriptor de acceso de funciones](#function-accessor) |
| `::` | [Descriptor de acceso de recursos anidados](#nested-resource-accessor) |
| `.`  | [Descriptor de acceso de propiedades](#property-accessor) |

## <a name="index-accessor"></a>Descriptor de acceso de índice

`array[index]`

`object['index']`

Para obtener un elemento de una matriz, use `[index]` y proporcione un entero para el índice.

En el ejemplo siguiente se obtiene un elemento de una matriz.

```bicep
var arrayVar = [
  'Coho'
  'Contoso'
  'Fabrikan'
]

output accessorResult string = arrayVar[1]
``` 

Salida del ejemplo:

| Nombre | Tipo | Value |
| ---- | ---- | ---- |
| accessorResult | string | "Contoso" |

También puede usar el descriptor de acceso de índice para obtener una propiedad de objeto por nombre. Debe usar una cadena para el índice, no un entero. En el ejemplo siguiente se obtiene una propiedad en un objeto.

```bicep
var environmentSettings = {
  dev: {
    name: 'Development'
  }
  prod: {
    name: 'Production'
  }
}

output accessorResult string = environmentSettings['dev'].name
```

Salida del ejemplo:

| Nombre | Tipo | Value |
| ---- | ---- | ---- |
| accessorResult | string | "Development" |

## <a name="function-accessor"></a>Descriptor de acceso de funciones

`resourceName.functionName()`

Dos funciones, [getSecret](bicep-functions-resource.md#getsecret) y [list*](bicep-functions-resource.md#list), admiten el operador de descriptor de acceso para llamar a la función. Estas dos funciones son las únicas que admiten el operador de descriptor de acceso.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se hace referencia a un almacén de claves existente y, a continuación, se usa `getSecret` para pasar un secreto a un módulo.

```bicep
resource kv 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  name: kvName
  scope: resourceGroup(subscriptionId, kvResourceGroup )
}

module sql './sql.bicep' = {
  name: 'deploySQL'
  params: {
    sqlServerName: sqlServerName
    adminLogin: adminLogin
    adminPassword: kv.getSecret('vmAdminPassword')
  }
}
```

## <a name="nested-resource-accessor"></a>Descriptor de acceso de recursos anidados

`parentResource::nestedResource`

Un recurso anidado es un recurso que se declara dentro de otro recurso. Use el descriptor de acceso de recursos anidados `::` para acceder a esos recursos anidados desde fuera del recurso primario.

Dentro del recurso primario, se hace referencia al recurso anidado tan solo con el nombre simbólico. Solo tiene que usar el descriptor de acceso de recursos anidados al hacer referencia al recurso anidado desde fuera del recurso primario.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra cómo hacer referencia a un recurso anidado desde dentro del recurso primario y desde fuera del recurso primario.

```bicep
resource demoParent 'demo.Rp/parentType@2020-01-01' = {
  name: 'demoParent'
  location: 'West US'

  // Declare a nested resource within 'demoParent'
  resource demoNested 'childType' = {
    name: 'demoNested'
    properties: {
      displayName: 'The nested instance.'
    }
  }

  // Declare another nested resource
  resource demoSibling 'childType' = {
    name: 'demoSibling'
    properties: {
      // Use symbolic name to reference because this line is within demoParent
      displayName: 'Sibling of ${demoNested.properties.displayName}'
    }
  }
}

// Use nested accessor to reference because this line is outside of demoParent
output displayName string = demoParent::demoNested.properties.displayName
```

## <a name="property-accessor"></a>Descriptor de acceso de propiedades

`objectName.propertyName`

Use los descriptores de acceso de propiedades para acceder a las propiedades de un objeto. Los descriptores de acceso de propiedades se pueden usar con cualquier objeto, incluidos los parámetros y variables que son objetos. Se produce un error al usar el acceso a una propiedad en una expresión que no es un objeto.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra una variable de objeto y cómo acceder a las propiedades.

```bicep
var x = {
  y: {
    z: 'Hello'
    a: true
  }
  q: 42
}

output outputZ string = x.y.z
output outputQ int = x.q
```

Salida del ejemplo:

| Nombre | Tipo | Value |
| ---- | ---- | ---- |
| `outputZ` | string | 'Hello' |
| `outputQ` | integer | 42 |

Normalmente, se usa el descriptor de acceso de propiedades con un recurso implementado en el archivo de Bicep. En el ejemplo siguiente se crea una dirección IP pública y se usan los descriptores de acceso de propiedades para devolver un valor del recurso implementado.

```bicep
resource publicIp 'Microsoft.Network/publicIPAddresses@2020-06-01' = {
  name: publicIpResourceName
  location: location
  properties: {
    publicIPAllocationMethod: dynamicAllocation ? 'Dynamic' : 'Static'
    dnsSettings: {
      domainNameLabel: publicIpDnsLabel
    }
  }
}

// Use property accessor to get value
output ipFqdn string = publicIp.properties.dnsSettings.fqdn
```

## <a name="next-steps"></a>Pasos siguientes

- Si desea ejecutar los ejemplos, use la CLI de Azure o Azure PowerShell para [implementar un archivo de Bicep](./quickstart-create-bicep-use-visual-studio-code.md#deploy-the-bicep-file).
- Para crear un archivo de Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- Para obtener información sobre cómo resolver errores de tipos de Bicep, consulte [Función any para Bicep](./bicep-functions-any.md).
