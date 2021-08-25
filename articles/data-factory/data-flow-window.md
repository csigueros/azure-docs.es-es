---
title: Transformación Ventana en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Transformación Ventana de flujo de datos de asignación de Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 11/16/2020
ms.openlocfilehash: 8331d3908e484f1b82a4aa73cd6d498dac2a0ad3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122638351"
---
# <a name="window-transformation-in-mapping-data-flow"></a>Transformación Ventana en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En la transformación Ventana se definen las agregaciones basadas en ventanas de las columnas de las secuencias de datos. En el Generador de expresiones, puede definir diferentes tipos de agregaciones basadas en ventanas de datos o tiempo (cláusula OVER de SQL), como LEAD, LAG, NTILE, CUMEDIST, RANK, etc.). Se generará un nuevo campo en la salida que incluya estas agregaciones. También puede incluir campos de agrupación opcionales.

![Captura de pantalla que muestra las ventanas seleccionadas en el menú.](media/data-flow/windows1.png "ventanas 1")

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>Over
Establezca las particiones de los datos de columna para la transformación Ventana. El equivalente en SQL es ```Partition By``` en la cláusula Over de SQL. Si desea crear un cálculo o una expresión para usarlos en la creación de las particiones, desplace el cursor por encima del nombre de la columna y seleccione "Columna calculada".

![Captura de pantalla que muestra la configuración de ventanas con la pestaña Over seleccionada.](media/data-flow/windows4.png "ventanas 4")

## <a name="sort"></a>Sort
Otra parte de la cláusula Over es establecer ```Order By```. Esto establecerá el orden de los datos. También puede crear una expresión que genere un valor de cálculo en este campo de columna para ordenar.

![Captura de pantalla que muestra la configuración de ventanas con la pestaña Sort seleccionada.](media/data-flow/windows5.png "ventanas 5")

## <a name="range-by"></a>Range By
A continuación, enlace o desenlace el marco de la ventana. Para establecer un marco de ventana sin enlazar, establezca el control deslizante en Unbounded (Sin enlazar) en ambos extremos. Si elige un valor entre Unbounded (Sin enlazar) y Current Row (Fila actual), debe establecer los valores de desplazamiento inicial y final. Ambos valores serán números enteros positivos. Puede usar números relativos o valores de sus datos.

El control deslizante de la ventana puede establecer dos valores: valores antes de la fila actual y valores después de la fila actual. Los desplazamientos inicial y final coinciden con los dos selectores del control deslizante.

![Captura de pantalla que muestra la configuración de ventanas con la pestaña Range by seleccionada.](media/data-flow/windows6.png "ventanas 6")

## <a name="window-columns"></a>Columnas de Ventana
Por último, use el Generador de expresiones para definir las agregaciones que desea utilizar con las ventanas de datos, por ejemplo, RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG, etc.

![Captura de pantalla que muestra el resultado de la acción de ventanas.](media/data-flow/windows7.png "ventanas 7")

Para ver la lista completa de las funciones de agregación y análisis disponibles para usar en el lenguaje de expresiones del Generador de expresiones de ADF Data Flow, consulte: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Pasos siguientes

Si busca una agregación de agrupación simple, use la [Transformación Agregar](data-flow-aggregate.md).
