---
title: Transformación Ordenar en el flujo de datos de asignación
description: Obtenga información sobre la transformación Ordenar en el flujo de datos de asignación de canalizaciones de Azure Data Factory y Synapse Analytics.
titleSuffix: Azure Data Factory & Azure Synapse
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 8781836be58403dfef130f55fa698b281360e641
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059898"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Transformación Ordenar en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

La transformación Ordenar permite ordenar las filas entrantes de la secuencia de datos actual. Puede elegir columnas individuales y ordenarlas en orden ascendente o descendente.

> [!NOTE]
> Los flujos de datos de asignación se ejecutan en clústeres Spark con datos distribuidos en varios nodos y particiones. Si decide volver a particionar los datos en una transformación posterior, puede perder la ordenación porque los datos se vuelven a mezclar. La mejor manera de mantener el criterio de ordenación en el flujo de datos es establecer una sola partición en la pestaña Optimizar de la transformación y mantener la transformación Ordenar lo más parecida posible al receptor.

## <a name="configuration"></a>Configuración

:::image type="content" source="media/data-flow/sort.png" alt-text="Configuración de ordenación":::

**No distinguir entre mayúsculas y minúsculas:** si desea tener en cuenta la distinción entre mayúsculas y minúsculas al ordenar campos de cadena o texto.

**Ordenar solo dentro de las particiones:** a medida que los flujos de datos se ejecutan en Spark, cada flujo de datos se divide en particiones. Esta configuración ordena datos solo dentro de particiones entrantes, en lugar de ordenar todo el flujo de datos. 

**Condiciones de ordenación:** elija las columnas por las que va a ordenar y en qué orden se realiza la ordenación. El orden determina la prioridad de ordenación. Elija si se van a mostrar o no los valores NULL al principio o al final del flujo de datos.

### <a name="computed-columns"></a>Columnas calculadas

Para modificar o extraer un valor de columna antes de aplicar la ordenación, mantenga el ratón sobre la columna y seleccione "Columna calculada". Se abrirá el generador de expresiones para crear una expresión para la operación de ordenación en lugar de utilizar un valor de columna.

## <a name="data-flow-script"></a>Script de flujo de datos

### <a name="syntax"></a>Sintaxis

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Ejemplo

:::image type="content" source="media/data-flow/sort.png" alt-text="Configuración de ordenación":::

El script de flujo de datos para la configuración de ordenación anterior se encuentra en el siguiente fragmento de código.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Pasos siguientes

Después de la ordenación, es posible que quiera usar la [transformación Agregar](data-flow-aggregate.md).
