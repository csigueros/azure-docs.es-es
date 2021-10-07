---
title: Transformación Ventana en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo usar la transformación Ventana en el flujo de datos de asignación de canalizaciones de Azure Data Factory y Synapse Analytics.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: a913e7a7c5e3ce24df649cb0f1a670650992ac52
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059670"
---
# <a name="window-transformation-in-mapping-data-flow"></a>Transformación Ventana en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

En la transformación Ventana se definen las agregaciones basadas en ventanas de las columnas de las secuencias de datos. En el Generador de expresiones, puede definir diferentes tipos de agregaciones basadas en ventanas de datos o tiempo (cláusula OVER de SQL), como LEAD, LAG, NTILE, CUMEDIST, RANK, etc.). Se generará un nuevo campo en la salida que incluya estas agregaciones. También puede incluir campos de agrupación opcionales.

:::image type="content" source="media/data-flow/windows1.png" alt-text="Captura de pantalla que muestra las ventanas seleccionadas en el menú.":::

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>Over
Establezca las particiones de los datos de columna para la transformación Ventana. El equivalente en SQL es ```Partition By``` en la cláusula Over de SQL. Si desea crear un cálculo o una expresión para usarlos en la creación de las particiones, desplace el cursor por encima del nombre de la columna y seleccione "Columna calculada".

:::image type="content" source="media/data-flow/windows4.png" alt-text="Captura de pantalla que muestra la configuración de ventanas con la pestaña Over seleccionada.":::

## <a name="sort"></a>Sort
Otra parte de la cláusula Over es establecer ```Order By```. Esto establecerá el orden de los datos. También puede crear una expresión que genere un valor de cálculo en este campo de columna para ordenar.

:::image type="content" source="media/data-flow/windows5.png" alt-text="Captura de pantalla que muestra la configuración de ventanas con la pestaña Sort seleccionada.":::

## <a name="range-by"></a>Range By
A continuación, enlace o desenlace el marco de la ventana. Para establecer un marco de ventana sin enlazar, establezca el control deslizante en Unbounded (Sin enlazar) en ambos extremos. Si elige un valor entre Unbounded (Sin enlazar) y Current Row (Fila actual), debe establecer los valores de desplazamiento inicial y final. Ambos valores serán números enteros positivos. Puede usar números relativos o valores de sus datos.

El control deslizante de la ventana puede establecer dos valores: valores antes de la fila actual y valores después de la fila actual. Los desplazamientos inicial y final coinciden con los dos selectores del control deslizante.

:::image type="content" source="media/data-flow/windows6.png" alt-text="Captura de pantalla que muestra la configuración de ventanas con la pestaña Range by seleccionada.":::

## <a name="window-columns"></a>Columnas de Ventana
Por último, use el Generador de expresiones para definir las agregaciones que desea utilizar con las ventanas de datos, por ejemplo, RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG, etc.

:::image type="content" source="media/data-flow/windows7.png" alt-text="Captura de pantalla que muestra el resultado de la acción de ventanas.":::

La lista completa de las funciones analíticas y de agregación disponibles para usar en el lenguaje de expresiones de un flujo de datos mediante el Generador de expresiones se incluye en [Expresiones de transformación de datos en flujos de datos de asignación](data-flow-expression-functions.md).

## <a name="next-steps"></a>Pasos siguientes

Si busca una agregación de agrupación simple, use la [Transformación Agregar](data-flow-aggregate.md).
