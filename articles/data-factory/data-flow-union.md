---
title: Transformación Unión en el flujo de datos de asignación
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre la transformación Nueva rama del flujo de datos de asignación en Azure Data Factory y Synapse Analytics.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 14cf10c51ffe470ff249ff4953f0edd8990aab1b
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059689"
---
# <a name="union-transformation-in-mapping-data-flow"></a>Transformación Unión en el flujo de datos de asignación

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

Unión combina varias secuencias de datos en una, y la unión SQL de esas secuencias será la salida de la transformación Unión. Todo el esquema de cada secuencia de entrada se combinará en su flujo de datos, sin necesidad de tener una clave de combinación.

Puede combinar un número n de secuencias en la tabla de configuración. Para ello, seleccione el icono "+" junto a cada fila configurada, incluidos los datos de origen y las secuencias de transformaciones existentes en el flujo de datos.

A continuación se muestra un breve vídeo paso a paso sobre la transformación Unión en el flujo de datos de asignación:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vngz]

:::image type="content" source="media/data-flow/union.png" alt-text="Transformación Unión":::

En este caso, puede combinar los metadatos dispares de varios orígenes (en este ejemplo, tres archivos de origen diferentes) y combinarlos en una única secuencia:

:::image type="content" source="media/data-flow/union111.png" alt-text="Información general de la transformación Unión":::

Para lograrlo, agregue filas adicionales en la configuración de Unión mediante la inclusión de todos los orígenes que quiere agregar. No se quiere ninguna clave común de búsqueda o unión:

:::image type="content" source="media/data-flow/unionsettings.png" alt-text="Configuración de la transformación Unión":::

Si establece una transformación Selección después de Unión, podrá cambiar el nombre de los campos superpuestos o los campos cuyos nombres no se han asignado desde orígenes sin encabezado. Haga clic en "Inspeccionar" para ver los metadatos combinados con un total de 132 columnas en este ejemplo de tres orígenes diferentes:

:::image type="content" source="media/data-flow/union333.png" alt-text="Transformación Unión final":::

## <a name="name-and-position"></a>Nombre y posición

Al elegir la "unión por nombre", cada valor de columna se colocará en la columna correspondiente de cada origen, con un nuevo esquema de metadatos concatenados.

Si elige la "unión por posición", cada valor de columna se colocará en la posición original de cada origen correspondiente, lo que dará lugar a un nuevo flujo de datos combinado, donde los datos de cada origen se agregan a la misma secuencia:

:::image type="content" source="media/data-flow/unionoutput.png" alt-text="Salida de unión":::

## <a name="next-steps"></a>Pasos siguientes

Explorar transformaciones similares, como [Combinación](data-flow-join.md) y [Existe](data-flow-exists.md).
