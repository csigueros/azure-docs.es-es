---
title: Transformación Filtrar en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Filtre las filas mediante la transformación Filtrar en el flujo de datos de asignación de Azure Data Factory o Synapse Analytics.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3cf00a5ea80278973a548a19e9ef0dc82ef877f
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060202"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transformación Filtrar en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

La transformación Filtrar permiten el filtrado de filas en función de una condición. El flujo de salida incluye todas las filas que coinciden con la condición de filtrado. La transformación Filtrar es similar a una cláusula WHERE de SQL.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xnxN]

## <a name="configuration"></a>Configuración

Utilice el generador de expresiones de flujo de datos para especificar una expresión como condición de filtro. Para abrir el generador de expresiones, haga clic en el cuadro azul. La condición de filtro debe ser de tipo booleano. Para obtener más información sobre cómo crear una expresión, consulte la documentación del [generador de expresiones](concepts-data-flow-expression-builder.md).

:::image type="content" source="media/data-flow/filter1.png" alt-text="Transformación Filtrar":::

## <a name="data-flow-script"></a>Script de flujo de datos

### <a name="syntax"></a>Sintaxis

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se muestra una transformación Filtrar denominada `FilterBefore1960` que toma el flujo entrante `CleanData`. La condición de filtro es la expresión `year <= 1960`.

En la interfaz de usuario, esta transformación es similar a la siguiente imagen:

:::image type="content" source="media/data-flow/filter1.png" alt-text="Transformación Filtrar":::

En el siguiente fragmento de código se muestra el script del flujo de datos para esta transformación:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>Pasos siguientes

Filtre columnas con la [transformación Selección](data-flow-select.md)
