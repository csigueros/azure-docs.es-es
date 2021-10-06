---
title: Recursos secundarios en Bicep
description: Describe cómo establecer el nombre y el tipo de los recursos secundarios en Bicep.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/13/2021
ms.openlocfilehash: 2d928ec83559a1bd57adde3cbae98c589bb1cd15
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128622006"
---
# <a name="set-name-and-type-for-child-resources-in-bicep"></a>Establecimiento del nombre y el tipo de los recursos secundarios en Bicep

Los recursos secundarios son recursos que solo existen en el contexto de otro recurso. Por ejemplo, una [extensión de máquina virtual](/azure/templates/microsoft.compute/virtualmachines/extensions) no puede existir sin una [máquina virtual](/azure/templates/microsoft.compute/virtualmachines). El recurso de extensión es un elemento secundario de la máquina virtual.

Cada recurso primario solo acepta determinados tipos de recursos como recursos secundarios. La jerarquía de tipos de recursos está disponible en el artículo de referencia de [recursos de Bicep](/azure/templates/).

En este artículo se muestran distintas formas de declarar un recurso secundario.

### <a name="microsoft-learn"></a>Microsoft Learn

Para más información sobre los recursos secundarios y para obtener instrucciones prácticas, consulte el módulo [Implementación de recursos secundarios y de extensión mediante Bicep](/learn/modules/child-extension-bicep-templates) en **Microsoft Learn.**

## <a name="name-and-type-pattern"></a>Patrón de nombre y de tipo

En Bicep, puede especificar el recurso secundario dentro del recurso primario o fuera de él. Los valores proporcionados para el nombre y el tipo de recurso varían en función de cómo se declara el recurso secundario. Pero el nombre completo y el tipo siempre se resuelven con el mismo patrón. 

El **nombre completo** del recurso secundario usa el patrón:

```bicep
{parent-resource-name}/{child-resource-name}
```

Si tiene más de dos niveles en la jerarquía, siga repitiendo nombres de recursos primarios:

```bicep
{parent-resource-name}/{child-level1-resource-name}/{child-level2-resource-name}
```

El **tipo completo** del recurso secundario usa el patrón:

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}
```

Si tiene más de dos niveles en la jerarquía, siga repitiendo tipos de recursos primarios:

```bicep
{resource-provider-namespace}/{parent-resource-type}/{child-level1-resource-type}/{child-level2-resource-type}
```

Si cuenta los segmentos entre los caracteres `/`, el número de segmentos del tipo siempre es uno más que el número de segmentos del nombre. 

## <a name="within-parent-resource"></a>Dentro del recurso primario

En el ejemplo siguiente se muestra el recurso secundario incluido en la propiedad resources del recurso primario.

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  <parent-resource-properties>

  resource <child-resource-symbolic-name> '<child-resource-type>' = {
    <child-resource-properties>
  }
}
```

Debe aparecer una declaración de recursos anidada en el nivel superior de sintaxis del recurso primario. Las declaraciones pueden anidarse a una profundidad arbitraria, siempre y cuando cada nivel sea un tipo secundario de su recurso primario.

Cuando el recurso secundario se define dentro del tipo de recurso primario, los valores type y name se indican como un solo segmento sin barras diagonales. En el ejemplo siguiente se muestra una cuenta de almacenamiento con un recurso secundario para el servicio de archivos, y el servicio de archivos tiene un recurso secundario para el recurso compartido de archivos. El nombre del servicio de archivos se establece en `default` y su tipo en `fileServices`. El nombre del recurso compartido de archivos se establece en `exampleshare` y su tipo en `shares`.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/insidedeclaration.bicep" highlight="9,12":::

Los tipos de recurso completos siguen siendo `Microsoft.Storage/storageAccounts/fileServices` y `Microsoft.Storage/storageAccounts/fileServices/shares`. No se proporciona `Microsoft.Storage/storageAccounts/` porque se supone que viene del tipo y versión del recurso primario. El recurso anidado puede declarar opcionalmente una versión de API mediante la sintaxis `<segment>@<version>`. Si el recurso anidado omite la versión de API, se usa la versión de API del recurso primario. Si el recurso anidado especifica una versión de API, se usa la versión de API especificada.

Los nombres de recursos secundarios se establecen en `default` y `exampleshare`, pero los nombres completos incluyen los nombres primarios. No se proporciona `examplestorage` ni `default` porque se asume a partir del recurso primario.

Un recurso anidado puede acceder a las propiedades de su recurso primario. Otros recursos declarados dentro del cuerpo del mismo recurso primario pueden hacerse referencia entre sí mediante los nombres simbólicos. Es posible que un recurso primario no tenga acceso a las propiedades de los recursos que contiene. Este intento provocaría una dependencia cíclica.

Para hacer referencia a un recurso anidado fuera del recurso primario, debe completarse con el nombre del recurso contenedor y el operador `::`. Por ejemplo, para generar una propiedad desde un recurso secundario:

```bicep
output childAddressPrefix string = VNet1::VNet1_Subnet1.properties.addressPrefix
```

## <a name="outside-parent-resource"></a>Fuera del recurso primario

En el ejemplo siguiente se muestra el recurso secundario fuera del recurso primario. Este enfoque puede usarse si el recurso primario no está implementado en la misma plantilla o si quiere usar [un bucle](loop-resources.md) para crear más de un recurso secundario. Especifique la propiedad primaria en el elemento secundario con el valor establecido en el nombre simbólico del elemento primario. Con esta sintaxis, todavía debe declarar el tipo de recurso completo, pero el nombre del recurso secundario es solo el nombre del elemento secundario.

```bicep
resource <parent-resource-symbolic-name> '<resource-type>@<api-version>' = {
  name: 'myParent'
  <parent-resource-properties>
}

resource <child-resource-symbolic-name> '<child-resource-type>@<api-version>' = {
  parent: <parent-resource-symbolic-name>
  name: 'myChild'
  <child-resource-properties>
}
```

Cuando el recurso secundario se define fuera del recurso primario, los valores type y name se indican con barras diagonales para incluir el tipo y el nombre primarios.

En el ejemplo siguiente se muestra una cuenta de almacenamiento, un servicio de archivos y un recurso compartido de archivos que se definen en el nivel raíz.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/outsidedeclaration.bicep" highlight="10,12,15,17":::

Hacer referencia al nombre simbólico del recurso secundario funciona igual que hacer referencia al elemento primario.

## <a name="full-resource-name-outside-parent"></a>Nombre completo del recurso fuera del elemento primario

También se puede usar el nombre completo y el tipo completo del recurso al declarar el recurso secundario fuera del elemento primario. No se debe establecer la propiedad primaria en el recurso secundario. Dado que la dependencia no se puede inferir, se debe establecer explícitamente.

:::code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/child-resource-name-type/fullnamedeclaration.bicep" highlight="10,11,17,18":::

> [!IMPORTANT]
> No se recomienda establecer el nombre completo y el tipo completo del recurso. No es tan seguro como usar cualquiera de los otros enfoques.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre cómo crear archivos de Bicep, consulte [Nociones sobre la estructura y la sintaxis de los archivos de Bicep](./file.md).
* Para información sobre el formato del nombre del recurso al hacer referencia a él, consulte la [función reference](./bicep-functions-resource.md#reference).
