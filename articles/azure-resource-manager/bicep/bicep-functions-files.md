---
title: 'Funciones de Bicep: archivos'
description: Aquí se describen las funciones que se usan en un archivo Bicep para cargar contenido desde un archivo.
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 28b28086986eb4e871cc6ed8c315c3802e721840
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359845"
---
# <a name="file-functions-for-bicep"></a>Funciones de archivo de Bicep

En este artículo se describen las funciones de Bicep para cargar contenido desde archivos externos.

## <a name="loadfileasbase64"></a>loadFileAsBase64

`loadFileAsBase64(filePath)`

Carga el archivo como una cadena base64.

Espacio de nombres: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| filePath | Sí | string | Ruta de acceso al archivo que se va a cargar. La ruta de acceso es relativa con respecto al archivo Bicep implementado. |

### <a name="remarks"></a>Comentarios

Use esta función si tiene contenido binario que quiere incluir en la implementación. En lugar de codificar manualmente el archivo en una cadena base64 y agregarlo al archivo Bicep, cárguelo con esta función. El archivo se carga cuando el archivo Bicep se compila en una plantilla JSON. Durante la implementación, la plantilla JSON dispone del contenido del archivo como una cadena codificada de forma rígida.

Esta función requiere **Bicep, versión 0.4.412 o posterior**. 

El tamaño máximo permitido del archivo es de **96 Kb**.

### <a name="return-value"></a>Valor devuelto

El archivo como una cadena base64.

## <a name="loadtextcontent"></a>loadTextContent

`loadTextContent(filePath, [encoding])`

Carga el contenido del archivo especificado como una cadena. 

Espacio de nombres: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | Tipo | Descripción |
|:--- |:--- |:--- |:--- |
| filePath | Sí | string | Ruta de acceso al archivo que se va a cargar. La ruta de acceso es relativa con respecto al archivo Bicep implementado. |
| encoding | No | string | Codificación del archivo. El valor predeterminado es `utf-8`. Las opciones disponibles son: `iso-8859-1`, `us-ascii`, `utf-16`, `utf-16BE` o `utf-8`.  |

### <a name="remarks"></a>Comentarios

Use esta función si tiene contenido que se almacene más en un archivo independiente. En lugar de duplicar el contenido del archivo Bicep, cárguelo con esta función. Por ejemplo, puede cargar un script de implementación desde un archivo. El archivo se carga cuando el archivo Bicep se compila en la plantilla JSON. Durante la implementación, la plantilla JSON dispone del contenido del archivo como una cadena codificada de forma rígida.

Al cargar un archivo JSON, puede usar la función [json](bicep-functions-object.md#json) con la función loadTextContent para crear un objeto JSON. En VS Code, las propiedades del objeto cargado están disponibles en IntelliSense. Por ejemplo, puede crear un archivo con valores para compartirlo entre muchos archivos Bicep. En este artículo se muestra un ejemplo.

Esta función requiere **Bicep, versión 0.4.412 o posterior**.

El tamaño máximo permitido del archivo es de **131 072 caracteres**, incluidos los finales de línea.

### <a name="return-value"></a>Valor devuelto

Contenido del archivo como una cadena.

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se carga un script desde un archivo y se usa para un script de implementación.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loaddeploymentscript.bicep" highlight="13" :::

En el ejemplo siguiente se crea un archivo JSON que contiene los valores que se quieren usar para un grupo de seguridad de red.

::: code language="json" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/nsg-security-rules.json" :::

Cargue ese archivo y conviértalo en un objeto JSON. Use el objeto para asignar valores al recurso.

::: code language="bicep" source="~/azure-docs-bicep-samples/syntax-samples/functions/loadTextContent/loadsharedrules.bicep" highlight="3,13-21" :::

Puede reutilizar el archivo de valores en otros archivos Bicep que implementen un grupo de seguridad de red.

## <a name="next-steps"></a>Pasos siguientes

* Puede encontrar una descripción de las secciones de un archivo de Bicep en [Nociones sobre la estructura y la sintaxis de los archivos de Bicep](./file.md).
