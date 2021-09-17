---
title: Patrón de archivo de variables compartido
description: Se describe el patrón de archivo de variables compartido.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: 812cb0b861418d3bd3d13959cf074442d9a81538
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446742"
---
# <a name="shared-variable-file-pattern"></a>Patrón de archivo de variables compartido

Reduzca la repetición de valores compartidos en los archivos Bicep. En su lugar, cargue esos valores desde un archivo JSON compartido dentro del archivo Bicep. Al usar matrices, concatene los valores compartidos con valores específicos de la implementación en el código Bicep.

## <a name="context-and-problem"></a>Contexto y problema

Al escribir el código de Bicep, es posible que tenga variables comunes que se reutilizan en un conjunto de archivos Bicep. Puede duplicar los valores cada vez que declara el recurso, por ejemplo, copiando y pegando los valores entre los archivos Bicep. Sin embargo, este enfoque es propenso a errores y, cuando necesite realizar cambios, deberá actualizar cada definición de recurso para mantenerla sincronizada con las demás.

Además, al trabajar con variables definidas como matrices, es posible que tenga un conjunto de valores comunes en varios archivos Bicep y que también necesite agregar valores específicos para el recurso que va a implementar. Cuando se mezclan las variables compartidas con las variables específicas del recurso, es más difícil que alguien comprenda la distinción entre las dos categorías de variables.

## <a name="solution"></a>Solución

Cree un archivo JSON que incluya las variables que necesita compartir. Use las funciones `json()` y `loadTextContent()` de Bicep para cargar el archivo y acceder a las variables. Para las variables de matriz, use la función `concat()` para combinar los valores compartidos con cualquier valor personalizado para el recurso específico.

## <a name="example-1-naming-prefixes"></a>Ejemplo 1: nomenclatura de prefijos

Supongamos que tiene varios archivos Bicep que definen recursos. Debe usar un prefijo de nomenclatura coherente para todos los recursos.

Defina un archivo JSON que incluya los prefijos de nomenclatura comunes que se aplican en toda la empresa:

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-prefixes.json" :::

En el archivo Bicep, declare una variable que importe los prefijos de nomenclatura compartidos:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="1" :::

Al definir los nombres de recursos, use la interpolación de cadenas para concatenar los prefijos de nombre compartido con sufijos de nombre únicos:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-1.bicep" range="3-4" :::

## <a name="example-2-network-security-group-rules"></a>Ejemplo 2: reglas del grupo de seguridad de red

Supongamos que tiene varios archivos Bicep que definen sus propios grupos de seguridad de red (NSG). Tiene un conjunto común de reglas de seguridad que se deben aplicar a cada grupo de seguridad de red y, a continuación, tiene reglas específicas de la aplicación que se deben agregar.

Defina un archivo JSON que incluya las reglas de seguridad comunes que se aplican en toda la empresa:

::: code language="json" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/shared-rules.json" :::

En el archivo Bicep, declare una variable que importe las reglas de seguridad compartidas:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="5" :::

Cree una matriz de variables que represente las reglas personalizadas para este NSG específico:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="6-21" :::

Defina el recurso de NSG. Use la función `concat()` para combinar las dos matrices y establecer la propiedad `securityRules`:

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/patterns-shared-variable-file/example-2.bicep" range="23-29" highlight="5" :::

## <a name="considerations"></a>Consideraciones

- Al usar este enfoque, el archivo JSON se incluirá dentro de la plantilla de ARM generada por Bicep. Las plantillas de ARM JSON generadas por Bicep tienen un límite de archivos de 4 MB, por lo que es importante evitar el uso de archivos de variables compartidas de gran tamaño.
- Asegúrese de que las matrices de variables compartidas no entren en conflicto con los valores de matriz especificados en cada archivo Bicep. Por ejemplo, al usar el patrón de conjunto de configuración para definir grupos de seguridad de red, asegúrese de que no tiene varias reglas que definan la misma prioridad y dirección.

## <a name="next-steps"></a>Pasos siguientes

[Más información acerca del patrón de conjunto de configuración](patterns-configuration-set.md).
