---
title: Estructura y sintaxis de un archivo Bicep
description: Describe la estructura y las propiedades de un archivo Bicep mediante la sintaxis declarativa.
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 5401aebb0b0a82a04a2b78f9af1dc6d133915c0b
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2021
ms.locfileid: "113286635"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Nociones sobre la estructura y la sintaxis de los archivos Bicep

En este artículo se describe la estructura de un archivo Bicep. Presenta las distintas secciones del archivo y las propiedades que están disponibles en esas secciones.

Este artículo está dirigido a usuarios que ya están familiarizados con los archivos Bicep. Proporciona información detallada sobre la estructura del archivo de Bicep. Para obtener un tutorial paso a paso que le guía por el proceso de creación de un archivo de Bicep, consulte [Inicio rápido: Creación de plantillas de archivos de Bicep con Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).

## <a name="bicep-format"></a>Formato de Bicep

Un archivo Bicep contiene los elementos siguientes. Bicep es un lenguaje declarativo, lo que significa que los elementos pueden aparecer en cualquier orden.  A diferencia de los lenguajes imperativos, el orden de los elementos no afecta a cómo se procesa la implementación.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

// deploy to different scope
module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  scope: <scope-object>
  params: {
    <parameter-names-and-values>
  }
}

output <output-name> <output-data-type> = <output-value>

// iterative output
output <output-name> array = [for <item> in <collection>: {
  <output-properties>
}]
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

En un módulo, puede especificar un ámbito que sea diferente del ámbito para el resto del archivo de Bicep. Para más información, consulte [Configuración del ámbito de módulo](modules.md#configure-module-scopes).

## <a name="parameters"></a>Parámetros

Use los parámetros para los valores que deben variar en las distintas implementaciones. Puede definir un valor predeterminado para el parámetro que se usa si no se proporciona ningún valor durante la implementación.

Por ejemplo, puede agregar un parámetro de SKU para especificar tamaños diferentes para un recurso. Puede usar las funciones de Bicep para crear el valor predeterminado, como obtener la ubicación del grupo de recursos.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Para información sobre los tipos de datos disponibles, consulte [Tipos de datos en Bicep](data-types.md).

Un parámetro no puede tener el mismo nombre que una variable, un módulo o un recurso.

Para más información, consulte [Parámetros en Bicep](./parameters.md).

## <a name="parameter-decorators"></a>Decoradores de parámetro

Puede agregar uno o varios decoradores para cada parámetro. Estos decoradores definen los valores permitidos para el parámetro. En el ejemplo siguiente se especifican las SKU que se pueden implementar a través del archivo Bicep.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

En la tabla siguiente se describen los decoradores disponibles y cómo usarlos.

| Decorador | Aplicar a | Argumento | Descripción |
| --------- | ---- | ----------- | ------- |
| permitidas | all | array | Valores permitidos para el parámetro. Use este decorador para asegurarse de que el usuario proporciona los valores correctos. |
| description | all | string | Texto que explica cómo usar el parámetro. La descripción se muestra a los usuarios a través del portal. |
| maxLength | array, string | int | Longitud máxima de los parámetros de cadena y matriz. El valor es inclusivo. |
| maxValue | int | int | Valor máximo del parámetro entero. Este valor es inclusivo. |
| metadata | all | object | Propiedades personalizadas que se van a aplicar al parámetro. Pueden incluir una propiedad de descripción equivalente al decorador de la descripción. |
| minLength | array, string | int | Longitud mínima de los parámetros de cadena y matriz. El valor es inclusivo. |
| minValue | int | int | Valor mínimo del parámetro entero. Este valor es inclusivo. |
| secure | string, object | ninguno | Marca el parámetro como seguro. El valor de un parámetro seguro no se guarda en el historial de implementaciones y no se registra. Para más información, consulte [Protección de cadenas y objetos](data-types.md#secure-strings-and-objects). |

## <a name="variables"></a>variables

Utilice variables para expresiones complejas que se repiten en un archivo Bicep. Por ejemplo, puede agregar una variable para un nombre de recurso que se construye mediante la concatenación de varios valores.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

No se especifica un [tipo de datos](data-types.md) para una variable. En lugar de eso, el tipo de datos se infiere a partir del valor.

Una variable no puede tener el mismo nombre que un parámetro, módulo o recurso.

Para más información, consulte [Variables en Bicep](./variables.md).

## <a name="resource"></a>Recurso

Use la palabra clave `resource` para definir un recurso que se va a implementar. La declaración de recursos incluye un nombre simbólico para el recurso. Usará este nombre simbólico en otras partes del archivo Bicep si necesita obtener un valor del recurso.

La declaración de recursos también incluye el tipo de recurso y la versión de la API.

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

En la declaración de recursos, se incluyen las propiedades para el tipo de recurso. Estas propiedades son exclusivas de cada tipo de recurso.

Para más información, consulte [Declaración de recursos en Bicep](resource-declaration.md).

Para [implementar un recurso de manera condicional](conditional-resource-deployment.md), agregue una expresión `if`.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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

Para [implementar más de una instancia](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) de un tipo de recurso, agregue una expresión `for`. La expresión puede recorrer en iteración los miembros de una matriz.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

Un recurso no puede tener el mismo nombre que un parámetro, una variable o un módulo.

## <a name="modules"></a>Módulos

Use los módulos para crea un vínculo con los otros archivos Bicep que contienen el código que desea reutilizar. El módulo contiene uno o varios recursos que se van a implementar. Estos recursos se implementan junto con cualquier otro recurso en el archivo Bicep.

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

Un módulo no puede tener el mismo nombre que un parámetro, una variable o un recurso.

Al igual que ocurre con los recursos, puede implementar un módulo de manera condicional o mediante iteración. La sintaxis para los módulos es la misma que para los recursos.

Para más información, consulte [Uso de módulos de Bicep](./modules.md).

## <a name="resource-and-module-decorators"></a>Decoradores de recursos y módulos

Puede agregar un decorador a una definición de recurso o de módulo. El único decorador compatible es `batchSize(int)`. Solo puede aplicarlo a una definición de recurso o de módulo que usa una expresión `for`.

De manera predeterminada, los recursos se implementan en paralelo. No se sabe el orden en que finalizan. Cuando agrega el decorador `batchSize`, implementa las instancias en serie. Use el argumento entero para especificar el número de instancias que se van a implementar en paralelo.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Para más información, consulte [Implementación en lotes.](loop-resources.md#deploy-in-batches)

## <a name="outputs"></a>Salidas

Use las salidas para devolver un valor a partir de la implementación. Por lo general, se devuelve un valor desde un recurso implementado cuando es necesario volver a utilizar ese valor para otra operación.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Especifique un [tipo de datos](data-types.md) para el valor de salida.

Una salida puede tener el mismo nombre que un parámetro, una variable, un módulo o un recurso.

Para más información, consulte [Salidas en Bicep](./outputs.md).

## <a name="whitespace"></a>Espacio en blanco

Los espacios y las pestañas se omiten al crear archivos de Bicep. Sin embargo, las nuevas líneas tienen significado semántico, por ejemplo, en declaraciones de [objeto](./data-types.md#objects) y [matriz](./data-types.md#arrays).

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

## <a name="next-steps"></a>Pasos siguientes

Si necesita una introducción a Bicep, consulte [¿Qué es Bicep?](./overview.md) Para los tipos de datos de Bicep, consulte [Tipos de datos](./data-types.md).
