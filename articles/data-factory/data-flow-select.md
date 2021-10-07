---
title: Transformación Selección en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre la transformación Selección en el flujo de datos de asignación de canalizaciones de Azure Data Factory y Synapse Analytics.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 47965d920738a8b0a20ac33446224af84041aa0c
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061625"
---
# <a name="select-transformation-in-mapping-data-flow"></a>Transformación Selección en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Use la transformación Selección para cambiar el nombre de las columnas, quitarlas o reordenarlas. Esta transformación no modifica los datos de fila, pero elige qué columnas se propagan hacia abajo. 

En una transformación Selección, los usuarios pueden especificar asignaciones fijas, usar patrones para realizar la asignación basada en reglas o habilitar la asignación automática. Las asignaciones fijas y basadas en reglas se pueden usar en la misma transformación Selección. Si una columna no coincide con una de las asignaciones definidas, se quitará.

## <a name="fixed-mapping"></a>Asignación fija

Si hay menos de 50 columnas definidas en la proyección, todas las columnas definidas tendrán una asignación fija de forma predeterminada. Una asignación fija toma una columna de entrada definida y le asigna un nombre exacto.

:::image type="content" source="media/data-flow/fixedmapping.png" alt-text="Asignación fija":::

> [!NOTE]
> No puede asignar una columna desfasada ni cambiarle el nombre mediante una asignación fija

### <a name="mapping-hierarchical-columns"></a>Asignación de columnas jerárquicas

Las asignaciones fijas se pueden usar para asignar una subcolumna de una columna jerárquica a una columna de nivel superior. Si tiene una jerarquía definida, use la lista desplegable de columnas para seleccionar una subcolumna. La transformación Selección creará una columna nueva con el valor y el tipo de datos de la subcolumna.

:::image type="content" source="media/data-flow/select-hierarchy.png" alt-text="Asignación jerárquica":::

## <a name="rule-based-mapping"></a>Asignación basada en reglas


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

Si desea asignar muchas columnas a la vez o pasar las columnas desfasadas a un nivel inferior, use la asignación basada en reglas para definir las asignaciones mediante patrones de columna. Coincidencia basada en los valores `name`, `type`, `stream` y `position` de las columnas. Puede usar cualquier combinación de asignaciones basadas en reglas y fijas. De forma predeterminada, todas las proyecciones con más de 50 columnas tendrán como valor predeterminado una asignación basada en reglas que coincida con todas las columnas y que genere el nombre insertado. 

Para agregar una asignación basada en reglas, haga clic en **Agregar asignación** y seleccione **Rule based mapping** (Asignación basada en reglas).

:::image type="content" source="media/data-flow/rule2.png" alt-text="Captura de pantalla que muestra la asignación basada en reglas seleccionada en Agregar asignación.":::

Cada asignación basada en reglas requiere dos entradas: la condición por la que buscar coincidencias y el nombre de cada columna asignada. Ambos valores se insertaron a través del [generador de expresiones](concepts-data-flow-expression-builder.md). En el cuadro de expresión de la izquierda, escriba la condición de coincidencia booleana. En el cuadro de expresión de la derecha, especifique a qué se asignará la columna coincidente.

:::image type="content" source="media/data-flow/rule-based-mapping.png" alt-text="Captura de pantalla que muestra una asignación.":::

Use la sintaxis de `$$` para hacer referencia al nombre de entrada de una columna coincidente. Utilizando la imagen anterior como ejemplo, supongamos que un usuario desea buscar coincidencias en todas las columnas de cadena cuyos nombres tengan más de 6 caracteres. Si una columna de entrada se denomina `test`, la expresión `$$ + '_short'` cambiará el nombre de la columna `test_short`. Si esta es la única asignación que existe, todas las columnas que no cumplan la condición se quitarán de los datos de salida.

Los patrones coinciden con las columnas desfasadas y definidas. Para ver qué columnas definidas están asignadas mediante una regla, haga clic en el icono de las gafas junto a la regla. Compruebe la salida mediante la vista previa de los datos.

### <a name="regex-mapping"></a>Asignación de expresión regular

Si hace clic en el icono del botón de contenido adicional hacia abajo, puede especificar una condición de asignación de regex. Una condición de asignación de regex coincide con todos los nombres de columna que coinciden con la condición regex especificada. Se puede usar en combinación con las asignaciones estándar basadas en reglas.

:::image type="content" source="media/data-flow/regex-matching.png" alt-text="Captura de pantalla que muestra la condición de asignación de regex con Hierarchy level (Nivel de jerarquía) y Name matches (Coincidencias de nombres).":::

El ejemplo anterior coincide con el patrón regex `(r)` o cualquier nombre de columna que contenga un "r" en minúscula. De forma similar a la asignación basada en reglas estándar, todas las columnas coincidentes se modifican por la condición de la derecha con `$$` sintaxis.

Si tiene varias coincidencias de expresiones regulares en el nombre de columna, puede hacer referencia a coincidencias específicas mediante `$n`, donde "n" hace referencia a la coincidencia. Por ejemplo, "$2" hace referencia a la segunda coincidencia de un nombre de columna.

### <a name="rule-based-hierarchies"></a>Jerarquías basadas en reglas

Si la proyección definida tiene una jerarquía, puede usar la asignación basada en reglas para asignar las subcolumnas de las jerarquías. Especifique una condición de coincidencia y la columna compleja cuyas subcolumnas desee asignar. Todas las subcolumnas coincidentes se enviarán con la regla para asignar un nombre de salida especificada a la derecha.

:::image type="content" source="media/data-flow/rule-based-hierarchy.png" alt-text="Captura de pantalla que muestra una asignación basada en reglas usando una jerarquía.":::

En el ejemplo anterior se hace coincidir con todas las subcolumnas de la columna compleja `a`. `a` contiene dos subcolumnas `b` y `c`. El esquema de salida incluirá dos columnas `b` y `c`, ya que la condición para asignar un nombre de salida es `$$`.

### <a name="parameterization"></a>Parametrización

Puede parametrizar los nombres de columna mediante la asignación basada en reglas. Use la palabra clave ```name``` para hacer coincidir los nombres de columna de entrada con un parámetro. Por ejemplo, si tiene un parámetro de flujo de datos ```mycolumn```, puede crear una regla que coincida con cualquier nombre de columna que sea igual a ```mycolumn```. Puede cambiar el nombre de la columna coincidente por una cadena codificada de forma rígida, como "clave empresarial", y hacer referencia a esta explícitamente. En este ejemplo, la condición de coincidencia es ```name == $mycolumn``` y la condición de nombre es "clave empresarial". 

## <a name="auto-mapping"></a>Asignación automática

Al agregar una transformación Selección, para habilitar la opción **Asignación automática** se puede cambiar el control deslizante de Asignación automática. Con la asignación automática, la transformación Selección asigna todas las columnas de entrada, excepto las duplicadas, con el mismo nombre que su entrada. Se incluirán las columnas desfasadas, lo que significa que los datos de salida pueden contener columnas no definidas en el esquema. Para obtener más información sobre las columnas desfasadas, consulte [Desfase de esquema](concepts-data-flow-schema-drift.md).

:::image type="content" source="media/data-flow/automap.png" alt-text="Asignación automática":::

Con la asignación automática activada, la transformación Selección respetará la configuración para omitir duplicados y proporcionará un nuevo alias para las columnas existentes. La asignación de alias es útil cuando se realizan varias combinaciones o búsquedas en el mismo flujo y en escenarios de autocombinación. 

## <a name="duplicate-columns"></a>Columnas duplicadas

De forma predeterminada, la transformación Selección quita las columnas duplicadas en ambas proyecciones de entrada y de salida. Las columnas de entrada duplicadas suelen provenir de las transformaciones Combinación y Búsqueda, en las que los nombres de columna se duplican en cada lado de la combinación. Pueden producirse columnas de salida duplicadas si se asignan dos columnas de entrada diferentes al mismo nombre. Active o desactive la casilla de verificación según si desea quitar o pasar las columnas duplicadas.

:::image type="content" source="media/data-flow/select-skip-dup.png" alt-text="Omitir duplicados":::

## <a name="ordering-of-columns"></a>Ordenación de columnas

El orden de las asignaciones determina el orden de las columnas de salida. Si una columna de entrada se asigna varias veces, solo se respetará la primera asignación. Si se quita alguna columna duplicada, se conservará la primera coincidencia.

## <a name="data-flow-script"></a>Script de flujo de datos

### <a name="syntax"></a>Sintaxis

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>Ejemplo

A continuación se muestra un ejemplo de asignación Selección con el script de flujo de datos correspondiente:

:::image type="content" source="media/data-flow/select-script-example.png" alt-text="Ejemplo de script de Selección":::

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>Pasos siguientes
* Después de usar Selección para cambiar el nombre de las columnas, reordenarlas y crear alias, use la [transformación del receptor](data-flow-sink.md) para enviar los datos a un almacén de datos.
