---
title: Estructura y sintaxis de un archivo Bicep
description: Describe la estructura y las propiedades de un archivo Bicep mediante la sintaxis declarativa.
ms.topic: conceptual
ms.date: 11/12/2021
ms.openlocfilehash: 352ff708b9b36eff06be8f3a3dda10b28b02e37b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493990"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Nociones sobre la estructura y la sintaxis de los archivos Bicep

En este artículo se describen la estructura y la sintaxis de un archivo Bicep. Presenta las distintas secciones del archivo y las propiedades que están disponibles en esas secciones.

Para obtener un tutorial paso a paso que le guía por el proceso de creación de un archivo de Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

## <a name="bicep-format"></a>Formato de Bicep

Bicep es un lenguaje declarativo, lo que significa que los elementos pueden aparecer en cualquier orden. A diferencia de los lenguajes imperativos, el orden de los elementos no afecta a cómo se procesa la implementación.

Un archivo Bicep contiene los elementos siguientes.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>
```

En el ejemplo siguiente se muestra una implementación de estos elementos.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

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

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Ámbito de destino

De manera predeterminada, el ámbito de destino está establecido en `resourceGroup`. Si va a realizar la implementación en el nivel de grupo de recursos, no es necesario establecer el ámbito de destino en el archivo Bicep.

Los valores permitidos son:

* **resourceGroup**: valor predeterminado que se usa para las [implementaciones de grupos de recursos](deploy-to-resource-group.md).
* **subscription**: se usa para las [implementaciones de suscripciones](deploy-to-subscription.md).
* **managementGroup**: se usa para las [implementaciones de grupos de administración](deploy-to-management-group.md).
* **tenant**: se usa para las [implementaciones de inquilinos](deploy-to-tenant.md).

En un módulo, puede especificar un ámbito que sea diferente del ámbito para el resto del archivo de Bicep. Para más información, consulte [Configuración del ámbito de módulo](modules.md#set-module-scope).

## <a name="parameters"></a>Parámetros

Use los parámetros para los valores que deben variar en las distintas implementaciones. Puede definir un valor predeterminado para el parámetro que se usa si no se proporciona ningún valor durante la implementación.

Por ejemplo, puede agregar un parámetro de SKU para especificar diferentes tamaños para un recurso. Puede pasar valores diferentes en función de si va a realizar la implementación en prueba o en producción.

```bicep
param storageSKU string = 'Standard_LRS'
```

El parámetro está disponible para su uso en el archivo Bicep.

```bicep
sku: {
  name: storageSKU
}
```

Para más información, consulte [Parámetros en Bicep](./parameters.md).

## <a name="parameter-decorators"></a>Decoradores de parámetro

Puede agregar uno o varios decoradores para cada parámetro. Estos decoradores describen el parámetro y definen restricciones para los valores que se pasan. En el ejemplo siguiente se muestra un decorador, pero hay muchos otros disponibles.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

Para más información, incluidas las descripciones de todos los decoradores disponibles, consulte [Elementos Decorator](parameters.md#decorators).

## <a name="variables"></a>Variables

Puede hacer que el archivo Bicep sea más legible encapsulando expresiones complejas en una variable. Por ejemplo, puede agregar una variable para un nombre de recurso que se construye mediante la concatenación de varios valores.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Aplique esta variable siempre que necesite la expresión compleja.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
```

Para más información, consulte [Variables en Bicep](./variables.md).

## <a name="resource"></a>Recurso

Use la palabra clave `resource` para definir un recurso que se va a implementar. La declaración de recursos incluye un nombre simbólico para el recurso. Va a utilizar este nombre simbólico en otras partes del archivo Bicep para obtener un valor del recurso.

La declaración de recursos incluye el tipo de recurso y la versión de la API. Dentro del cuerpo de la declaración de recurso, incluya propiedades específicas del tipo de recurso.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
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
```

Para más información, consulte [Declaración de recursos en Bicep](resource-declaration.md).

## <a name="modules"></a>Módulos

Los módulos permiten reutilizar el código de un archivo Bicep en otros archivos Bicep. En la declaración del módulo, se vincula al archivo que va a reutilizar. Al implementar el archivo Bicep, también se implementan los recursos del módulo.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

El nombre simbólico le permite hacer referencia al módulo desde otro lugar del archivo. Por ejemplo, puede obtener un valor de salida de un módulo mediante el uso del nombre simbólico y el nombre del valor de salida.

Para más información, consulte [Uso de módulos de Bicep](./modules.md).

## <a name="resource-and-module-decorators"></a>Decoradores de recursos y módulos

Puede agregar un decorador a una definición de recurso o de módulo. El único decorador compatible es `batchSize(int)`. Solo puede aplicarlo a una definición de recurso o de módulo que usa una expresión `for`.

De manera predeterminada, los recursos se implementan en paralelo. Cuando agrega el decorador `batchSize`, implementa las instancias en serie.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Para más información, consulte [Implementación en lotes.](loops.md#deploy-in-batches)

## <a name="outputs"></a>Salidas

Use las salidas para devolver unos valores a partir de la implementación. Por lo general, se devuelve un valor desde un recurso implementado cuando es necesario volver a utilizar ese valor para otra operación.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Para más información, consulte [Salidas en Bicep](./outputs.md).

## <a name="loops"></a>Bucles

Puede agregar bucles iterativos al archivo Bicep para definir varias copias de lo siguiente:

* resource
* module
* variable
* propiedad
* output

Utilice la expresión `for` para definir un bucle.

```bicep
param moduleCount int = 2

module stgModule './example.bicep' = [for i in range(0, moduleCount): {
  name: '${i}deployModule'
  params: {
  }
}]
```

Puede recorrer en iteración una matriz, un objeto o un índice de enteros.

Para más información, consulte [Bucles iterativos en Bicep](loops.md).

## <a name="conditional-deployment"></a>Implementación condicional

Puede agregar un recurso o módulo al archivo Bicep, que se implementa condicionalmente. Durante la implementación, se evalúa la condición, y el resultado determina si se implementa el recurso o el módulo. Utilice la expresión `if` para definir una implementación condicional.

```bicep
param deployZone bool

resource dnsZone 'Microsoft.Network/dnszones@2018-05-01' = if (deployZone) {
  name: 'myZone'
  location: 'global'
}
```

Para obtener más información, consulte [Implementación condicional en Bicep](conditional-resource-deployment.md).

## <a name="whitespace"></a>Espacio en blanco

Los espacios y las pestañas se omiten al crear archivos de Bicep.

Bicep diferencia las nuevas líneas. Por ejemplo:

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
  ...
}
```

No se puede escribir como:

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' =
    if (newOrExisting == 'new') {
      ...
    }
```

Defina [objetos](./data-types.md#objects) y [matrices](./data-types.md#arrays) en varias líneas.

## <a name="comments"></a>Comentarios

Use `//` para los comentarios de una sola línea o `/* ... */` para los comentarios de varias líneas.

En el ejemplo siguiente, se muestra un comentario de una sola línea.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

En el ejemplo siguiente, se muestra un comentario de varias líneas.

```bicep
/*
  This Bicep file assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Cadenas de varias líneas

Una cadena se puede dividir en varias líneas. Use tres caracteres de comillas simples `'''` para iniciar y finalizar la cadena multilínea.

Los caracteres de la cadena multilínea se controlan tal cual. No se necesitan caracteres de escape. No se puede incluir `'''` en la cadena multilínea. Actualmente, no se admite la interpolación de cadenas.

Puede iniciar la cadena justo después de las `'''` de apertura, o bien incluir una línea nueva. Cualquiera sea el caso, la cadena resultante no incluye una línea nueva. En función de cómo finalizan las líneas en el archivo Bicep, las líneas nuevas se interpretan como `\r\n` o `\n`.

En el ejemplo siguiente, se muestra una cadena multilínea.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

El ejemplo anterior es equivalente al JSON siguiente.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="known-limitations"></a>Restricciones conocidas

- No se admite el concepto de apiProfile, que se usa para asignar un único apiProfile a un valor de apiVersion establecido para cada tipo de recurso.
- No se admiten las funciones definidas por el usuario.

## <a name="next-steps"></a>Pasos siguientes

Si necesita una introducción a Bicep, consulte [¿Qué es Bicep?](./overview.md) Para los tipos de datos de Bicep, consulte [Tipos de datos](./data-types.md).
