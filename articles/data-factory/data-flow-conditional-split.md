---
title: Transformación División condicional en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Divida los datos en diferentes secuencias mediante la transformación División condicional en un flujo de datos de asignación de Azure Data Factory o Synapse Analytics.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 15864e8dfb694478f8156d5122608dec88493285
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060297"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Transformación División condicional en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

La transformación División condicional enruta las filas de datos a diferentes flujos en función de las condiciones coincidentes. La transformación División condicional es similar a una estructura de decisión CASE en un lenguaje de programación. La transformación evalúa las expresiones y, según los resultados, dirige la fila de datos a la secuencia especificada.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4wKCX]

## <a name="configuration"></a>Configuración

El valor **Dividir** determina si la fila de datos fluye hacia el primer flujo coincidente o hacia todos los flujos que coincidan.

Utilice el generador de expresiones de flujo de datos para especificar una expresión como condición de división. Para agregar una nueva condición, haga clic en el icono del signo más de una fila existente. También se puede agregar un flujo predeterminada para las filas que no coinciden con ninguna condición.

:::image type="content" source="media/data-flow/conditionalsplit1.png" alt-text="división condicional":::

## <a name="data-flow-script"></a>Script de flujo de datos

### <a name="syntax"></a>Sintaxis

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Ejemplo

El ejemplo siguiente es una transformación División condicional denominada `SplitByYear` que toma el flujo entrante `CleanData`. Esta transformación tiene dos condiciones de división, `year < 1960` y `year > 1980`. `disjoint` es false porque los datos se dirigen a la primera condición de coincidencia. Cada fila que coincide con la primera condición se dirige al flujo de salida `moviesBefore1960`. Todas las filas restantes que coinciden con la segunda condición se dirigen al flujo de salida `moviesAFter1980`. Todas las demás filas pasan a través del flujo predeterminado `AllOtherMovies`.

En la interfaz de usuario del servicio, esta transformación es similar a la siguiente imagen:

:::image type="content" source="media/data-flow/conditionalsplit1.png" alt-text="división condicional":::

En el siguiente fragmento de código se muestra el script del flujo de datos para esta transformación:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>Pasos siguientes

Las transformaciones de flujo de datos comunes que se usan con la división condicional son la [transformación Combinación](data-flow-join.md), la [transformación Búsqueda](data-flow-lookup.md) y la [transformación Selección](data-flow-select.md)
