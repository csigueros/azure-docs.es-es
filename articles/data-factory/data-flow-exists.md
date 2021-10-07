---
title: Transformación Existe en flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Busque filas existentes mediante la transformación Existe en el flujo de datos de asignación de Azure Data Factory y Synapse Analytics
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 747a873e64bd143d988173f236f27d7246f6deeb
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058741"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Transformación Existe en flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

La transformación Existe es una transformación de filtrado de filas que comprueba si los datos existen en otro origen o flujo. El flujo de salida incluye todas las filas del flujo izquierdo que, o bien existen, o bien no existen en el flujo derecho. La transformación Existe es similar a ```SQL WHERE EXISTS``` y ```SQL WHERE NOT EXISTS```.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>Configuración

1. Elija en la lista desplegable **Right stream** (Flujo derecho) el flujo de datos donde va a comprobar la existencia.
1. Especifique si busca que los datos existan o no existan en el valor **Exist type** (Tipo de Existe).
1. Seleccione si quiere o no una **expresión personalizada**.
1. Elija las columnas de clave que desea comparar como condiciones de existencia. De forma predeterminada, el flujo de datos busca la igualdad entre una columna de cada flujo. Para comparar a través de un valor de proceso, mantenga el mouse sobre la lista desplegable y seleccione **Columna calculada**.

:::image type="content" source="media/data-flow/exists.png" alt-text="Configuración de Existe":::

### <a name="multiple-exists-conditions"></a>Varias condiciones Existe

Para comparar varias columnas de cada flujo, agregue una nueva condición de existencia haciendo clic en el icono de signo más situado junto a una fila existente. Cada condición adicional se combina con una instrucción "y". La comparación de dos columnas equivale a la expresión siguiente:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Expresión personalizada

Para crear una expresión de forma libre que contenga operadores distintos de "y" e "igual a", seleccione el campo **Custom expression** (Expresión personalizada). Escriba una expresión personalizada mediante el generador de expresiones de flujo de datos haciendo clic en el cuadro azul.

:::image type="content" source="media/data-flow/exists1.png" alt-text="Configuración personalizada de Existe":::

## <a name="broadcast-optimization"></a>Optimización de difusión

:::image type="content" source="media/data-flow/broadcast.png" alt-text="Combinación de difusión":::

En las combinaciones, búsquedas y transformaciones Existe, si uno o ambos flujos de datos caben en la memoria del nodo de trabajo, puede optimizar el rendimiento al habilitar la opción **Difusión**. De forma predeterminada, el motor de Spark decidirá automáticamente si difundir o no una parte. Para elegir manualmente la parte que se va a difundir, seleccione **Fijo**.

No se recomienda deshabilitar la difusión a través de la opción **Desactivado** a menos que las combinaciones experimenten errores de tiempo de espera.

## <a name="data-flow-script"></a>Script de flujo de datos

### <a name="syntax"></a>Sintaxis

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Ejemplo

El ejemplo siguiente es una transformación Existe denominada `checkForChanges` que toma el flujo izquierdo `NameNorm2` y el flujo derecho `TypeConversions`.  La condición Existe es la expresión `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` que devuelve true si coinciden las columnas `EMPID` y `Region` de cada flujo. Mientras se comprueba la existencia, `negate` es false. No se habilita ninguna difusión en la pestaña Optimize (Optimizar), por lo que `broadcast` tiene el valor `'none'`.

En la experiencia de la interfaz de usuario, esta transformación es similar a la siguiente imagen:

:::image type="content" source="media/data-flow/exists-script.png" alt-text="Ejemplo de Existe":::

En el siguiente fragmento de código se muestra el script del flujo de datos para esta transformación:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Pasos siguientes

Otras transformaciones similares son [Búsqueda](data-flow-lookup.md) y [Combinación](data-flow-join.md).
