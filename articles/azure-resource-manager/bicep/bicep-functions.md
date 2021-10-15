---
title: Funciones de Bicep
description: Describe las funciones que se van a usar en un archivo de Bicep para recuperar valores, trabajar con cadenas y valores numéricos y recuperar información de implementación.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 6fabd0bdbe6341382344c0e63ca711519275a3f4
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129351679"
---
# <a name="bicep-functions"></a>Funciones de Bicep

En este artículo se describen todas las funciones que puede usar en un archivo de Bicep. Puede encontrar una descripción de las secciones de un archivo de Bicep en [Nociones sobre la estructura y la sintaxis de los archivos de Bicep](./file.md).

La mayoría de las funciones operan de la misma forma cuando se implementan en un grupo de recursos, en una suscripción, en un grupo de administración o en un inquilino. Hay algunas funciones que no pueden utilizarse en todos los ámbitos. Se indican en las listas siguientes.

## <a name="namespaces-for-functions"></a>Espacios de nombres para funciones

Todas las funciones de Bicep se encuentran dentro de dos espacios de nombres: `az` y `sys`. Normalmente, no es necesario especificar el espacio de nombres cuando se usa la función. Especifique el espacio de nombres solo cuando el nombre de la función sea el mismo que otro elemento que haya definido en el archivo Bicep. Por ejemplo, si crea un parámetro denominado `range`, debe diferenciar la función `range` mediante la incorporación del espacio de nombres `sys`.

```bicep
// Parameter contains the same name as a function
param range int

// Must use sys namespace to call the function. 
// The second use of range refers to the parameter.
output result array = sys.range(1, range)
```

El espacio de nombres `az` contiene funciones específicas de una implementación de Azure. El espacio de nombres `sys` contiene funciones que se usan para construir valores. El espacio de nombres `sys` también incluye elementos decoradores para parámetros y bucles de recursos. Los espacios de nombres se anotan en este artículo.

## <a name="any-function"></a>Cualquier función

La [función any](./bicep-functions-any.md) está disponible en Bicep para ayudar a resolver problemas relacionados con las advertencias de tipos de datos. Esta función está en el espacio de nombres `sys`.

## <a name="array-functions"></a>Funciones de matriz

Las siguientes funciones están disponibles para trabajar con matrices. Todas estas funciones están en el espacio de nombres `sys`.

* [array](./bicep-functions-array.md#array)
* [concat](./bicep-functions-array.md#concat)
* [contains](./bicep-functions-array.md#contains)
* [empty](./bicep-functions-array.md#empty)
* [first](./bicep-functions-array.md#first)
* [intersection](./bicep-functions-array.md#intersection)
* [last](./bicep-functions-array.md#last)
* [length](./bicep-functions-array.md#length)
* [min](./bicep-functions-array.md#min)
* [max](./bicep-functions-array.md#max)
* [range](./bicep-functions-array.md#range)
* [skip](./bicep-functions-array.md#skip)
* [take](./bicep-functions-array.md#take)
* [union](./bicep-functions-array.md#union)

## <a name="date-functions"></a>Funciones de fecha

Las siguientes funciones están disponibles para trabajar con fechas. Todas estas funciones están en el espacio de nombres `sys`.

* [dateTimeAdd](./bicep-functions-date.md#datetimeadd)
* [utcNow](./bicep-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funciones con valores de implementación

Las siguientes funciones están disponibles para obtener valores relacionados con la implementación. Todas estas funciones están en el espacio de nombres `az`.

* [deployment](./bicep-functions-deployment.md#deployment)
* [environment](./bicep-functions-deployment.md#environment)

## <a name="file-functions"></a>Funciones de archivo

Las siguientes funciones están disponibles para cargar el contenido de archivos externos en el archivo Bicep. Todas estas funciones están en el espacio de nombres `sys`.

* [loadFileAsBase64](bicep-functions-files.md#loadfileasbase64)
* [loadTextContent](bicep-functions-files.md#loadtextcontent)

## <a name="logical-functions"></a>Funciones lógicas

La siguiente función está disponible para trabajar con condiciones lógicas. Esta función está en el espacio de nombres `sys`.

* [bool](./bicep-functions-logical.md#bool)

## <a name="numeric-functions"></a>Funciones numéricas

Las siguientes funciones están disponibles para trabajar con enteros. Todas estas funciones están en el espacio de nombres `sys`.

* [int](./bicep-functions-numeric.md#int)
* [min](./bicep-functions-numeric.md#min)
* [max](./bicep-functions-numeric.md#max)

## <a name="object-functions"></a>Funciones de objeto

Las siguientes funciones están disponibles para trabajar con objetos. Todas estas funciones están en el espacio de nombres `sys`.

* [contains](./bicep-functions-object.md#contains)
* [empty](./bicep-functions-object.md#empty)
* [intersection](./bicep-functions-object.md#intersection)
* [json](./bicep-functions-object.md#json)
* [length](./bicep-functions-object.md#length)
* [union](./bicep-functions-object.md#union)

## <a name="resource-functions"></a>Funciones de recursos

Las siguientes funciones están disponibles para obtener valores de recursos. La mayoría de estas funciones están en el espacio de nombres `az`. Las funciones list y la función getSecret se llaman directamente en el tipo de recurso, por lo que no tienen un calificador de espacio de nombres.

* [extensionResourceId](./bicep-functions-resource.md#extensionresourceid)
* [getSecret](./bicep-functions-resource.md#getsecret)
* [listAccountSas](./bicep-functions-resource.md#list)
* [listKeys](./bicep-functions-resource.md#listkeys)
* [listSecrets](./bicep-functions-resource.md#list)
* [list*](./bicep-functions-resource.md#list)
* [pickZones](./bicep-functions-resource.md#pickzones)
* [providers (en desuso)](./bicep-functions-resource.md#providers)
* [reference](./bicep-functions-resource.md#reference)
* [resourceId](./bicep-functions-resource.md#resourceid): puede utilizarse con cualquier ámbito, pero los valores correctos cambiarán en función de este.
* [subscriptionResourceId](./bicep-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](./bicep-functions-resource.md#tenantresourceid)

## <a name="scope-functions"></a>Funciones de Scope

Las siguientes funciones están disponibles para obtener valores de ámbito. Todas estas funciones están en el espacio de nombres `az`.

* [managementGroup](./bicep-functions-scope.md#managementgroup)
* [resourceGroup](./bicep-functions-scope.md#resourcegroup): solo puede utilizarse cuando la implementación se realiza en un grupo de recursos.
* [subscription](./bicep-functions-scope.md#subscription): solo puede utilizarse cuando la implementación se realiza en un grupo de recursos o una suscripción.
* [tenant](./bicep-functions-scope.md#tenant)

## <a name="string-functions"></a>Funciones de cadena

Bicep ofrece las siguientes funciones para trabajar con cadenas. Todas estas funciones están en el espacio de nombres `sys`.

* [base64](./bicep-functions-string.md#base64)
* [base64ToJson](./bicep-functions-string.md#base64tojson)
* [base64ToString](./bicep-functions-string.md#base64tostring)
* [concat](./bicep-functions-string.md#concat)
* [contains](./bicep-functions-string.md#contains)
* [dataUri](./bicep-functions-string.md#datauri)
* [dataUriToString](./bicep-functions-string.md#datauritostring)
* [empty](./bicep-functions-string.md#empty)
* [endsWith](./bicep-functions-string.md#endswith)
* [first](./bicep-functions-string.md#first)
* [format](./bicep-functions-string.md#format)
* [guid](./bicep-functions-string.md#guid)
* [indexOf](./bicep-functions-string.md#indexof)
* [last](./bicep-functions-string.md#last)
* [lastIndexOf](./bicep-functions-string.md#lastindexof)
* [length](./bicep-functions-string.md#length)
* [newGuid](./bicep-functions-string.md#newguid)
* [padLeft](./bicep-functions-string.md#padleft)
* [replace](./bicep-functions-string.md#replace)
* [skip](./bicep-functions-string.md#skip)
* [split](./bicep-functions-string.md#split)
* [startsWith](./bicep-functions-string.md#startswith)
* [string](./bicep-functions-string.md#string)
* [substring](./bicep-functions-string.md#substring)
* [take](./bicep-functions-string.md#take)
* [toLower](./bicep-functions-string.md#tolower)
* [toUpper](./bicep-functions-string.md#toupper)
* [trim](./bicep-functions-string.md#trim)
* [uniqueString](./bicep-functions-string.md#uniquestring)
* [uri](./bicep-functions-string.md#uri)
* [uriComponent](./bicep-functions-string.md#uricomponent)
* [uriComponentToString](./bicep-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Pasos siguientes

* Puede encontrar una descripción de las secciones de un archivo de Bicep en [Nociones sobre la estructura y la sintaxis de los archivos de Bicep](./file.md).
* Para iterar una cantidad de veces específica al crear un tipo de recurso, vea [Implementación de varias instancias de recursos en Bicep](./loop-resources.md).
* Para ver cómo implementar el archivo de Bicep que ha creado, consulte [Implementación de recursos con Bicep y Azure PowerShell](./deploy-powershell.md).
