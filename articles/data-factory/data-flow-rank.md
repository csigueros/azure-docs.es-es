---
title: Transformación de clasificación en el flujo de datos de asignación
description: Aprenda a usar una transformación de clasificación de flujo de datos de asignación para generar una columna de clasificación en canalizaciones de Azure Data Factory o Synapse Analytics.
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: c3af0355a02badb5aaa5d5b13a51b74392c931d8
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059892"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Transformación de clasificación en el flujo de datos de asignación 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Use la transformación de clasificación para generar una clasificación ordenada en función de las condiciones de ordenación especificadas por el usuario. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Configuración

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="Configuración de clasificación":::

**No distinguir entre mayúsculas y minúsculas:** si una columna de ordenación es de tipo cadena, las mayúsculas o minúsculas se factorizarán en la clasificación. 

**Denso:** si se habilita, la columna de clasificación tendrá una clasificación densa. Cada recuento de clasificación será un número consecutivo y los valores de clasificación no se omitirán después de una vinculación.

**Rank column (Columna de clasificación):** nombre de la columna de clasificación generada. Esta columna será de tipo Long.

**Condiciones de ordenación:** elija las columnas por las que va a ordenar y en qué orden se realiza la ordenación. El orden determina la prioridad de ordenación.

La configuración anterior toma los datos de baloncesto entrantes y crea una columna de clasificación denominada "pointsRanking". La fila con el valor más alto de la columna *PTS* tendrá un valor de *pointsRanking* de 1.

## <a name="data-flow-script"></a>Script de flujo de datos

### <a name="syntax"></a>Sintaxis

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Ejemplo

:::image type="content" source="media/data-flow/rank-configuration.png" alt-text="Configuración de clasificación":::

El script del flujo de datos para la configuración de clasificación anterior se encuentra en el siguiente fragmento de código.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>Pasos siguientes

Filtre las filas en función de los valores de clasificación mediante la [transformación de filtro](data-flow-filter.md).
