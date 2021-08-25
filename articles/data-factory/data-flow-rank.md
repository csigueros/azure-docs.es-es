---
title: Transformación de clasificación en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Cómo usar la transformación de clasificación del flujo de datos de asignación de Azure Data Factory para generar una columna de clasificación
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 10/05/2020
ms.openlocfilehash: 6577425827668f0eaddb19b276e940fff7dafa1a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638888"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Transformación de clasificación en el flujo de datos de asignación 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Use la transformación de clasificación para generar una clasificación ordenada en función de las condiciones de ordenación especificadas por el usuario. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Configuración

![Configuración de clasificación](media/data-flow/rank-configuration.png "Configuración de clasificación")

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

![Configuración de clasificación](media/data-flow/rank-configuration.png "Configuración de clasificación")

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
