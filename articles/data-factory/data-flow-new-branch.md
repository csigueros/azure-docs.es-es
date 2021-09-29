---
title: Varias ramas en un flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Replicación de flujos de datos en el flujo de datos de asignación con varias ramas
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 05aa62c32054f7a5e9ab75367f3d07c6f9223506
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060031"
---
# <a name="creating-a-new-branch-in-mapping-data-flow"></a>Creación de una nueva rama en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Agregue una nueva rama para realizar varios conjuntos de operaciones y transformaciones en la misma secuencia de datos. La incorporación de una nueva rama resulta útil si quiere utilizar el mismo origen para varios receptores o para autocombinar datos.

Se puede agregar una nueva rana desde la lista de transformación del mismo modo en que se agregan otras transformaciones. La opción **Nueva rama** solo estará disponible como acción cuando exista una transformación después de la transformación que está intentando ramificar.

:::image type="content" source="media/data-flow/new-branch2.png" alt-text="Captura de pantalla que muestra la opción New branch (Nueva rama) en el menú Multiple inputs / outputs (Varias entradas y salidas).":::

En el ejemplo siguiente, el flujo de datos está leyendo los datos de carreras de taxi. Es obligatorio agregar los resultados por día y por proveedor. En lugar de crear dos flujos de datos independientes que leen del mismo origen, se puede agregar una nueva rama. De este modo, ambas agregaciones se pueden ejecutar como parte del mismo flujo de datos. 

:::image type="content" source="media/data-flow/new-branch.png" alt-text="Captura de pantalla que muestra el flujo de datos con dos ramas desde el origen.":::

> [!NOTE]
> Al hacer clic en el signo más (+) para agregar transformaciones al gráfico, solo verá la opción Nueva rama cuando haya bloques de transformación posteriores. Esto se debe a que Nueva rama crea una referencia a la secuencia existente y requiere un procesamiento ascendente adicional para funcionar. Si no ve la opción Nueva rama, agregue primero una columna derivada u otra transformación y, a continuación, vuelva al bloque anterior y verá Nueva rama como opción.

## <a name="next-steps"></a>Pasos siguientes

Después de la bifurcación, es posible que desee utilizar las [transformaciones de flujos de datos](data-flow-transformation-overview.md).
